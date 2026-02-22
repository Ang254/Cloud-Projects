# Design and Configuration of Internet-Facing Load Balancer with Backend Instances

---

## Project Overview

This project demonstrates how to attach backend EC2 instances to an Internet-facing Application Load Balancer (ALB) on AWS.

The objective was to make application servers accessible to the internet through a secure and scalable load balancing layer while keeping backend instances controlled through security group rules.

---

## Architecture Diagram

![Architecture Diagram](screenshots/ArchD.png)

---

## Step 1: VPC and Subnet Setup

Created a VPC with the following components:

- Public Subnets (for Load Balancer)
- Private Subnets (for Backend EC2 instances)

While the project was deployed in a single Availability Zone, in practice, subnets should be distributed across multiple Availability Zones to achieve high availability and fault tolerance.

---

## Step 2: Security Group Configuration

Configured separate security groups to enforce isolation and least privilege.

Load Balancer Security Group
- Allow HTTP (Port 80) from 0.0.0.0/0
Acts as the public entry point to the application

Backend EC2 Security Group

Allow HTTP (Port 80) ONLY from the Load Balancer Security Group and SSH (Port 22)
No direct public internet access
Prevents direct access to backend servers

## Step 3: Launch Backend EC2 Instances

Launched EC2 instances in private subnets.

Installed Apache web server:

```bash
sudo dnf install httpd -y
sudo systemctl start httpd
sudo systemctl enable httpd```




## Step 4: Create Target Group

Created a Target Group with the following configuration:
- Target Type: Instance
- Protocol: HTTP
- Port: 80
- Registered backend EC2 instances to the Target Group.
Health checks ensure traffic is routed only to healthy instances.

## Step 5: Create Internet-Facing Application Load Balancer

Created an Application Load Balancer with:

Scheme: Internet-facing

Listener: HTTP (Port 80)

Attached to Public Subnets

Associated with the created Target Group

The Load Balancer distributes incoming traffic across multiple backend instances.

Step 6: Verify Load Balancing

Accessed the DNS name of the Load Balancer from a web browser.

Traffic was successfully distributed across backend instances, confirming:

Proper Target Group registration

Health checks functioning correctly

Secure communication between Load Balancer and backend servers

High Availability Features

Internet-facing Application Load Balancer

Backend instances deployed across multiple Availability Zones

Health checks for automatic traffic routing

Scalable architecture design

Security Considerations

Backend instances are not publicly accessible

Only the Load Balancer can communicate with backend servers

Principle of least privilege applied in security group rules

Public exposure limited strictly to the Load Balancer

Private subnet isolation for backend instances
