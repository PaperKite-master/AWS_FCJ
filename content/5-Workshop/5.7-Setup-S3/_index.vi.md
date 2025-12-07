---
title : "Chuẩn bị s3"
date: 2025-09-09
weight : 6
chapter : false
pre : " <b> 5.7. </b> "
---
**1. Tạo một file html để deploy lên s3**
- Thay InvokeURL/resourceName trong đoạn code dưới bằng invoke URL của bạn
```
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
  <title>Chatbot - ChatGPT Style</title>

  <style>
    body {
      margin: 0;
      font-family: "Inter", "Segoe UI", sans-serif;
      background-color: #f0f0f0;
      display: flex;
      flex-direction: column;
      height: 100vh;
    }

    header {
      background-color: #2e7d32; /* Green */
      color: white;
      padding: 1rem;
      text-align: center;
      font-weight: bold;
      font-size: 1.2rem;
    }

    #chatBox {
      flex: 1;
      overflow-y: auto;
      padding: 1.5rem;
      display: flex;
      flex-direction: column;
      gap: 1rem;
    }

    /* ChatGPT-like message blocks */
    .message {
      width: 100%;
      max-width: 800px;
      margin: auto;
      padding: 1rem;
      border-radius: 10px;
      font-size: 1rem;
      line-height: 1.5;
      border: 1px solid #ddd;
      background-color: #ffffff;
    }

    .user {
      background-color: #e8f5e9; /* xanh lá nhạt */
      border-color: #c8e6c9;
    }

    .bot {
      background-color: #ffffff;
      border-color: #e0e0e0;
    }

    /* ChatGPT-like input bar */
    footer {
      padding: 1rem;
      background-color: #f7f7f8;
      border-top: 1px solid #ddd;
      display: flex;
      justify-content: center;
    }

    .input-container {
      width: 100%;
      max-width: 800px;
      display: flex;
      gap: 0.5rem;
      background: white;
      border: 1px solid #ccc;
      padding: 0.75rem;
      border-radius: 12px;
      box-shadow: 0 2px 4px rgba(0,0,0,0.1);
    }

    input {
      flex: 1;
      border: none;
      outline: none;
      font-size: 1rem;
      background: none;
    }

    button {
      background-color: #4caf50;
      color: white;
      border: none;
      padding: 0.6rem 1rem;
      font-size: 1rem;
      border-radius: 8px;
      cursor: pointer;
      transition: 0.2s ease;
    }

    button:hover {
      background-color: #449d48;
    }
  </style>
</head>

<body>

  <header>🌿 Chatbot </header>

  <div id="chatBox"></div>

  <footer>
    <div class="input-container">
      <input type="text" id="userInput" placeholder="Send a message..." />
      <button onclick="sendMessage()">Send</button>
    </div>
  </footer>

  <script>
    let history = [];

    async function sendMessage() {
      const userInput = document.getElementById("userInput");
      const message = userInput.value.trim();
      if (!message) return;

      addMessage(message, "user");

      const response = await fetch("InvokeURL/resourceName", {
        method: "POST",
        headers: { "Content-Type": "application/json" },
        body: JSON.stringify({ message, history })
      });

      const data = await response.json();
      const botReply = data.response;

      addMessage(botReply, "bot");

      history.push({ user: message, assistant: botReply });

      userInput.value = "";
    }

    function addMessage(text, sender) {
      const msg = document.createElement("div");
      msg.classList.add("message", sender);
      msg.textContent = text;
      document.getElementById("chatBox").appendChild(msg);
      msg.scrollIntoView({ behavior: "smooth" });
    }
  </script>

</body>
</html>
```
**2. Vào s3**
- Chọn create bucket
![alt text](https://paperkite-master.github.io/AWS_FCJ/images/5-Workshop/5.7-Setup-S3/image.png)
- Tạo tên cho bucket
![alt text](https://paperkite-master.github.io/AWS_FCJ/images/5-Workshop/5.7-Setup-S3/image-1.png)
- bỏ Block public (Theo best practice, bạn nên để bucket private, nhưng trong workshop này, mình sẽ để public để có thể test dễ dàng)
![alt text](https://paperkite-master.github.io/AWS_FCJ/images/5-Workshop/5.7-Setup-S3/image-2.png)
- Nhấp chuột vào create bucket
**3. Vào bucket bạn vừa tạo**
- Chọn tab properties
![alt text](https://paperkite-master.github.io/AWS_FCJ/images/5-Workshop/5.7-Setup-S3/image-3.png)
- Nhấp chuột vào edit trong static wed hosting
![alt text](https://paperkite-master.github.io/AWS_FCJ/images/5-Workshop/5.7-Setup-S3/image-4.png)
- Chọn enable
![alt text](https://paperkite-master.github.io/AWS_FCJ/images/5-Workshop/5.7-Setup-S3/image-5.png)
- Chọn save changes
- Vào tab Permissio và chọn edit trong Bucket policy
![alt text](https://paperkite-master.github.io/AWS_FCJ/images/5-Workshop/5.7-Setup-S3/image-6.png)
- Dán đoạn code này vào bucket policy (đổi your-bucket-name bằng tên bucket của bạn)
```
{
    "Version": "2012-10-17",
    "Statement": [
      {
        "Sid": "PublicReadGetObject",
        "Effect": "Allow",
        "Principal": "*",
        "Action": "s3:GetObject",
        "Resource": "arn:aws:s3:::your-bucket-name/*"
      }
    ]
  }
```
![alt text](https://paperkite-master.github.io/AWS_FCJ/images/5-Workshop/5.7-Setup-S3/image-7.png)
- Nháy chuột vào nút save change
- Vào tab object upload file html của bạn lên s3
![alt text](https://paperkite-master.github.io/AWS_FCJ/images/5-Workshop/5.7-Setup-S3/image-8.png)
![alt text](https://paperkite-master.github.io/AWS_FCJ/images/5-Workshop/5.7-Setup-S3/image-9.png)
- Nhấp chuột vào object url để vào trang html đã deploy
![alt text](https://paperkite-master.github.io/AWS_FCJ/images/5-Workshop/5.7-Setup-S3/image-10.png)
- Vậy là bạn đã thành công
![alt text](https://paperkite-master.github.io/AWS_FCJ/images/5-Workshop/5.7-Setup-S3/image-11.png)