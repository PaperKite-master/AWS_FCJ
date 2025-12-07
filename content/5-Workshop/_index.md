---
title: "Workshop"
date: 2025-09-09
weight: 5
chapter: false
pre: " <b> 5. </b> "
---

## Overview
Amazon Bedrock provides the ability to integrate leading foundation models (LLMs) from Anthropic, Meta, AI21 Labs, and other providers through a simple API, allowing you to build AI applications without managing complex infrastructure.

In this workshop, we will learn how to build, deploy, and test a complete AI Chatbot using a serverless architecture, enabling users to interact with Claude Haiku 4.5 without having to manage servers or worry about scaling.

We will create a system with **five main components** to build the chatbot: Amazon Bedrock (AI engine), AWS Lambda (backend logic), API Gateway (REST API endpoint), Amazon S3 (frontend hosting), and CloudWatch (monitoring). These components deliver a fully serverless architecture with low cost and automatic scaling.

##### Main Components:
**Amazon Bedrock (AI Engine)** – Provides the Claude Haiku 4.5 model through a simple API. You call the InvokeModel API to send messages and receive intelligent AI responses.

**AWS Lambda (Backend Logic)** – Runs Node.js 24 code to process requests from API Gateway. Lambda validates input (message length, history limits), formats prompts for Bedrock, calls the Bedrock API, and handles errors.

**API Gateway (REST API Endpoint)** – Creates a public HTTPS endpoint (POST /chat) for the frontend to call. API Gateway handles CORS configuration, routes requests to Lambda, and provides throttling to protect the backend from abuse.

**Amazon S3 (Frontend Hosting)** – Hosts a static website (HTML) for the chatbot UI. Users access the chatbot through the browser; the interface sends messages to API Gateway and displays AI responses.

**CloudWatch (Monitoring & Debugging)** – Automatically collects logs from Lambda execution, allowing you to debug errors.

### Architecture Overview
User Browser → S3 (Static Website) → API Gateway → Lambda → Bedrock (Claude) → CloudWatch

Flow:

- User enters a message in the chatbot UI (hosted on S3)
- JavaScript sends a POST request to the API Gateway endpoint
- API Gateway invokes the Lambda function
- Lambda validates input, formats the prompt, and calls the Bedrock InvokeModel API
- Bedrock processes it with Claude Haiku 4.5 and returns the AI response
- Lambda returns the response back to API Gateway → Browser  
- CloudWatch logs the entire process

## Workshop Modules
[**Module 1**: Setup Amazon Bedrock](5.3-Setup-Amazon-Bedrock/)  
• Enable Claude Haiku 4.5 model access  
• Understand inference profiles  
• Test the model via AWS Console  

[**Module 2**: Create Lambda Function](5.4-Setup_Lambda/)  
• Create a Node.js 24 Lambda function  
• Deploy chatbot backend code  
• Configure IAM role with Bedrock permissions  
• Set environment variables  

[**Module 3**: Configure API Gateway](5.5-Setup-API-gateway/)  
• Create REST API  
• Configure POST /chat endpoint  
• Enable CORS  
• Test API with Postman  

[**Module 4**: Deploy Frontend to S3](5.7-Setup-S3/)  
• Create S3 bucket  
• Enable static website hosting  
• Upload HTML chatbot UI  
• Configure public access  

[**Module 5**: Testing & Debugging](5.6-Monitoring-CloudWatch/)  
• Test end-to-end flow  
• View CloudWatch logs  
