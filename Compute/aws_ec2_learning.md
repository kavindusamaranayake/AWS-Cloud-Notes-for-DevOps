# Amazon EC2 (Elastic Compute Cloud) - Complete Learning Notes

## 🚀 Overview

**Amazon Elastic Compute Cloud (Amazon EC2)** is a foundational service in AWS that provides **resizable compute capacity in the cloud**. It allows users to launch and manage virtual servers, known as **instances**, to run applications just like on physical machines.

EC2 is designed to make web-scale computing easier for developers by providing **on-demand, scalable, and secure virtual machines** in the AWS Cloud.

---

## 💡 Key Benefits

- **Scalability:** Instantly scale up or down based on demand.
- **Cost-Effectiveness:** Pay only for the compute time you use.
- **Flexibility:** Choose from various instance types optimized for different workloads.
- **Security:** Control network access and integrate with IAM roles and security groups.
- **Reliability:** Built on AWS’s highly available global infrastructure.

---

## 🏗️ EC2 Architecture Overview

Here’s how EC2 integrates within AWS architecture:

```
+-----------------------------------------------------+
|                   AWS Global Network                |
|                                                     |
|  +----------------+      +---------------------+    |
|  |   AWS Region   | ---> |  Availability Zone  |    |
|  +----------------+      +---------------------+    |
|                                                     |
|       +-------------------------------------+       |
|       |     Amazon EC2 Instance (Virtual)   |       |
|       |  +------------------------------+   |       |
|       |  |  OS + Application Stack      |   |       |
|       |  +------------------------------+   |       |
|       |  |  EBS Volume (Storage)        |   |       |
|       |  |  Elastic IP (Static IP)      |   |       |
|       +-------------------------------------+       |
|                                                     |
+-----------------------------------------------------+
```

---

## ⚙️ EC2 Instance Lifecycle

Each EC2 instance transitions through multiple states:

```
┌────────────┐     ┌───────────┐     ┌─────────────┐     ┌────────────┐
│  Pending   │ --> │  Running  │ --> │  Stopping   │ --> │  Stopped   │
└────────────┘     └───────────┘     └─────────────┘     └────────────┘
                                          │
                                          ▼
                                    ┌────────────┐
                                    │ Terminated │
                                    └────────────┘
```

**State Descriptions:**
- **Pending:** Instance is being initialized (not billed).
- **Running:** Instance is active and billing starts.
- **Stopping / Stopped:** Instance is stopped, not billed but storage persists.
- **Terminated:** Instance is permanently deleted.

---

## 🧱 EC2 Instance Components

- **Amazon Machine Image (AMI):** Template containing OS, configurations, and apps.
- **Instance Type:** Determines CPU, memory, and network performance.
- **EBS Volume:** Persistent block storage for your instance.
- **Elastic IP:** Static IP address for easy access.
- **Security Group:** Acts as a virtual firewall for inbound and outbound traffic.
- **Key Pair:** Used for SSH authentication.

---

## 🖥️ EC2 Instance Types

EC2 offers multiple **instance families**, each optimized for specific use cases.

| Family | Name | Description | Example Use Case |
|---------|------|--------------|------------------|
| **T** | Burstable | Low-cost, general workloads | Web servers, dev/test apps |
| **M** | General Purpose | Balanced compute, memory | Small-medium databases |
| **C** | Compute Optimized | High CPU-to-memory ratio | Batch processing, game servers |
| **R** | Memory Optimized | High memory performance | In-memory databases |
| **G** | GPU Instances | Graphics and ML workloads | Deep learning, rendering |
| **P** | GPU Accelerated | AI/ML, HPC | Model training |
| **I/D/Hpc** | Storage Optimized | High I/O workloads | Data warehousing, analytics |
| **Mac** | macOS Instances | Apple development | iOS/macOS CI/CD |

**Example:**
```bash
# Launching a T3.micro instance using AWS CLI
aws ec2 run-instances \
  --image-id ami-0abcd1234abcd5678 \
  --count 1 \
  --instance-type t3.micro \
  --key-name my-key-pair \
  --security-group-ids sg-0123456789abcdef0 \
  --subnet-id subnet-6e7f829e
```

---

## 💰 EC2 Purchasing Options

AWS provides multiple pricing models depending on usage and cost optimization.

### 1️⃣ On-Demand Instances

