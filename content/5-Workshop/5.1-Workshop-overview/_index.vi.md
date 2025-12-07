---
title : "Tổng quan"
date: 2025-09-09 
weight : 1
chapter : false
pre : " <b> 5.1. </b> "
---

 ## **Workshop overview**
Trong workshop này, bạn sẽ xây dựng một AI Chatbot hoàn chỉnh sử dụng kiến trúc serverless trên AWS. Chatbot sử dụng Claude Haiku 4.5 được deploy hoàn toàn serverless với chi phí thấp và khả năng scale tự động.
### Các module
**Module 1**: Setup Amazon Bedrock
**Module 2**: Create Lambda Function
**Module 3**: Configure API Gateway
**Module 4**: Deploy Frontend to S3
**Module 5**: Testing & Debugging

 Kiến Trúc Tổng Quan
 ![alt text](https://paperkite-master.github.io/AWS_FCJ/images/5-Workshop/5.1-Workshop-overview/image.png)



---

## **AWS Services & Vai Trò Trong Workshop**

### 1. **Amazon Bedrock — AI Brain**
- Xử lý và tạo phản hồi từ tin nhắn user.
- Sử dụng **Claude Haiku 4.5** (Messages API).
- Tính năng:
  - Inference Profiles
  - Quản lý conversation history
  - Temperature control

---

### 2. **AWS Lambda — Backend Logic**
Vai trò:
- Nhận request từ API Gateway
- Validate input, format prompt
- Gọi Bedrock và trả response

Tính năng:
- Node.js 24 runtime  
- Environment variables (MODEL_ID, ALLOWED_ORIGIN)
- CloudWatch Logs
- Input validation + error handling
- Retry logic  
- CORS handling

---

### 3. **Amazon API Gateway — API Endpoint**
Vai trò:
- Public REST API cho browser
- CORS management
- Route POST `/chat` đến Lambda
- Throttling tránh abuse

Tính năng:
- REST API hoặc HTTP API  
- CORS configuration  
- Lambda Proxy Integration  
- OPTIONS method cho CORS preflight  

---

### 4. **Amazon S3 — Frontend Hosting**
- Static Website Hosting
- Host HTML/CSS/JS
- Cho phép người dùng truy cập chatbot qua browser

---

### 5. **Amazon CloudWatch — Monitoring & Debugging**
- Thu thập log từ Lambda
- Debug request/response & errors

---

## Workshop Learning Objectives

Sau workshop, bạn sẽ:

- Hiểu kiến trúc serverless và cách các service tương tác
- Deploy chatbot production-ready với Claude Haiku 4.5
- Tích hợp Bedrock API với Lambda 
- Cấu hình API Gateway 
- Host static website trên S3
- Debug bằng CloudWatch Logs
- Tối ưu chi phí serverless

---
