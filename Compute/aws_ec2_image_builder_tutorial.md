# Amazon EC2 Image Builder - Complete Tutorial & Learning Guide

## 🚀 Overview

**Amazon EC2 Image Builder** is a fully managed AWS service that simplifies the automation of building, testing, and distributing **Amazon Machine Images (AMIs)** and **container images**. It ensures that your images are always up-to-date, secure, and compliant with your organization’s policies.

You can use Image Builder to automatically create **golden images** — preconfigured AMIs with your software, security patches, and configurations — through a continuous build pipeline.

---

## 💡 Why Use EC2 Image Builder?

| Benefit | Description |
|----------|-------------|
| **Automation** | Automatically build, test, and distribute images with versioning. |
| **Security** | Integrates with **AWS Inspector**, **STIG**, and **CIS Benchmarks** for compliance and vulnerability scanning. |
| **Consistency** | Ensures every instance is launched from the same pre-approved image. |
| **Integration** | Works with AWS services like **CloudWatch**, **SNS**, **EventBridge**, and **Systems Manager**. |
| **Cost Efficiency** | Reduces manual effort and errors by automating repetitive image creation tasks. |

---

## 🧩 Architecture Overview

The EC2 Image Builder process follows a defined **pipeline** that automates from image creation to distribution:

```
+-----------------------------------------------------------+
|                   AWS EC2 Image Builder                   |
+-----------------------------------------------------------+
|                                                           |
|  1. Source AMI or Docker Base Image                       |
|  2. Build Components (YAML-based scripts/configs)          |
|  3. Image Recipe (defines configuration + version)         |
|  4. Build Phase (instance setup + package installation)    |
|  5. Test Phase (security + validation tests)               |
|  6. Output AMI or Container Image                          |
|  7. Distribution (multi-region, shared, or public)         |
+-----------------------------------------------------------+
|        CloudWatch | SNS | Inspector | EventBridge          |
+-----------------------------------------------------------+
```

---

## ⚙️ Key Concepts

### 1️⃣ Source Image
A **base image** from which your customized AMI or container will be created.
- Examples: Amazon Linux 2, Ubuntu, Windows Server, or an existing AMI.

### 2️⃣ Components
Reusable **YAML-based definitions** that describe build or test actions.
- Build components: Install software or configure OS.
- Test components: Validate configuration, run security scans.

Example YAML snippet:
```yaml
name: InstallApache
description: Install Apache web server
phases:
  build:
    commands:
      - sudo yum install -y httpd
      - sudo systemctl enable httpd
```

### 3️⃣ Image Recipe
A versioned document that defines:
- Source image
- Components to apply
- Custom configurations
- Output type (AMI or container image)

### 4️⃣ Infrastructure Configuration
Specifies where and how the build/test instances run:
- Instance type (e.g., t3.medium)
- IAM role permissions
- VPC/subnet configuration
- SNS notifications
- Logging via CloudWatch

### 5️⃣ Distribution Configuration
Defines how and where the built image is distributed:
- Regions for replication
- Launch permissions (private/public)
- Account or organization sharing

---

## 🪜 Step-by-Step Setup Tutorial

Let’s go through how to create a complete **Image Builder Pipeline** using the AWS Console or CLI.

### Step 1️⃣: Define the Pipeline
1. Navigate to **EC2 → Image Builder → Create Image Pipeline**.
2. Enter details:
   - **Pipeline name** (e.g., `WebServer-Image-Pipeline`)
   - Add tags for identification.
   - Choose **manual** or **scheduled builds** (e.g., every Sunday at 2 AM).

---

### Step 2️⃣: Create or Choose a Recipe
1. Select **Create recipe**.
2. Choose **image type** (Amazon Machine Image or Container Image).
3. Choose **Base Image** → e.g., Amazon Linux 2.
4. Add **components** (predefined or custom YAML scripts).
   - Example: Install Apache, configure NGINX, update packages.

**Sample CLI Command:**
```bash
aws imagebuilder create-image-recipe \
  --name WebServerRecipe \
  --semantic-version 1.0.0 \
  --components '[{"componentArn": "arn:aws:imagebuilder:us-east-1:aws:component/update-linux:1.0.0"}]' \
  --parent-image arn:aws:imagebuilder:us-east-1:aws:image/amazon-linux-2-x86/x.x.x
```

---

### Step 3️⃣: Configure Infrastructure
Define where Image Builder runs:
- Choose **Instance Type** → t3.medium or similar.
- Attach **IAM Role** for EC2 Image Builder.
- Configure **VPC**, **Subnet**, and **Security Groups**.
- Enable **CloudWatch Logs** for build monitoring.

