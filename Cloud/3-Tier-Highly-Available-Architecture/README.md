# Design and Configuration of a Highly Available 3-Tier Architecture on AWS

---

## Project Overview

This project demonstrates the deployment of a highly available 3-tier architecture on AWS. 
The system separates the User/Presentation Tier, Application Tier, and Data Tier across multiple Availability Zones to ensure scalability and fault tolerance.

---

# Architecture Diagram

![Architecture Diagram](screenshots/Architecturediagram.png)

---

# Step 1: VPC Creation

Created a VPC with CIDR block 10.0.0.0/16.

![VPC Created](screenshots/vpc-created.png)

---

# Step 2: Subnet Configuration

Configured 6 subnets across 2 Availability Zones:

- 2 Public Subnets
- 2 Private Application Subnets
- 2 Private Database Subnets

![Subnets](screenshots/subnets.png)

---

# Step 3: Internet Gateway & Route Table

Configured Internet Gateway and updated Public Route Table.

![Public Route Table](screenshots/public-route-table.png)

---

# Step 4: NAT Gateway Configuration

Created NAT Gateway in Public Subnet and updated Private Route Table.

![NAT Gateway](screenshots/nat-gateway.png)

---

# Step 5: Security Groups

Configured separate security groups for:

- Load Balancer
- Application Servers
- Database

![App Security Group](screenshots/app-sg.png)

---

# Step 6: Application Tier Deployment

Launched EC2 instances in private subnets and installed Apache web server.

![EC2 Running](screenshots/ec2-running.png)

---

# Step 7: Load Balancer Configuration

Configured Application Load Balancer and attached Target Group.

![ALB Healthy](screenshots/alb-target-healthy.png)

---

# Step 8: RDS Multi-AZ Deployment

Deployed MySQL RDS instance with Multi-AZ enabled.

![RDS Multi AZ](screenshots/rds-multiaz.png)

---

# High Availability Features

- Multi-AZ Deployment
- Application Load Balancer
- Auto Scaling Group
- Multi-AZ RDS
- Private Subnet Isolation

---

# Security Considerations

- No public access to Application or Database tier
- Security groups follow least privilege principle
- NAT Gateway used for controlled outbound traffic
- Database accessible only from Application tier

