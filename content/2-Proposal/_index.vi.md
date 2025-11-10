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
![](/images/2-Proposal/AWS1.drawio.png)

### Các Lớp Thành Phần Và Vai Trò Trong Kiến Trúc AWS 

#### A. Lớp Giao Diện Người Dùng 

| Dịch vụ | Vai trò | Mô tả chi tiết |
|----------|----------|----------------|
| **AWS Amplify** | Triển khai website | Dùng để **host trang web tĩnh** (React, Vue, Next.js) và **tự động build/deploy** khi push code lên GitHub. |
| **Amazon CloudFront (CDN)** | Tăng tốc độ tải trang | Lưu cache nội dung tĩnh (ảnh, CSS, JS) gần người dùng giúp **giảm độ trễ và băng thông**. |
| **Amazon S3** | Lưu file tĩnh & hình ảnh sản phẩm | Dùng như **kho chứa nội dung (static content)** cho hình ảnh, banner, file CSS/JS. |

---

#### B. Lớp Logic Ứng Dụng 

| Dịch vụ | Vai trò | Mô tả chi tiết |
|----------|----------|----------------|
| **Amazon API Gateway** | Cổng API trung gian | Nhận yêu cầu từ frontend và chuyển tiếp đến các Lambda function để xử lý. |
| **AWS Lambda** | Xử lý logic server-side | Chạy code xử lý đơn hàng, thanh toán, xác thực, gửi email,… **không cần máy chủ riêng**. |
| **Amazon DynamoDB** | Cơ sở dữ liệu NoSQL | Lưu trữ **sản phẩm, tài khoản, đơn hàng, giỏ hàng**, tốc độ cao, mở rộng tự động. |
| **Amazon OpenSearch Service** | Tìm kiếm sản phẩm | Cho phép người dùng **tìm nhanh sản phẩm theo từ khóa, màu, giá**,… |
| **Amazon EventBridge** | Xử lý sự kiện | Kích hoạt sự kiện tự động (ví dụ: khi có đơn hàng mới → gửi email, cập nhật stock). |
| **AWS Secrets Manager** | Bảo mật thông tin nhạy cảm | Lưu **API key, token thanh toán, mật khẩu DB**, giúp bảo mật dữ liệu. |

---

#### C. Lớp Quản Lý Người Dùng & Bảo Mật

| Dịch vụ | Vai trò | Mô tả chi tiết |
|----------|----------|----------------|
| **Amazon Cognito** | Đăng nhập & quản lý người dùng | Hỗ trợ **đăng ký, đăng nhập, reset password, xác thực MFA** mà không cần tự xây dựng hệ thống auth. |
| **AWS WAF (Web Application Firewall)** | Chống tấn công web | Bảo vệ trang web khỏi **SQL Injection, XSS, DDoS** và các truy cập độc hại. |
| **Amazon Route 53** | DNS & domain | Quản lý tên miền . |

---

#### D. Lớp Thông Báo & Giao Tiếp

| Dịch vụ | Vai trò | Mô tả chi tiết |
|----------|----------|----------------|
| **Amazon SNS (Simple Notification Service)** | Gửi thông báo hệ thống | Gửi thông báo cho admin hoặc người dùng (qua email, SMS, hoặc push notification). |
| **Amazon SES (Simple Email Service)** | Gửi email giao dịch | Gửi email xác nhận đơn hàng, khuyến mãi, reset password,... |

---

#### E. Lớp AI & Machine Learning

| Dịch vụ | Vai trò | Mô tả chi tiết |
|----------|----------|----------------|
| **Amazon Translate** | Dịch nội dung | Dịch mô tả sản phẩm sang các ngôn ngữ khác, phục vụ khách quốc tế. |
| **Amazon Bedrock** | Tạo nội dung AI |  tạo **chatbot tư vấn mua hàng**. |

---

#### F. Lớp Giám Sát & Quản Trị

| Dịch vụ | Vai trò | Mô tả chi tiết |
|----------|----------|----------------|
| **Amazon CloudWatch** | Giám sát hoạt động | Theo dõi log, hiệu suất, cảnh báo khi lỗi hoặc chi phí tăng cao. |
| **AWS CloudTrail** | Ghi lại hành động quản trị | Theo dõi lịch sử thao tác (ai thay đổi cấu hình gì, lúc nào), giúp kiểm toán. |


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
Xem chi phí trên : https://calculator.aws/#/estimate?id=dd9c5b1bea2e5345e329d856d546299534bfd8d0
	

## Dịch vụ lưu trữ & dữ liệu

