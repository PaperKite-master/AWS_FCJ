---
title : "Chuẩn bị Lambda"
date: 2025-09-09 
weight : 4 
chapter : false
pre : " <b> 5.4. </b> "
---

**1. Vào Lambda**
![alt text](https://paperkite-master.github.io/AWS_FCJ/images/5-Workshop/5.4-Setup_Lambda/image.png)
- Bạn có thể code với lambda bằng các ngôn ngữ như trong hình.Trong workshop này mình sẽ sử dụng ngôn ngữ Node.js
**2. Nhấp chuột vào Create function**
- Điền tên function, chọn kiểu runtime
![alt text](https://paperkite-master.github.io/AWS_FCJ/images/5-Workshop/5.4-Setup_Lambda/image-1.png)
- Nhấp chuột vào Change default execution role chọn existing role sau đó nhấp chuột vào role mà bạn đã tạo trước đó cho workshop
![alt text](https://paperkite-master.github.io/AWS_FCJ/images/5-Workshop/5.4-Setup_Lambda/image-2.png)
- Nhấp chuột vào create function
**3. Set up function**
- Vào tab configuration
![alt text](https://paperkite-master.github.io/AWS_FCJ/images/5-Workshop/5.4-Setup_Lambda/image-3.png)
- Chỉnh memory lên 500mb và time out là 2m
![alt text](https://paperkite-master.github.io/AWS_FCJ/images/5-Workshop/5.4-Setup_Lambda/image-4.png)
- Vào tab code dán đoạn code này vào
![alt text](https://paperkite-master.github.io/AWS_FCJ/images/5-Workshop/5.4-Setup_Lambda/image-5.png)
- Nhấp chuột vào nút deploy
```
const { BedrockRuntimeClient, InvokeModelCommand } = require("@aws-sdk/client-bedrock-runtime");

const client = new BedrockRuntimeClient({ region: "us-east-1" });

const CORS_HEADERS = {
    "Access-Control-Allow-Origin": process.env.ALLOWED_ORIGIN || "*",
    "Access-Control-Allow-Headers": "Content-Type,X-Amz-Date,Authorization,X-Api-Key",
    "Access-Control-Allow-Methods": "OPTIONS,POST",
    "Content-Type": "application/json"
};

const MAX_MESSAGE_LENGTH = 2000;
const MAX_HISTORY_TURNS = 10;

exports.handler = async (event) => {
    const startTime = Date.now();
    
    console.log("Request received:", {
        sourceIp: event.requestContext?.identity?.sourceIp || event.requestContext?.http?.sourceIp,
        userAgent: event.requestContext?.identity?.userAgent || event.headers?.["user-agent"]
    });

    const httpMethod = event.httpMethod || event.requestContext?.http?.method;
    if (httpMethod === "OPTIONS") {
        return { statusCode: 200, headers: CORS_HEADERS, body: "" };
    }

    let body;
    try {
        body = JSON.parse(event.body || "{}");
    } catch (e) {
        console.error("Invalid JSON:", e.message);
        return { 
            statusCode: 400, 
            headers: CORS_HEADERS, 
            body: JSON.stringify({ error: "Invalid JSON format" }) 
        };
    }

    const userMessage = (body.message || "").toString().trim();
    const history = Array.isArray(body.history) ? body.history : [];

    if (!userMessage) {
        return { 
            statusCode: 400, 
            headers: CORS_HEADERS, 
            body: JSON.stringify({ error: "Message is required" }) 
        };
    }

    if (userMessage.length > MAX_MESSAGE_LENGTH) {
        return { 
            statusCode: 400, 
            headers: CORS_HEADERS, 
            body: JSON.stringify({ 
                error: `Message too long. Maximum ${MAX_MESSAGE_LENGTH} characters allowed.` 
            }) 
        };
    }

    // Claude dùng messages format
    const messages = [];
    
    const recentHistory = history.slice(-MAX_HISTORY_TURNS);
    for (const turn of recentHistory) {
        if (turn.user) {
            messages.push({ role: "user", content: turn.user });
        }
        if (turn.assistant) {
            messages.push({ role: "assistant", content: turn.assistant });
        }
    }
    
    messages.push({ role: "user", content: userMessage });

    
    const requestBody = {
        anthropic_version: "bedrock-2023-05-31",
        max_tokens: 512,
        messages: messages,
        temperature: 0.7
    };

   
    const modelId = "arn:aws:bedrock:us-east-1:756859458422:inference-profile/us.anthropic.claude-haiku-4-5-20251001-v1:0";

    try {
        console.log("Invoking Bedrock model:", modelId);
        
        const command = new InvokeModelCommand({
            modelId,
            contentType: "application/json",
            accept: "application/json",
            body: JSON.stringify(requestBody)
        });

        const response = await client.send(command);
        const result = JSON.parse(new TextDecoder().decode(response.body));

        const reply = result.content[0].text || "Xin lỗi, tôi không thể tạo phản hồi.";

        const duration = Date.now() - startTime;
        console.log("Request completed:", { duration, responseLength: reply.length });

        return {
            statusCode: 200,
            headers: CORS_HEADERS,
            body: JSON.stringify({ response: reply })
        };

    } catch (error) {
        console.error("Bedrock invocation error:", {
            message: error.message,
            code: error.code,
            modelId: modelId
        });

        return {
            statusCode: 500,
            headers: CORS_HEADERS,
            body: JSON.stringify({ 
                error: "Dịch vụ AI tạm thời không khả dụng. Vui lòng thử lại sau."
            })
        };
    }
};

```
**Tài liệu liên quan:**
https://docs.aws.amazon.com/lambda/latest/dg/getting-started.html
