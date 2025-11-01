# AWS App Runner Tutorial

## 🚀 Overview

**AWS App Runner** is a fully managed service that allows developers to build, deploy, and run containerized web applications and APIs at scale, without managing any infrastructure. It automatically handles scaling, load balancing, and deployment from your source code or container image.

With App Runner, you can go from code to a live, secure, and scalable web application in just a few clicks.

---

## 🧩 Key Features

- **Fully managed** – No need to manage servers or containers manually.
- **Automatic scaling** – Scales up or down based on traffic.
- **Secure by default** – HTTPS, IAM integration, and VPC access.
- **Continuous deployment** – Supports automatic deployments from source or container registry.
- **Supports both source code and container images** – Deploy directly from GitHub or Amazon ECR.

---

## 🏗️ App Runner Architecture

App Runner abstracts complex infrastructure by managing the following components:

```
Source Code / Container Image
        ↓
Build Service (for source code)
        ↓
Deployment Pipeline
        ↓
App Runner Service (runs your application)
        ↓
Load Balancer & Autoscaler
        ↓
Public HTTPS Endpoint
```

### Components:

- **Source Type** – The origin of your application. It can be:
  - Source code repository (e.g., GitHub)
  - Container image repository (e.g., Amazon ECR)

- **App Runner Connection** – A secure connection between App Runner and your source.

- **Runtime** – The environment used to run your application (e.g., Node.js, Python, etc.).

- **Deployment** – App Runner automatically deploys updates from your source.

- **Custom Domain** – You can map your own domain to the App Runner service.

---

## ⚙️ Step-by-Step Setup

Let’s walk through the process of creating an App Runner service.

### 🪜 Step 1: Prepare Your Application

You can use a simple Node.js application:

```bash
mkdir app-runner-demo
cd app-runner-demo
```

Create `app.js`:

```javascript
const express = require('express');
const app = express();

app.get('/', (req, res) => {
  res.send('Hello from AWS App Runner!');
});

const port = process.env.PORT || 8080;
app.listen(port, () => console.log(`Server running on port ${port}`));
```

Add a `Dockerfile`:

```dockerfile
FROM node:18
WORKDIR /usr/src/app
COPY package*.json ./
RUN npm install
COPY . .
CMD ["node", "app.js"]
```

---

### 🪜 Step 2: Push to Repository

You can choose either:

- **Option 1: Source code (GitHub)**  
  Connect App Runner directly to your GitHub repository.

- **Option 2: Container image (Amazon ECR)**  
  Build and push the Docker image:

  ```bash
  aws ecr create-repository --repository-name app-runner-demo
  docker build -t app-runner-demo .
  docker tag app-runner-demo:latest <your_account_id>.dkr.ecr.<region>.amazonaws.com/app-runner-demo:latest
  docker push <your_account_id>.dkr.ecr.<region>.amazonaws.com/app-runner-demo:latest
  ```

---

### 🪜 Step 3: Create App Runner Service

1. Go to **AWS Console → App Runner**.
2. Click **Create service**.
3. Choose **Source type**:
   - From **Source code repository (GitHub)** or
   - From **Container registry (ECR)**.
4. Configure the following:
   - **Deployment settings** → Manual or Automatic.
   - **Service name** → `app-runner-demo`.
   - **CPU/Memory configuration** (example: 1 vCPU, 2 GB).
5. Review and **Create service**.

App Runner automatically builds and deploys your application.

---

### 🪜 Step 4: Access Your Application

After deployment completes:
- You’ll get a **default HTTPS URL** (e.g., `https://<random-id>.<region>.awsapprunner.com`).
- Visit this URL to see your running app!

To add your own domain:
1. Go to **Custom domains** in App Runner.
2. Add your domain name (e.g., `app.mydomain.com`).
3. Update your DNS records as instructed.

---

## 🧮 Supported Configurations

| vCPU | Memory |
|------|---------|
| 0.25 | 0.5 GB  |
| 0.25 | 1 GB    |
| 0.5  | 1 GB    |
| 1    | 2 GB    |
| 1    | 3 GB    |
| 1    | 4 GB    |
| 2    | 4 GB    |
| 2    | 6 GB    |
| 4    | 8 GB    |
| 4    | 10 GB   |
| 4    | 12 GB   |

---

## 🔐 Security and Networking

- **HTTPS Enabled**: Every service comes with a default HTTPS endpoint.
- **VPC Access**: You can configure App Runner to connect to private resources within a VPC.
- **IAM Integration**: Fine-grained access control using AWS IAM roles.

---

## 🔁 Continuous Deployment

App Runner can automatically detect changes from your source (GitHub or ECR) and redeploy.

Example:
- Commit new code to `main` branch.
- App Runner detects the change.
- Automatically rebuilds and redeploys your application.

---

## 🧰 Example Use Cases

- Deploying a **web API** for a microservice.
- Hosting a **frontend web app** (React, Angular, etc.).
- Running **cron jobs or background tasks** via endpoints.
- Rapid prototyping of serverless web apps.

---

## 🧠 Best Practices

- Use **Automatic deployments** with version control.
- Keep your **container images lightweight**.
- Use **AWS CloudWatch Logs** to monitor your application.
- Secure connections using **IAM roles** and **private ECR repos**.
- Configure **autoscaling** to handle traffic spikes.

---

## 📚 Example: Deploying a Python Flask App

`app.py`
```python
from flask import Flask
app = Flask(__name__)

@app.route('/')
def home():
    return 'Hello from AWS App Runner with Python!'

if __name__ == '__main__':
    app.run(host='0.0.0.0', port=8080)
```

`Dockerfile`
```dockerfile
FROM python:3.11
WORKDIR /app
COPY requirements.txt ./
RUN pip install -r requirements.txt
COPY . .
CMD ["python", "app.py"]
```

Push this to ECR and follow the same setup steps.

---

## 🧾 Conclusion

AWS App Runner is an excellent choice for developers who want to deploy containerized web apps or APIs quickly without worrying about servers, scaling, or infrastructure management.

It bridges the gap between serverless and containerized workloads by offering an easy, automated, and scalable platform.

---



