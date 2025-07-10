# End-to-End AWS Infrastructure Deployment with Terraform and GitHub Actions

## Overview

This project provisions and deploys a complete AWS infrastructure for a simple web application using **Terraform** (IaC) and **GitHub Actions** (CI/CD). It includes a VPC with public and private subnets, an EC2 instance running Dockerized NGINX, an RDS instance, and an ECR registry to store Docker images.

The application serves a basic “Hello World” webpage and showcases how to automate infrastructure deployment and application delivery using cloud-native tools.

## Architecture Summary
![image](https://github.com/user-attachments/assets/c67e7b15-1018-47db-ae24-5fa4c106e4c5)

**AWS Services Used:**
- VPC (public/private subnets)
- EC2 (for web server)
- RDS (private DB)
- ECR (Docker image registry)
- IAM, Security Groups

![image](https://github.com/user-attachments/assets/f221297f-4931-42d1-9805-d96def0e2354)

**CI/CD Workflow:**
- Push to `main` triggers pipeline
- Docker image is built and pushed to ECR
- Manual approval required
- SSH into EC2 to pull image and run container

## Infrastructure Breakdown

### 1. Networking (VPC)
- Custom VPC with:
  - **1 Public Subnet** (EC2)
  - **1 Private Subnet** (RDS)
- Configured for secure, segmented traffic flow

### 2. Compute (EC2 + Docker + NGINX)
- EC2 instance in the public subnet
- Runs a Docker container with NGINX serving a static HTML file
- HTTP (port 80) exposed via Security Group

### 3. Database (RDS)
- RDS instance in the private subnet
- Security Group allows access only from EC2 instance

### 4. Container Registry (ECR)
- Stores Docker image built in CI/CD pipeline
- Used to deploy application to EC2

## CI/CD Workflow (GitHub Actions)

**Path:** `.github/workflows/deploy.yml`

### Workflow Steps:
1. **Checkout Code**
   - `actions/checkout@v2`

2. **Docker Build**
   - Sets up Docker Buildx
   - Builds Docker image

3. **Push to ECR**
   - Authenticates to AWS using `aws-actions/configure-aws-credentials@v2`
   - Pushes Docker image to ECR

4. **Manual Approval**
   - Pauses workflow until manual approval is given

5. **Deploy to EC2**
   - SSH into EC2 instance
   - Pulls Docker image from ECR and runs NGINX container

## Infrastructure as Code with Terraform

### Key Features:
- Modular architecture using Terraform modules for:
  - VPC
  - EC2
  - RDS
  - ECR
- Multi-environment structure:
  - `env/dev`
  - `env/prod`
  - `env/stage`
- Version-controlled with GitHub for change tracking and rollback

## Project Structure

![image](https://github.com/user-attachments/assets/7373c6e8-b36d-4455-aa6c-62ac4979c862)


## Security and Secrets

GitHub Secrets used for secure access:
- `AWS_ACCESS_KEY_ID` and `AWS_SECRET_ACCESS_KEY`
- `AWS_ACCOUNT_ID` (for ECR access)
- `EC2_PUBLIC_KEY` (for SSH access)

Configured via `aws-actions/configure-aws-credentials`.

## Usage

### 1. Set Up GitHub Secrets
Add the following:
- `AWS_ACCESS_KEY_ID`
- `AWS_SECRET_ACCESS_KEY`
- `AWS_ACCOUNT_ID`
- `EC2_PUBLIC_KEY`

### 2. Trigger the Pipeline
Push changes to `main` branch. Approve manually when prompted.

### 3. Access the App
Visit the EC2 public IP in your browser to see the “Hello World” page.

## Tools & Technologies

- **Terraform**
- **AWS** (EC2, RDS, VPC, ECR)
- **GitHub Actions**
- **Docker**
- **NGINX**

## What This Project Demonstrates

- Reproducible, modular IaC using Terraform
- CI/CD automation with GitHub Actions
- AWS infrastructure deployment with best practices
- Security-conscious deployment using private/public subnet design
- Multi-environment support for dev/staging/prod

## Conclusion

This project shows how to build and deploy a production-style, container-based application using AWS infrastructure, IaC, and CI/CD practices. It can be used as a base for scaling up to more complex cloud-native applications.
