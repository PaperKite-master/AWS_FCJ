---
title: "Proposal"
date: 2025-09-09
weight: 2
chapter: false
pre: " <b> 2. </b> "
---
# Website bán quần áo "leaf"
## Sử dụng tích hợp các dịch vụ aws để tối ưu hệ thống (s3, cloud watch,...)
## 1. Tóm tắt điều hành
Leaf là một nền tảng thương mại điện tử chuyên buôn bán các mặt hàng thời trang nam nữ, bao gồm cả các phụ kiện thời trang như trang sức, giày dép, nón các loại, dây da,... wed tích hợp các dịch vụ của aws để tối ưu hóa chi phí và nâng cao trải nghiệm sản phẩm.
## 2.Tuyên bố vấn đề.
*Vấn đề*
Một cửa hàng bán quần áo đang cần một kênh thương mại điện tử riêng để tối ưu hóa trải nghiệm của người dùng.
*Giải pháp*
Tạo một trang wed bán hàng riêng cho cửa hàng, sử dụng các dịch vụ aws để tối ưu hóa chi phí, thời gian và trải nghiệm người dùng.
## 3.Kiến trúc giải pháp
Nền tảng áp dụng kiến trúc AWS Serverless đẻ quản lý đữ liệu
*Dịch vụ aws sử dụng*
*AmazonS3*:Lưu trữ các tệp tĩnh (ảnh sản phẩm, logo, video giới thiệu, file CSS/JS).
*Amazon DynamoDB*:Lưu trữ dữ liệu quan hệ (dữ liệu sản phẩm, người dùng,...).
*Amazon Lambda*:Xử lý backend không cần quản lý server (serverless).
*Amazon WAF*:Bảo vệ website khỏi các tấn công web (Injection, DDOs,..).
*Amazon CloudWatch*:Monitoring, logging, alert.
*Amazon Cognito*: dịch vụ quản lý danh tính
*Amazon Amplify*: deploy hệ thống
## 4. Triển khai kĩ thuật
*Các giai đoạn triển khai*
1. *Thu thập các yêu cầu và chức năng của hệ thống*
2. *Tính toán chi phí và kiểm tra tính khả thi*
3. *Thiết kế giao diện mẫu bằng figma*
4. *Xây dựng các thuộc tính của database*
5. *Xây dựng giao diện wed(frontend)*
6. *Xây dụng API, Backend, tích hợp các dịch vụ aws*
7. *Phát triển, kiểm thử, và triển khai dự án*
## 5. Lộ trình & Mốc triển khai
- Tháng 1: Học AWS.
- Tháng 2: Thiết kế, và triển khai dự án.
- Tháng 3: Triển khai, kiểm thử.
## 6.Ước tính ngân sách
Xem chi phí trên : https://calculator.aws/#/estimate?id=a390da12e17aacf8927f6adb29e9db690acce749
	

*Amazon Simple Storage Service (S3)*

0.69 USD

US East (Ohio)

S3 Standard storage (20 GB per month), PUT, COPY, POST, LIST requests to S3 Standard (300), GET, SELECT, and all other requests from S3 Standard (300), Data returned by S3 Select (10 GB per month), Data scanned by S3 Select (10 GB per month) DT Inbound: Internet (200 GB per month), DT Outbound: US East (N. Virginia) (20 GB per month)

*Amazon DynamoDB*
-
0.25 USD

US East (N. Virginia)

Table class (Standard), Average item size (all attributes) (200 KB), Data storage size (1 GB)

*AWS Lambda*

0.00 USD

US East (N. Virginia)

Architecture (x86), Architecture (x86), Invoke Mode (Buffered), Amount of ephemeral storage allocated (10 GB), Number of requests (1000000 per month)

*AWS Web Application Firewall (WAF)*
-
5.00 USD

US East (N. Virginia)

Number of Web Access Control Lists (Web ACLs) utilized (1 per month), Number of Rules added per Web ACL (0 per month)

*Amazon Cognito*
-
5.00 USD

US East (N. Virginia)

Optimization Rate for Token Requests (0), Optimization Rate for App Clients (0), Advanced security features (Enabled), Number of monthly active users (MAU) (100)

*AWS Amplify*

1.81 USD

US East (N. Virginia)

Select build instance size (Standard (8 GB Memory, 4 vCPUs)), Duration of each request (in ms) (500), Number of build minutes (60 per month), Data stored per month (20 GB), Data served per month (5 GB)

*Amazon CloudWatch*

9.02 USD

US East (N. Virginia)

Number of Metrics (includes detailed and custom metrics) (20), Standard Logs: Data Ingested (5 GB), Number of Dashboards (1), Number of Standard Resolution Alarm Metrics (5)

*Tổng*: 21.77 USD/ tháng
## 7.Kết quả kỳ vọng
- Trang wed chạy với độ trễ thấp, hiển thị hình ảnh mượt mà