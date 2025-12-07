---
title : "Setup API Gateway"
date: 2025-09-09
weight : 5
chapter : false
pre : " <b> 5.5 </b> "
---

**1. Go to API Gateway**
- Click "Create an API"
![alt text](https://paperkite-master.github.io/AWS_FCJ/images/5-Workshop/5.5-Setup-API-gateway/image.png)
- Select "Build" under REST API
![alt text](https://paperkite-master.github.io/AWS_FCJ/images/5-Workshop/5.5-Setup-API-gateway/image-1.png)
- Enter a name
![alt text](https://paperkite-master.github.io/AWS_FCJ/images/5-Workshop/5.5-Setup-API-gateway/image-2.png)
- Choose a security policy
![alt text](https://paperkite-master.github.io/AWS_FCJ/images/5-Workshop/5.5-Setup-API-gateway/image-3.png)
- Click "Create API"

**2. Create Resource**
- Click "Create Resource"
![alt text](https://paperkite-master.github.io/AWS_FCJ/images/5-Workshop/5.5-Setup-API-gateway/image-4.png)
- Name the resource and enable CORS
![alt text](https://paperkite-master.github.io/AWS_FCJ/images/5-Workshop/5.5-Setup-API-gateway/image-5.png)
- Click the "Create Resource" button

**3. Create Method**
- Click "Create Method"
![alt text](https://paperkite-master.github.io/AWS_FCJ/images/5-Workshop/5.5-Setup-API-gateway/image-6.png)
- Select POST and enable Lambda Proxy Integration
![alt text](https://paperkite-master.github.io/AWS_FCJ/images/5-Workshop/5.5-Setup-API-gateway/image-7.png)
- Add the Lambda function ARN
![alt text](https://paperkite-master.github.io/AWS_FCJ/images/5-Workshop/5.5-Setup-API-gateway/image-8.png)
- Click "Create Method"

**4. Deploy API**
- Click "Deploy API"
![alt text](https://paperkite-master.github.io/AWS_FCJ/images/5-Workshop/5.5-Setup-API-gateway/image-9.png)
- Choose "New Stage" and name the stage
![alt text](https://paperkite-master.github.io/AWS_FCJ/images/5-Workshop/5.5-Setup-API-gateway/image-10.png)
- Click the "Deploy" button

**5. Test**
- After creating the stage, you will get the Invoke URL
  ![alt text](https://paperkite-master.github.io/AWS_FCJ/images/5-Workshop/5.5-Setup-API-gateway/image-11.png)
- Use the Invoke URL to test in Postman with the format InvokeURL/<resource-name>, using the POST method
  ![alt text](https://paperkite-master.github.io/AWS_FCJ/images/5-Workshop/5.5-Setup-API-gateway/image-12.png)
- Use the following JSON body:
  
  {
    "message": "What is Amazon Bedrock? Please answer briefly, no more than 3 lines."
  }
 
- Click "Send"
![alt text](https://paperkite-master.github.io/AWS_FCJ/images/5-Workshop/5.5-Setup-API-gateway/image-13.png)
- You should receive a successful response
---