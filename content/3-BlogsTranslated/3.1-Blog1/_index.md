---
title: "Blog 1"
date: 2025-09-09
weight: 1
chapter: false
pre: " <b> 3.1. </b> "
---
{{% notice warning %}}
⚠️ **Note:** The information below is for reference only. Please **do not copy verbatim** for your report, including this warning.
{{% /notice %}}

# Getting Started with Healthcare Data Lakes: Using Microservices

Data lakes can help hospitals and healthcare facilities turn data into business insights and maintain business continuity while protecting patient privacy. A **data lake** is a centralized, managed, and secure repository that stores all your data—both raw and processed—for analysis. A data lake lets you break down data silos and combine different analytics approaches to gain insights and make better business decisions.

This blog post is part of a broader series on getting started with healthcare data lakes. In my final post of the series, *“Getting Started with Healthcare Data Lakes: Diving into Amazon Cognito,”* I focused on using Amazon Cognito and Attribute Based Access Control (ABAC) to authenticate and authorize users in the healthcare data lake solution. In this post, I outline how the solution evolved at a foundational level, including the design decisions I made and the additional features used. You can find code samples for the solution in this Git repo for reference.

---

## Architecture Guidance

The biggest change since the last walkthrough of the overall architecture is splitting a single service into a set of smaller services to improve maintainability and flexibility. Ingesting a large volume of heterogeneous healthcare data often requires specialized connectors for each format; by encapsulating them as separate microservices, we can add, remove, or modify each connector without affecting the others. The microservices are loosely coupled through publish/subscribe messaging centered on what I call the “pub/sub hub.”

This solution is another reasonable sprint iteration from my previous post. The scope remains limited to ingesting and basic parsing of **HL7v2 messages** formatted in **Encoding Rules 7 (ER7)** through a REST interface.

**The solution architecture is now as follows:**

> *Figure 1. Overall architecture; colored boxes represent distinct services.*

---

While the term *microservices* has some inherent ambiguity, several traits are common:  
- Small, autonomous, loosely coupled  
- Reusable, communicating through well-defined interfaces  
- Specialized to do one thing well  
- Often implemented in an **event-driven architecture**

When deciding where to draw boundaries between microservices, consider:  
- **Intrinsic**: technology used, performance, reliability, scalability  
- **Extrinsic**: dependent functionality, rate of change, reusability  
- **Human**: team ownership, managing *cognitive load*

---

## Technology Choices and Communication Scope

| Communication scope                       | Technologies / patterns to consider                                                        |
| ----------------------------------------- | ------------------------------------------------------------------------------------------ |
| Within a single microservice              | Amazon Simple Queue Service (Amazon SQS), AWS Step Functions                               |
| Between microservices in a single service | AWS CloudFormation cross-stack references, Amazon Simple Notification Service (Amazon SNS) |
| Between services                          | Amazon EventBridge, AWS Cloud Map, Amazon API Gateway                                      |

---

## The Pub/Sub Hub

A **hub-and-spoke** (message broker) architecture works well with a small set of closely related microservices.  
- Each microservice depends only on the *hub*  
- Inter-microservice connections are limited to the published message contents  
- Fewer synchronous calls because pub/sub is a one-way asynchronous *push*

Drawback: you need **coordination and monitoring** to avoid microservices processing the wrong message.

---

## Core Microservice

Provides the foundational data and communication layer, including:  
- **Amazon S3** bucket for data  
- **Amazon DynamoDB** for the data catalog  
- **AWS Lambda** to write messages into the data lake and catalog  
- **Amazon SNS** topic as the *hub*  
- **Amazon S3** bucket for artifacts such as Lambda code

> Only allow indirect write access to the data lake through a Lambda function → ensures consistency.

---

## Front Door Microservice

- Provides an API Gateway for external REST interactions  
- Authentication and authorization based on **OIDC** via **Amazon Cognito**  
- Self-managed *deduplication* using DynamoDB instead of SNS FIFO because:  
  1. SNS deduplication TTL is only 5 minutes  
  2. SNS FIFO requires SQS FIFO  
  3. You can proactively notify the sender that the message is a duplicate  

---

## Staging ER7 Microservice

- Lambda “trigger” subscribed to the pub/sub hub, filtering messages by attribute  
- Step Functions Express Workflow to convert ER7 → JSON  
- Two Lambdas:  
  1. Fix ER7 formatting (newline, carriage return)  
  2. Parsing logic  
- Results or errors are pushed back into the pub/sub hub  

---

## New Features in the Solution

### 1. AWS CloudFormation Cross-Stack References
Example *outputs* in the core microservice:
```yaml
Outputs:
  Bucket:
    Value: !Ref Bucket
    Export:
      Name: !Sub ${AWS::StackName}-Bucket
  ArtifactBucket:
    Value: !Ref ArtifactBucket
    Export:
      Name: !Sub ${AWS::StackName}-ArtifactBucket
  Topic:
    Value: !Ref Topic
    Export:
      Name: !Sub ${AWS::StackName}-Topic
  Catalog:
    Value: !Ref Catalog
    Export:
      Name: !Sub ${AWS::StackName}-Catalog
  CatalogArn:
    Value: !GetAtt Catalog.Arn
    Export:
      Name: !Sub ${AWS::StackName}-CatalogArn
```

