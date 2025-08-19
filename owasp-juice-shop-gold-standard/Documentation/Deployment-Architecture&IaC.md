# Deployment Architecture & IaC

**Document Version:** 1.0  
**Status:** Final  
**Author:** Abhishek Shrivastav  
**Date:** August 12, 2025  

---

## 1. Deployment Architecture Overview

The OWASP Juice Shop will be deployed to **Amazon Web Services (AWS)** using a simple, scalable, and modern architecture. The core of the application runs as a Docker container, managed by an **EC2 instance** within an **Auto Scaling Group (ASG)** for resilience. An **Application Load Balancer (ALB)** serves as the single entry point for all user traffic, providing SSL termination and routing requests to the application instance.

This architecture is designed to mimic a typical small- to medium-sized cloud deployment. User identities are managed via **IAM password policies**.

---

### 1.1 Architecture Diagram

```mermaid
graph TD
    subgraph "Internet"
        User[End User]
    end
    subgraph "AWS Cloud"
        subgraph "VPC (10.0.0.0/16)"
            subgraph "Public Subnet (10.0.1.0/24)"
                ALB[Application Load Balancer]
            end
            subgraph "Private Subnet (10.0.2.0/24)"
                ASG[Auto Scaling Group]
                EC2[EC2 Instance <br> Docker + Juice Shop]
            end
            S3[S3 Bucket <br> (for file uploads)]
        end
    end
    User -- HTTPS (443) --> ALB
    ALB -- HTTP (3000) --> EC2
    EC2 -- Read/Write --> S3
```

**Key Architectural Components:**

* **VPC:** Isolated virtual network to host all resources.
* **Public Subnet:** Hosts the ALB, accessible from the internet.
* **Private Subnet:** Hosts EC2 instances, only accessible via ALB.
* **Application Load Balancer:** Terminates HTTPS traffic and routes HTTP requests.
* **Auto Scaling Group:** Ensures resilience and availability.
* **EC2 Instance:** Runs Docker with Juice Shop container.
* **S3 Bucket:** Stores file uploads (e.g., "Contact Us" form).

---

## 2. CI/CD Pipeline Overview

Deployment is automated via a **CI/CD pipeline**:

1. Application is built and packaged into a Docker container.
2. Secrets (like DB credentials) are managed via a **private .tfvars repo**.
3. CI/CD system clones the repo at build time and injects secrets.
4. Docker image is deployed to the AWS infrastructure.

---

## 3. Infrastructure-as-Code (IaC) - Terraform

The entire infrastructure is defined using **Terraform**, acting as the single source of truth.

### Example Terraform Snippets

```hcl
# Provider & Region
provider "aws" {
  region = "ap-south-1"
}

# VPC
resource "aws_vpc" "juice_shop_vpc" {
  cidr_block = "10.0.0.0/16"
  tags = { Name = "JuiceShop_VPC" }
}

# Public Subnet
resource "aws_subnet" "public_subnet" {
  vpc_id                  = aws_vpc.juice_shop_vpc.id
  cidr_block              = "10.0.1.0/24"
  map_public_ip_on_launch = true
  tags = { Name = "JuiceShop_Public_Subnet" }
}

# Private Subnet
resource "aws_subnet" "private_subnet" {
  vpc_id     = aws_vpc.juice_shop_vpc.id
  cidr_block = "10.0.2.0/24"
  tags = { Name = "JuiceShop_Private_Subnet" }
}

# Internet Gateway
resource "aws_internet_gateway" "gw" {
  vpc_id = aws_vpc.juice_shop_vpc.id
  tags = { Name = "JuiceShop_IGW" }
}

# Security Groups (ALB + App)
resource "aws_security_group" "alb_sg" {
  name   = "juice-shop-alb-sg"
  vpc_id = aws_vpc.juice_shop_vpc.id

  ingress { from_port = 443 to_port = 443 protocol = "tcp" cidr_blocks = ["0.0.0.0/0"] }
  egress  { from_port = 0   to_port = 0   protocol = "-1"  cidr_blocks = ["0.0.0.0/0"] }
}

resource "aws_security_group" "app_sg" {
  name   = "juice-shop-app-sg"
  vpc_id = aws_vpc.juice_shop_vpc.id

  ingress { from_port = 3000 to_port = 3000 protocol = "tcp" cidr_blocks = ["0.0.0.0/0"] description = "App traffic" }
  ingress { from_port = 22   to_port = 22   protocol = "tcp" cidr_blocks = ["0.0.0.0/0"] description = "SSH access" }
  egress  { from_port = 0    to_port = 0    protocol = "-1"  cidr_blocks = ["0.0.0.0/0"] }
}

# S3 Bucket
resource "aws_s3_bucket" "uploads_bucket" {
  bucket = "juice-shop-user-uploads-unique-name"
  acl    = "public-read"
}

# IAM Role + Policy
resource "aws_iam_role" "ec2_role" {
  name = "juice-shop-ec2-role"
  assume_role_policy = jsonencode({
    Version = "2012-10-17"
    Statement = [{ Action = "sts:AssumeRole", Effect = "Allow", Principal = { Service = "ec2.amazonaws.com" } }]
  })
}

resource "aws_iam_policy" "s3_policy" {
  name        = "juice-shop-s3-policy"
  description = "Policy for EC2 to access S3"
  policy = jsonencode({
    Version = "2012-10-17"
    Statement = [{ Action = "s3:*", Effect = "Allow", Resource = "*" }]
  })
}
```

---

## 4. Summary

This architecture demonstrates a **realistic AWS deployment** using **Docker, Terraform, ALB, ASG, and S3**. By codifying infrastructure and automating deployments with CI/CD, the system is **scalable, resilient, and reproducible**.