**Sample CLI Command:**
```bash
aws imagebuilder create-infrastructure-configuration \
  --name WebServerInfra \
  --instance-profile-name EC2ImageBuilderRole \
  --security-group-ids sg-0123456789abcdef0 \
  --subnet-id subnet-12345abcde \
  --terminate-instance-on-failure
```

---

### Step 4️⃣: Define Distribution
Set where the built AMI will be shared or replicated.
- Choose **Target Regions** (e.g., `us-east-1`, `ap-south-1`).
- Configure **launch permissions** (private, shared, or public).

**Example CLI Command:**
```bash
aws imagebuilder create-distribution-configuration \
  --name WebServerDistConfig \
  --distributions '[{"region":"us-east-1","amiDistributionConfiguration":{"name":"WebServerImage-{{imagebuilder:buildDate}}"}}]'
```

---

### Step 5️⃣: Build & Test
The build process automatically launches an **EC2 instance**:
1. Installs defined components.
2. Runs validation tests.
3. Creates a new AMI.

After build completion:
- The AMI appears under **EC2 → AMIs**.
- Logs and test results are visible in **CloudWatch**.

**Example Build Command:**
```bash
aws imagebuilder start-image-pipeline-execution --image-pipeline-arn <pipeline-arn>
```

---

## 🧪 Testing & Validation

Each image build goes through automated testing phases:
- **Functional tests:** Ensure applications and services run correctly.
- **Security tests:** Check against CIS, STIG, or custom scripts.
- **Vulnerability scans:** Use **Amazon Inspector** to identify CVEs.

Example test component YAML:
```yaml
name: ValidateApache
description: Verify Apache is running
phases:
  test:
    commands:
      - sudo systemctl is-active httpd
```

---

## 🧠 Advanced Concepts

### 🔄 Version Control
Each image, component, and recipe is **versioned** automatically (1.0.0 → 1.0.1 → 1.1.0).
You can roll back or rebuild previous versions easily.

### 🔔 Notifications
Integrate with **Amazon SNS** to receive email or SMS notifications when:
- Image build starts/completes.
- Tests fail.
- Image is distributed.

### 🔍 Monitoring
Use **Amazon CloudWatch** to monitor:
- Build times
- Pipeline success/failure
- Component execution logs

### 🔐 Security Compliance
Integrate Image Builder with:
- **Amazon Inspector** – scans for vulnerabilities.
- **STIG & CIS Benchmarks** – validates OS-level compliance.
- **Custom Hardening Scripts** – enforce company policies.

---

## 📦 Example: Weekly Hardened AMI for Web Servers

**Scenario:**
You want to automatically build a **secure, patched, and preconfigured web server image** every week.

**Configuration Steps:**
1. Base image: Amazon Linux 2
2. Build components:
   - Update system packages
   - Install Apache & security tools
3. Test components:
   - Verify Apache service
   - Run CIS compliance checks
4. Schedule: Every Sunday 2:00 AM UTC
5. Distribution: Shared across `dev`, `staging`, and `prod` accounts.

**Outcome:**
- Automatically built AMI with current security patches.
- Reduced manual maintenance.
- Consistent infrastructure across environments.

---

## 🧠 Best Practices

✅ Use **version control** for recipes and components.  
✅ Enable **CloudWatch logging** to troubleshoot builds.  
✅ Automate pipeline execution using **EventBridge schedules**.  
✅ Integrate with **AWS Inspector** for compliance validation.  
✅ Distribute AMIs across **multiple regions** for high availability.  
✅ Store **component definitions** in a Git repository for collaboration.

---

## 🧾 Summary

| Concept | Description |
|----------|-------------|
| **Image Builder Pipeline** | Automates the build, test, and distribution of images. |
| **Image Recipe** | Defines base image and components. |
| **Infrastructure Configuration** | Specifies resources and permissions for builds. |
| **Distribution Configuration** | Defines how and where AMIs are shared. |
| **AWSTOE** | AWS Task Orchestrator & Executor for build/test automation. |
| **SNS / CloudWatch** | Enables monitoring and notifications. |

---

## 🧭 Conclusion

AWS EC2 Image Builder is a powerful DevOps tool for automating **image creation pipelines**. It ensures your infrastructure is **consistent, secure, and compliant**, and helps you save time by automating repetitive manual tasks.

When combined with **CI/CD**, **Inspector**, and **EventBridge**, Image Builder forms the backbone of a robust automated cloud image management system.

---

### 🙏 Thanks for Reading!
If you found this guide helpful, consider ⭐ starring this project on GitHub and sharing it to help others learn AWS EC2 Image Builder!

