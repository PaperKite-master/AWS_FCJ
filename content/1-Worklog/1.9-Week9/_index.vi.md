---
title: "Worklog Tuần 9"
date: ""
weight: 1
chapter: false
pre: " <b> 1.9. </b> "
---



### Mục tiêu tuần 9:

* Cài đặt môi trường chạy song song để thiết kế được project.
* Hoàn thành việc gắn api Quản lí sản phẩm và Phân loại.

### Các nhiệm vụ cần thực hiện trong tuần:

| Thứ | Công việc                                                                                                                                                                                   | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu                            |
| --- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------ | --------------- | ----------------------------------------- |
| 2   | - Gắn api của backend và test swager trước khi đưa api vào fontend. <br> - Kiểm tra các Login để có thể thêm quyền admin.                                                                                            | 03/11/2025   | 09/11/2025      |   <http://localhost:8080/swagger-ui/index.html?gidzl=Un-z6C7ELdHx1huVmjDLSde1l1xRqL1wPbRiGjxKLtfc0EyGqOG4AMm1kKJItmKiFrplGZUFvjbSnyrKSW&continue=#/product-variant-controller/listProductVariants> |
| 3   | - Thiết kế Phân loại và Quản lí sản phẩm cho admin <br> - Gắng api Quản lí sản phẩm và Phân loại. <br> - Có vấn đề về backend khi chưa cày và chạy backend và Docker.                                            | 03/11/2025   | 09/11/2025      | <http://localhost:8080/swagger-ui/index.html?gidzl=Un-z6C7ELdHx1huVmjDLSde1l1xRqL1wPbRiGjxKLtfc0EyGqOG4AMm1kKJItmKiFrplGZUFvjbSnyrKSW&continue=#/product-variant-controller/listProductVariants> |
| 4   | - Cài môi trường và liên kết git backend và chạy song song giữa backend, Docker, fondend. <br> - Thêm api và chekc kiểm tra xem có hoạt động không.  | 03/11/2025   | 09/11/2025       | <http://localhost:8080/swagger-ui/index.html?gidzl=Un-z6C7ELdHx1huVmjDLSde1l1xRqL1wPbRiGjxKLtfc0EyGqOG4AMm1kKJItmKiFrplGZUFvjbSnyrKSW&continue=#/product-variant-controller/listProductVariants> |
| 5   | - Hoàn thành việc gáng api của cả 2 nhưng vấn để xuất hiện bên trong làm sao để lấy ảnh từ S3 và lưu ảnh để hiển thị lên web. <br> - Học tìm hiểu và lấy link S3Key để đưa vào hiển thị. <br> - TÌm cách để có thể thêm, sửa, xóa hình ảnh hiển thị thông qua danh mục sản phẩm bằng liên kết api.              | 03/11/2025   | 09/11/2025      | <http://localhost:8080/swagger-ui/index.html?gidzl=Un-z6C7ELdHx1huVmjDLSde1l1xRqL1wPbRiGjxKLtfc0EyGqOG4AMm1kKJItmKiFrplGZUFvjbSnyrKSW&continue=#/product-variant-controller/listProductVariants> |
| 6   | - Hoàn thiện được việc thêm, sửa, xóa sản phẩm và hiển thị sản phẩm trong phần quản lí sản phẩm của project.                                                                      | 03/11/2025   | 09/11/2025      | <http://localhost:8080/swagger-ui/index.html?gidzl=Un-z6C7ELdHx1huVmjDLSde1l1xRqL1wPbRiGjxKLtfc0EyGqOG4AMm1kKJItmKiFrplGZUFvjbSnyrKSW&continue=#/product-variant-controller/listProductVariants> |


### Kết quả đạt được tuần 9:

* **Thiết lập môi trường phát triển đồng bộ:**
  * Đã cài đặt và cấu hình thành công môi trường chạy song song giữa Backend, Docker và Frontend.
  * Hoàn tất việc liên kết Git cho Backend, đảm bảo mã nguồn được đồng bộ.

* **Tích hợp API và Phân quyền:**
  * Hiểu và sử dụng thành thạo Swagger để kiểm thử API trước khi tích hợp vào Frontend.
  * Xử lý thành công luồng Đăng nhập và phân quyền cho tài khoản Admin.

* **Hoàn thiện Module Quản lý Sản phẩm:**
  * Hoàn thành thiết kế UI cho trang Quản lý sản phẩm và Phân loại.
  * Tích hợp thành công API cho đầy đủ các chức năng CRUD (Thêm, Sửa, Xóa, Xem) đối với sản phẩm.

* **Giải quyết bài toán lưu trữ hình ảnh với AWS S3:**
  * Nắm được cơ chế lưu trữ đối tượng trên S3.
  * Đã xử lý được vấn đề logic về việc lấy S3 Key từ Backend và hiển thị hình ảnh sản phẩm lên giao diện Frontend.
  * ...