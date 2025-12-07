---
title : "Overview"
date: 2025-09-09 
weight : 1
chapter : false
pre : " <b> 5.1. </b> "
---

## **Workshop Overview**
In this workshop, you will build a complete AI Chatbot using serverless architecture on AWS. The chatbot uses Claude Haiku 4.5 deployed entirely serverless with low costs and automatic scaling capabilities.

### Modules
**Module 1**: Setup Amazon Bedrock
**Module 2**: Create Lambda Function
**Module 3**: Configure API Gateway
**Module 4**: Deploy Frontend to S3
**Module 5**: Testing & Debugging

### Architecture Overview
![alt text](https://paperkite-master.github.io/AWS_FCJ/images/5-Workshop/5.1-Workshop-overview/image.png)

---

## **AWS Services & Their Roles in the Workshop**

### 1. **Amazon Bedrock — AI Brain**
- Process and generate responses from user messages.
- Uses **Claude Haiku 4.5** (Messages API).
- Features:
  - Inference Profiles
  - Conversation history management
  - Temperature control

---

### 2. **AWS Lambda — Backend Logic**
Role:
- Receive requests from API Gateway
- Validate input, format prompt
- Call Bedrock and return response

Features:
- Node.js 24 runtime  
- Environment variables (MODEL_ID, ALLOWED_ORIGIN)
- CloudWatch Logs
- Input validation + error handling
- Retry logic  
- CORS handling

---

### 3. **Amazon API Gateway — API Endpoint**
Role:
- Public REST API for browsers
- CORS management
- Route POST `/chat` to Lambda
- Throttling to prevent abuse

Features:
- REST API or HTTP API  
- CORS configuration  
- Lambda Proxy Integration  
- OPTIONS method for CORS preflight  

---

### 4. **Amazon S3 — Frontend Hosting**
- Static Website Hosting
- Host HTML/CSS/JS
- Allow users to access chatbot via browser

---

### 5. **Amazon CloudWatch — Monitoring & Debugging**
- Collect logs from Lambda
- Debug request/response & errors

---

## Workshop Learning Objectives

After the workshop, you will:

- Understand serverless architecture and how services interact
- Deploy a production-ready chatbot with Claude Haiku 4.5
- Integrate Bedrock API with Lambda 
- Configure API Gateway 
- Host static website on S3
- Debug using CloudWatch Logs
- Optimize serverless costs

---