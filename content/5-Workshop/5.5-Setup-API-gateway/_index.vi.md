---
title : "Chuẩn bị API gateway"
date: 2025-09-09
weight : 5
chapter : false
pre : " <b> 5.5 </b> "
---

**1. Vào API gateway**
- Nhấp vào create an api
![alt text](https://paperkite-master.github.io/AWS_FCJ/images/5-Workshop/5.5-Setup-API-gateway/image.png)
- Chọn build trong Rest api
![alt text](https://paperkite-master.github.io/AWS_FCJ/images/5-Workshop/5.5-Setup-API-gateway/image-1.png)
- Nhập tên 
![alt text](https://paperkite-master.github.io/AWS_FCJ/images/5-Workshop/5.5-Setup-API-gateway/image-2.png)
- Chọn policy security
![alt text](https://paperkite-master.github.io/AWS_FCJ/images/5-Workshop/5.5-Setup-API-gateway/image-3.png)
- Nhấp chuột vào create API
**2. Tạo Resource**
- Nhấp vào create resource
![alt text](https://paperkite-master.github.io/AWS_FCJ/images/5-Workshop/5.5-Setup-API-gateway/image-4.png)
- Đặt tên resource và tick vào CORS
![alt text](https://paperkite-master.github.io/AWS_FCJ/images/5-Workshop/5.5-Setup-API-gateway/image-5.png)
- Nhấp chuột vào nút create resource
**3. Create method**
- Nhấp chuột vào create method
![alt text](https://paperkite-master.github.io/AWS_FCJ/images/5-Workshop/5.5-Setup-API-gateway/image-6.png)
- Chọn phương thức post và bật Lambda proxy integration
![alt text](https://paperkite-master.github.io/AWS_FCJ/images/5-Workshop/5.5-Setup-API-gateway/image-7.png)
- Thêm ARN
![alt text](https://paperkite-master.github.io/AWS_FCJ/images/5-Workshop/5.5-Setup-API-gateway/image-8.png)
- Nhấp chuột vào create method
**4.deploy API**
-  Nhấp chuột vào deploy API
![alt text](https://paperkite-master.github.io/AWS_FCJ/images/5-Workshop/5.5-Setup-API-gateway/image-9.png)
- Chọn new stage và đặt tên cho stage
![alt text](https://paperkite-master.github.io/AWS_FCJ/images/5-Workshop/5.5-Setup-API-gateway/image-10.png)
- Nhấp chuột vào nút deploy
**5. Test**
- Sau khi tạo stage xong bạn sẽ có được Invoke URL
  ![alt text](https://paperkite-master.github.io/AWS_FCJ/images/5-Workshop/5.5-Setup-API-gateway/image-11.png)
- Nhập invoke url để test trên postman với cú pháp invoke url/Tên resource, chọn phương thức post
  ![alt text](https://paperkite-master.github.io/AWS_FCJ/images/5-Workshop/5.5-Setup-API-gateway/image-12.png)
- Nhập
  
  {
    "message": "amazon bedrock là gì?, nói ngắn gọn không quá 3 dòng"
  }
 
- Sau khi Nhấp chuột vào send
![alt text](https://paperkite-master.github.io/AWS_FCJ/images/5-Workshop/5.5-Setup-API-gateway/image-13.png)
- Vậy là bạn đã thành công