- **No long-term commitment.**
- **Pay per second** (minimum 60 seconds).
- Ideal for **short-term or unpredictable workloads**.

**Example:** Running a website during promotions that requires temporary capacity.

### 2️⃣ Savings Plans

- **Flexible pricing model** that provides savings (up to 72%).
- Commit to 1 or 3 years of consistent usage.
- Applies across **EC2, Fargate, and Lambda**.
- Manageable through **AWS Cost Explorer**.

**Example:** Committing to predictable usage for backend services.

### 3️⃣ Reserved Instances (RI)

- Commit to a **specific instance type** for 1 or 3 years.
- Up to **75% savings** compared to On-Demand.
- Types: **Standard** (more savings) or **Convertible** (flexible instance types).

**Example:** Hosting a production database server for 3 years.

### 4️⃣ Spot Instances

- Use **unused AWS capacity** at up to **90% discount**.
- Can be interrupted when AWS needs capacity.
- Ideal for **fault-tolerant workloads** (e.g., big data, batch jobs).

**Example:** Running machine learning training jobs that can resume from checkpoints.

### 5️⃣ Dedicated Hosts

- Provide **physical servers** dedicated to your organization.
- Support **BYOL (Bring Your Own License)** compliance.
- Great for **enterprise compliance and security requirements**.

### 6️⃣ Dedicated Instances

- Run on hardware **dedicated to a single customer**.
- Ensures isolation but managed by AWS.
- Ideal for **regulated industries** (finance, healthcare).

---

## 🧩 Amazon Machine Images (AMI)

An **AMI** provides the information required to launch an EC2 instance:

- **Base OS** (e.g., Amazon Linux, Ubuntu, Windows)
- **Launch permissions** (who can use it)
- **Block device mapping** (defines volumes to attach)

**Example:** Creating a custom AMI from an existing instance:
```bash
aws ec2 create-image --instance-id i-1234567890abcdef0 --name "MyCustomAMI" --description "Custom web server image"
```

---

## 🔐 Security and Networking

- Use **Security Groups** for inbound/outbound traffic control.
- Configure **Key Pairs** for SSH login.
- Use **Elastic IPs** for static addressing.
- Access private resources with **VPC integration**.

**Example:** SSH into an EC2 instance
```bash
ssh -i "my-key.pem" ec2-user@<public-ip>
```

---

## 📊 EC2 Monitoring and Management

Use **Amazon CloudWatch** to monitor instance performance:

- CPU Utilization
- Disk I/O and Network I/O
- Memory usage (via custom metrics)

**Example:**
- Create alarms for CPU > 80%
- Automatically scale instances using **Auto Scaling Groups**

---

## 🧠 Best Practices

✅ Choose the right instance type based on workload.  
✅ Use IAM roles instead of embedding credentials.  
✅ Tag resources for easy management.  
✅ Automate deployments with **EC2 Launch Templates**.  
✅ Backup critical data using **EBS Snapshots**.  
✅ Use **Elastic Load Balancer** for high availability.  

---

## 🔁 Example Use Case: Web Server Deployment

**Goal:** Deploy a simple web application using EC2.

1. Launch a **t2.micro** instance (free tier eligible).
2. SSH into the instance.
3. Install a web server:
   ```bash
   sudo yum install httpd -y
   sudo systemctl start httpd
   sudo systemctl enable httpd
   ```
4. Create a simple HTML file:
   ```bash
   echo "<h1>Hello from EC2!</h1>" | sudo tee /var/www/html/index.html
   ```
5. Open your instance’s public IP in a browser → ✅ Live website!

---

## 🧾 Summary

| Concept | Description |
|----------|-------------|
| **EC2** | Virtual compute service for hosting applications |
| **AMI** | Template for launching instances |
| **EBS** | Persistent storage attached to instances |
| **Elastic IP** | Static IP for instance access |
| **Instance Type** | Defines CPU, memory, and network performance |
| **Security Group** | Virtual firewall controlling traffic |

---

## 🧭 Conclusion

Amazon EC2 is the backbone of AWS compute services, offering flexibility, scalability, and cost-efficiency for all kinds of workloads—from small web apps to enterprise-grade systems.

Learning EC2 is essential for cloud engineers and DevOps professionals, as it provides the foundation for understanding AWS infrastructure.

---

### 🙏 Thanks for Reading!
If you found this helpful, star ⭐ this repository on GitHub to help others learn too!

