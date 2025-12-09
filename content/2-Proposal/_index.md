---
title: "Proposal"
date: 2025-09-09
weight: 2
chapter: false
pre: " <b> 2. </b> "
---
# "Leaf" Clothing E-commerce Website
## Using AWS Services Integration to Optimize the System (S3, CloudWatch, etc.)

## 1. Executive Summary
Leaf is an e-commerce platform specializing in fashion products for men and women, including fashion accessories such as jewelry, shoes, hats, and leather straps. The website integrates AWS services to optimize costs and enhance the user experience.

## 2. Problem Statement
**Problem:**  
A clothing store needs its own e-commerce channel to optimize the user experience.  

**Solution:**  
Create a dedicated e-commerce website for the store using AWS services to optimize cost, time, and user experience.

## 3. Solution Architecture
The platform applies an **AWS Serverless architecture** to manage data.  
![](https://paperkite-master.github.io/AWS_FCJ/images/2-Proposal/AWS1.drawio.png)

### Components and Roles in the AWS Architecture

#### A. User Interface Layer

| Service | Role | Detailed Description |
|---------|------|--------------------|
| **AWS Amplify** | Website deployment | Hosts **static websites** (React, Vue, Next.js) and **automatically builds/deploys** when code is pushed to GitHub. |
| **Amazon CloudFront (CDN)** | Improve page loading speed | Caches static content (images, CSS, JS) close to users to **reduce latency and bandwidth usage**. |
| **Amazon S3** | Store static files & product images | Acts as a **content repository** for images, banners, CSS/JS files. |

---

#### B. Application Logic Layer

| Service | Role | Detailed Description |
|---------|------|--------------------|
| **Amazon API Gateway** | API gateway | Receives requests from frontend and forwards them to Lambda functions for processing. |
| **AWS Lambda** | Server-side logic | Handles order processing, payments, authentication, email sending, **without dedicated servers**. |
| **Amazon DynamoDB** | NoSQL database | Stores **products, accounts, orders, shopping carts**, offering high speed and automatic scaling. |
| **Amazon OpenSearch Service** | Product search | Allows users to **quickly search products by keywords, color, price, etc.** |
| **Amazon EventBridge** | Event handling | Automatically triggers events (e.g., new order → send email, update stock). |
| **AWS Secrets Manager** | Secure sensitive data | Stores **API keys, payment tokens, database passwords** to protect data. |

---

#### C. User Management & Security Layer

| Service | Role | Detailed Description |
|---------|------|--------------------|
| **Amazon Cognito** | User authentication & management | Supports **signup, login, password reset, MFA** without building your own auth system. |
| **AWS WAF (Web Application Firewall)** | Web protection | Protects against **SQL Injection, XSS, DDoS**, and other malicious access. |
| **Amazon Route 53** | DNS & domain | Manages domain names. |

---

#### D. Notification & Communication Layer

| Service | Role | Detailed Description |
|---------|------|--------------------|
| **Amazon SNS (Simple Notification Service)** | System notifications | Sends notifications to admins or users (via email, SMS, or push notifications). |
| **Amazon SES (Simple Email Service)** | Transactional emails | Sends order confirmations, promotions, password reset emails, etc. |

---

#### E. AI & Machine Learning Layer

| Service | Role | Detailed Description |
|---------|------|--------------------|
| **Amazon Translate** | Content translation | Translates product descriptions to other languages for international customers. |
| **Amazon Bedrock** | AI content generation | Creates **chatbots for shopping assistance**. |

---

#### F. Monitoring & Management Layer

| Service | Role | Detailed Description |
|---------|------|--------------------|
| **Amazon CloudWatch** | System monitoring | Monitors logs, performance, alerts for errors or cost spikes. |
| **AWS CloudTrail** | Administrative logging | Tracks configuration changes (who changed what, and when) for auditing purposes. |

---

## 4. Technical Implementation
**Implementation Stages:**
1. Collect system requirements and features  
2. Estimate cost and check feasibility  
3. Design UI prototypes using Figma  
4. Build database schema  
5. Develop frontend interface  
6. Build API, backend, integrate AWS services  
7. Test, deploy, and finalize the project  

## 5. Timeline & Milestones
- **Month 1:** Learn AWS  
- **Month 2:** Design and implement the project  
- **Month 3:** Deployment and testing  

## 6. Budget Estimate
Check cost here: [AWS Pricing Calculator](https://calculator.aws/#/estimate?id=dd9c5b1bea2e5345e329d856d546299534bfd8d0)

---

## Storage & Data Services

| Service | Function | Estimated Usage | Cost/Month (USD) | Notes |
|---------|---------|----------------|-----------------|-------|
| **Amazon S3** | Store images, CSS, JS | 10 GB storage, ~5k GET, ~500 PUT | **0.35** | Low data, low traffic |
| **DynamoDB** | Store orders & carts | ~1 GB data, 100k read/write | **0.20** | On-demand mode |
| **OpenSearch Service** | Product search | 1 small instance, 10% uptime | **3.00** | Reduced configuration due to small dataset |

---

## Backend & Logic Processing

| Service | Function | Estimated Usage | Cost/Month (USD) | Notes |
|---------|---------|----------------|-----------------|-------|
| **AWS Lambda** | API processing, payments | 100k requests, 128MB, 100ms | **0.20** | Very cheap due to serverless |
| **API Gateway** | API access | 100k requests | **0.10** | Directly connected to Lambda |
| **Secrets Manager** | Secure API keys, DB | 1 secret | **0.40** | Maintained |
| **EventBridge** | Internal event triggers | 1k events/month | **0.05** | Lightweight for notifications/orders |

---

## User Interface, Authentication & Security

| Service | Function | Estimated Usage | Cost/Month (USD) | Notes |
|---------|---------|----------------|-----------------|-------|
| **Amplify Hosting** | Frontend deployment | 10 GB, 3 builds/month | **1.50** | CI/CD + static hosting |
| **CloudFront (CDN)** | Content delivery | 5 GB out | **0.20** | Reduce CDN cost |
| **WAF (Web Firewall)** | Web protection | 1 ACL | **5.00** | Basic security required |
| **Cognito** | User authentication | 100 MAU | **1.00** | Reduced from $5 baseline |
| **Route 53** | Domain DNS | 1 hosted zone | **0.50** | Unchanged |

---

## Email & Notifications

| Service | Function | Estimated Usage | Cost/Month (USD) | Notes |
|---------|---------|----------------|-----------------|-------|
| **SES (Email)** | Order confirmation emails | 1,000 emails/month | **0.15** | $0.0001 per email |
| **SNS (Notifications)** | HTTP/email notifications | 1k messages | **0.05** | Used for internal notifications |

---

## AI & Machine Learning (Optional)

| Service | Function | Estimated Usage | Cost/Month (USD) | Notes |
|---------|---------|----------------|-----------------|-------|
| **Translate** | Product translation EN↔VI | 10k characters | **0.15** | Support international customers |
| **Bedrock** | Generate product descriptions | 100 small requests | **0.10** | Can be disabled if not needed |

---

## Monitoring & Logging

| Service | Function | Estimated Usage | Cost/Month (USD) | Notes |
|---------|---------|----------------|-----------------|-------|
| **CloudWatch** | Logs, metrics monitoring | 1–2 GB log | **1.50** | Reduced from $9 |
| **CloudTrail** | Activity auditing | Default usage | **0.00** | Free tier sufficient |

---

## Total Estimated Monthly Cost

| Service Group | Total Cost (USD/month) |
|---------------|------------------------|
| Storage & Data | 3.55 |
| Backend & Processing | 0.75 |
| UI & Security | 8.20 |
| Email & Notifications | 0.20 |
| AI & ML (Optional) | 0.25 |
| Monitoring & Logs | 1.50 |
| **Total (Actual)** | **≈ 14.45 USD / month (~375,000 VND)** |

## 7. Expected Outcomes
- The website runs with low latency and smooth image display.