| Dịch vụ | Chức năng | Ước tính sử dụng | Chi phí/tháng (USD) | Ghi chú |
|----------|------------|------------------|----------------------|----------|
| **Amazon S3** | Lưu hình ảnh, CSS, JS | 10 GB storage, ~5k GET, ~500 PUT | **0.35** | Dữ liệu ít, truy cập thấp |
| **DynamoDB** | Lưu dữ liệu đơn hàng, giỏ hàng | ~1 GB data, 100k read/write | **0.20** | Dùng chế độ On-demand |
| **OpenSearch Service** | Tìm kiếm sản phẩm | 1 small instance, 10% uptime | **3.00** | Giảm cấu hình do dữ liệu nhỏ |

---

## Xử lý logic & backend

| Dịch vụ | Chức năng | Ước tính sử dụng | Chi phí/tháng (USD) | Ghi chú |
|----------|------------|------------------|----------------------|----------|
| **AWS Lambda** | Xử lý API, thanh toán | 100k requests, 128MB, 100ms | **0.20** | Rất rẻ nhờ serverless |
| **API Gateway** | Cổng truy cập API | 100k requests | **0.10** | Gắn trực tiếp với Lambda |
| **Secrets Manager** | Bảo mật API key, DB | 1 secret | **0.40** | Giữ nguyên |
| **EventBridge** | Kích hoạt sự kiện nội bộ | 1k events/tháng | **0.05** | Dùng nhẹ cho notify/order |

---

##  Giao diện, người dùng & bảo mật

| Dịch vụ | Chức năng | Ước tính sử dụng | Chi phí/tháng (USD) | Ghi chú |
|----------|------------|------------------|----------------------|----------|
| **Amplify Hosting** | Triển khai frontend | 10 GB, 3 build/tháng | **1.50** | CI/CD + hosting tĩnh |
| **CloudFront (CDN)** | Phân phối nội dung | 5 GB out | **0.20** | Giảm chi phí CDN |
| **WAF (Web Firewall)** | Chống tấn công web | 1 ACL | **5.00** | Cần cho bảo mật cơ bản |
| **Cognito** | Đăng nhập/đăng ký người dùng | 100 MAU | **1.00** | Giảm so với gốc (5 USD) |
| **Route 53** | Tên miền DNS | 1 hosted zone | **0.50** | Không đổi |

---

## Email & Thông báo

| Dịch vụ | Chức năng | Ước tính sử dụng | Chi phí/tháng (USD) | Ghi chú |
|----------|------------|------------------|----------------------|----------|
| **SES (Email)** | Gửi email xác nhận đơn hàng | 1,000 email/tháng | **0.15** | 0.0001 USD/email |
| **SNS (Thông báo)** | Notify qua HTTP/email | 1k messages | **0.05** | Dùng cho notify nội bộ |

---

## AI & Machine Learning (tùy chọn)

| Dịch vụ | Chức năng | Ước tính sử dụng | Chi phí/tháng (USD) | Ghi chú |
|----------|------------|------------------|----------------------|----------|
| **Translate** | Dịch sản phẩm Anh↔Việt | 10k ký tự | **0.15** | Hỗ trợ khách quốc tế |
| **Bedrock** | Sinh mô tả sản phẩm | 100 requests nhỏ | **0.10** | Có thể tắt nếu không cần |

---

## Giám sát & nhật ký

| Dịch vụ | Chức năng | Ước tính sử dụng | Chi phí/tháng (USD) | Ghi chú |
|----------|------------|------------------|----------------------|----------|
| **CloudWatch** | Theo dõi logs, metrics | 1–2 GB log | **1.50** | Giảm so với 9 USD |
| **CloudTrail** | Audit hoạt động | Dùng mặc định | **0.00** | Free tier đủ dùng |

---

## Tổng kết chi phí thực tế

| Nhóm dịch vụ | Tổng chi phí (USD/tháng) |
|---------------|---------------------------|
| Lưu trữ & dữ liệu | 3.55 |
| Backend & xử lý | 0.75 |
| Giao diện & bảo mật | 8.20 |
| Email & thông báo | 0.20 |
| AI & ML (tùy chọn) | 0.25 |
| Giám sát & logs | 1.50 |
| **Tổng cộng (thực tế)** | **≈ 14.45 USD / tháng (~375,000 VND)** |
## 7.Kết quả kỳ vọng
- Trang wed chạy với độ trễ thấp, hiển thị hình ảnh mượt mà. dịch tất cả lại thành tiếng anh cho tôi (vẫn là markdown)