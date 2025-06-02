# Project-1-AWS
# Scalable Web App with ALB and Auto scaling 

Deployment of a Scalable Web Application on AWS

## Overview:
This example deploys a highly available, scalable, and monitored web application infrastructure on AWS. It leverages core AWS services like EC2, Application Load Balancer (ALB), Auto Scaling, RDS, IAM, CloudWatch, and SNS to ensure performance, resilience, and operational visibility.


## 🏗️ Architecture Diagram Description

This architecture is designed with **high availability**, **scalability**, and **security** in mind, leveraging core AWS services:

---

### 1. 🚪 Traffic Ingress via ALB
- An **Application Load Balancer (ALB)** is deployed in a **public subnet** to receive incoming HTTP traffic from the internet.
- It distributes traffic across **EC2 instances** to ensure load balancing and enhance user experience.

---

### 2. 💻 Highly Available EC2 Instances
- **EC2 instances** host the dynamic web application and are deployed across **two Availability Zones** to achieve fault tolerance.
- The instances are part of an **Auto Scaling Group (ASG)** with a **target tracking scaling policy** that adjusts capacity based on demand.
  - This helps maintain performance during traffic spikes and optimize costs during low usage.

---

### 3. 🔒 Security Groups
- **ALB security group** allows **inbound HTTP traffic** from the internet.
- **EC2 security group** allows **inbound traffic only from the ALB**, adding an extra security layer.

---

### 4. 🗄️ Resilient Database Layer
- An **Amazon RDS (PostgreSQL or MySQL)** instance is deployed in a **Multi-AZ configuration** to ensure high availability and automatic failover.

---

### 5. 🔐 Database Security
- A **dedicated security group** for the RDS instance allows **inbound access only from the EC2 security group**, limiting DB access to trusted app servers.

---

### 6. 🛡️ Secure Credential Management
- An **IAM role** is attached to EC2 instances to allow **secure access to credentials** stored in **AWS Secrets Manager**, ensuring proper role-based access control.

---

### 7. 📈 Monitoring and Alerts
- **Amazon CloudWatch** monitors key system metrics like **CPU and memory usage**.
- When thresholds are breached:
  - **CloudWatch Alarms** are triggered.
  - An **Amazon SNS topic** sends alert notifications to administrators, enabling **proactive incident management**.

---

![Architecture Diagram](https://github.com/user-attachments/assets/8902748b-d460-416b-90b6-0612299436e0)



# 🚀 AWS Infrastructure Setup Steps

## 🌐 1. Network Environment

- Create the required **Subnets**.
- Create an **Internet Gateway**.
- Create **Route Tables** and associate them accordingly.

![Subnets](https://github.com/user-attachments/assets/ec303bdc-effa-4b3e-a5ee-350d04e2cab0)

---

## 🔐 2. Security Configuration

### Security Groups

- **ALB Security Group**
  - Allow HTTP (port 80) and HTTPS (port 443) from anywhere.

- **EC2 Security Group**
  - Allow HTTP and HTTPS from **ALB-SG** only.

- **DB Security Group**
  - Allow HTTP, HTTPS, and MySQL/Aurora from **EC2-SG** only.

### IAM Role

Create an IAM Role with the following permissions:
- `SecretsManagerReadWrite`
- `AmazonEC2ReadOnlyAccess`
- `CloudWatchAgentServerPolicy`
- `AmazonSSMManagedInstanceCore`

![Security Groups](https://github.com/user-attachments/assets/1993d505-afaf-479c-823f-d726785941b1)

---

## 🧰 3. Launch Template

- **AMI**: Amazon Linux 2  
- **Instance Type**: `t2.micro`  
- **Security Group**: EC2-SG  
- **IAM Profile**: The role created above  
- **User Data**: Install Apache and deploy the application files

![Launch Template](https://github.com/user-attachments/assets/35d9c92e-2f75-431b-8279-395fa0f04823)

---

## ⚖️ 4. Application Load Balancer (ALB)

- Type: Application Load Balancer
- Scheme: Internet Facing
- Subnets: Private Subnet 1 and Private Subnet 2
- Security Group: ALB-SG

### Listener

- Create Target Group (Name: `application TG`)
- Select this TG during load balancer setup

![Load Balancer](https://github.com/user-attachments/assets/d32db238-3cec-4ffc-ac8a-e84dbb4e7f91)

---

## 📈 5. Auto Scaling Group (ASG)

- Use the previously created Launch Template
- Select Private Subnet 1 and Private Subnet 2
- Attach to the existing Load Balancer
- Configure group size and scaling as shown

![Scaling Policy](https://github.com/user-attachments/assets/2b324a79-d312-4744-bcf7-363d7f42575e)

- Enable CloudWatch group metrics
- Create the ASG → Two EC2 instances should be created

![Instances](https://github.com/user-attachments/assets/7e6f40a6-26ef-4e51-8434-2d80c33e5fd2)

---

## 🗄️ 6. Database Instance

- Create a Subnet Group (Private Subnet 3 and Private Subnet 4)
- Choose MySQL DB Engine
- Enable Multi-AZ Deployment (2 instances)
- Use default Secrets Manager integration
- Select the created Subnet Group the DB-SG.![DB](https://github.com/user-attachments/assets/44d040b0-76ef-4441-8c1a-f75de7fb75cb)

