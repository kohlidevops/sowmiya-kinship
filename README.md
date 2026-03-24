# Terraform Infrastructure – Kinship Project
📌 Overview

This repository contains Terraform configurations to provision and manage cloud infrastructure for the Kinship project.

The infrastructure is designed with best practices, including:

Modular structure
Secure networking
Scalable compute resources
Automated provisioning

🏗️ Infrastructure Architecture

The Terraform setup provisions the following resources:

🌐 Network
1 VPC (Virtual Private Cloud)
2 Subnets:
Public Subnet
Private Subnet
VPC Flow Logs enabled for monitoring and auditing

🌍 Cloud NAT
Cloud NAT enables private VMs to access the internet securely without assigning public IPs
Used for:
Installing packages (apt, npm, pip)
Pulling Docker images
Accessing external APIs
Ensures outbound internet access while keeping VMs private

💻 Compute Resources
4 Virtual Machines (VMs):
Public VMs
VPN Server
Assets Server
Private VMs
Knowledge Backend
Studio Frontend (Admin use)

🔐 Security
Firewall rules configured for controlled access
Network Security Groups (NSG) / equivalent rules
Private VMs are not exposed to the internet
Access via VPN server

⚙️ Additional Configurations
Labels/tags for resource organization
Deletion protection is enabled for critical resources
Logging and monitoring enabled

Repository Structure
terraform-gcp-infra/ │── environments/ │ └── poc/ # Environment configuration │ ├── main.tf │ ├── variables.tf │ ├── terraform.tfvars │ ├── backend.tf │ └── terraform.tfstate │ │── modules/ │ ├── vpc/ # VPC module │ └── vm/ # VM module │ │── scripts/ │ └── install-vpn.sh # VPN setup script

🚀 Getting Started
1. Prerequisites

Make sure you have:

Terraform installed (>= 1.x)
Cloud provider account (GCP)
CLI authenticated (e.g., gcloud auth login)
SSH key pair generated
2. Clone Repository
git clone <your-repo-url>
cd terraform-infra/environments/dev
3. Configure Variables

Update the terraform.tfvars file:

project_id = "your-project-id"
region     = "your-region"
zone       = "your-zone"

🚀 Steps to Execute Terraform
1. Navigate to Environment
cd environments/poc
2. Initialize Terraform
terraform init
3. Format Terraform Code
terraform fmt
4. Validate Configuration
terraform validate
5. Plan Infrastructure
terraform plan
6. Apply Infrastructure
🔑 Load SSH Public Key (PowerShell)
$key = Get-Content $env:USERPROFILE\.ssh\<gcp-key.pub> -Raw
▶️ Apply Command
terraform apply -var="ssh_public_key=$key"
🌐 Post Deployment Steps (VPN Setup)

After infrastructure is successfully created:

1. Connect to VPN Server
ssh <username>@<vpn-public-ip>
2. Verify VPN Installation Logs
sudo cat /var/log/startup-script.log
3. Retrieve VPN Client Configuration
sudo cat /root/client1.ovpn
4. Setup VPN on Local Machine

Create a new file:

client1.ovpn
Copy the content from the server and paste into this file
Save the file locally
5. Install OpenVPN
Download and install OpenVPN client on your local machine
Import the client1.ovpn file
6. Test VPN Connection

Once connected:

ping <private-vm-ip>

🔐 Access Private VM

After VPN connection is successful:

ssh -i <private-key-path> <username>@<private-ip>

⚠️ Notes
Ensure SSH key is correct before applying Terraform
VPN must be connected to access private VMs
Do not expose private VM IPs publicly
Keep .tfstate files secure

🔥 Summary

This setup provides:

Secure infrastructure with public & private VMs
VPN-based access to private resources
Modular Terraform design
Easy reproducibility and setup

✅ Workflow Summary
Run Terraform commands
Provision infrastructure
Connect to VPN server
Download .ovpn config
Connect using OpenVPN
Access private VM securely





# Kinship Studio CI Pipeline

## Repository Components

### 1. Continuous Integration Workflow (`kinship-studio-ci.yml`)

The **Kinship Studio CI pipeline** automates code quality checks and application build validation whenever changes are pushed to the repository.

---

## 🔄 Workflow Triggers

The workflow is triggered on:

* Push to `main` branch
* Pull requests targeting `main`
* Manual trigger (`workflow_dispatch`)

---

## ⚙️ Pipeline Stages

The pipeline consists of **two main stages**:

---

# 1. Quality Checks

This stage ensures that the code is clean, consistent, and production-ready before building the application.

### Steps Performed

#### 1. Checkout Repository

The workflow fetches the latest source code from the repository.

---

#### 2. Setup Node.js Environment

Node.js version **20** is installed to match the application runtime.

---

#### 3. Install Dependencies

All required project dependencies are installed using npm.

```
npm install
```

---

#### 4. Prettier Formatting Check

Ensures that the code follows consistent formatting rules.

```
npm run format
```

---

#### 5. ESLint Check

Runs linting to detect code quality issues and enforce best practices.

```
npm run lint
```

---

#### 6. Run Tests

Executes automated test cases to validate application functionality.

```
npm run test
```

---

### ❗ Failure Condition

If any of the following fails:

* Formatting check
* Linting
* Tests

➡️ The pipeline **stops immediately**, and the build step will not run.

---

# 2. Build Next.js Application

Once all quality checks pass, the pipeline proceeds to build the application.

---

### Steps Performed

#### 1. Cache Next.js Build

To improve performance, the `.next/cache` directory is cached.

* Reduces build time for subsequent runs
* Cache key is based on `package.json`

---

#### 2. Build Application

The Next.js application is built for production.

```
npm run build
```

---

## 📦 Output

* A **successful production build** of the Next.js application
* Ensures the app is ready for deployment
* No Docker image is generated in this pipeline (CI-only validation)

---

## 📌 Key Benefits

* ✅ Enforces code formatting standards
* ✅ Detects lint issues early
* ✅ Ensures all tests pass before build
* ✅ Speeds up builds using caching
* ✅ Prevents broken builds from reaching deployment

---

## 🛠 Requirements

Ensure the following scripts exist in your `package.json`:

```json
{
  "scripts": {
    "format": "prettier --check .",
    "lint": "eslint .",
    "test": "jest",
    "build": "next build"
  }
}
```

---

## 📂 Workflow File Location

```
.github/workflows/kinship-studio-ci.yml
```

---

## 🔥 Summary

The Kinship Studio CI pipeline ensures that:

* Code is properly formatted
* Linting rules are followed
* Tests are successfully executed
* The application builds without errors

