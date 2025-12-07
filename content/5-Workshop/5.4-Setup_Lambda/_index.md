---
title : "Setup Lambda"
date: 2025-09-09 
weight : 4 
chapter : false
pre : " <b> 5.4. </b> "
---

**1. Go to Lambda**
![alt text](https://paperkite-master.github.io/AWS_FCJ/images/5-Workshop/5.4-Setup_Lambda/image.png)
- You can code with Lambda using languages as shown in the image. In this workshop, we will use Node.js
**2. Click on Create function**
- Fill in the function name and select the runtime type
![alt text](https://paperkite-master.github.io/AWS_FCJ/images/5-Workshop/5.4-Setup_Lambda/image-1.png)
- Click on "Change default execution role", select "existing role", then click on the role you created earlier for the workshop
![alt text](https://paperkite-master.github.io/AWS_FCJ/images/5-Workshop/5.4-Setup_Lambda/image-2.png)
- Click on "Create function"

**3. Set up function**
- Go to the Configuration tab
![alt text](https://paperkite-master.github.io/AWS_FCJ/images/5-Workshop/5.4-Setup_Lambda/image-3.png)
- Increase memory to 500MB and timeout to 2 minutes
![alt text](https://paperkite-master.github.io/AWS_FCJ/images/5-Workshop/5.4-Setup_Lambda/image-4.png)
- Go to the Code tab and paste this code:
![alt text](https://paperkite-master.github.io/AWS_FCJ/images/5-Workshop/5.4-Setup_Lambda/image-5.png)
- Click the Deploy button

````javascript
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

    // Claude uses messages format
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

        const reply = result.content[0].text || "Sorry, I cannot generate a response.";

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
                error: "AI service is temporarily unavailable. Please try again later."
            })
        };
    }
};
```
**Related Documentation:**
https://docs.aws.amazon.com/lambda/latest/dg/getting-started.html