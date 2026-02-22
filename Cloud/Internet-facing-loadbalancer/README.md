Design and Configuration of Internet-Facing Load Balancer with Backend Instances
Project Overview

This project demonstrates how to attach backend EC2 instances to an Internet-facing Application Load Balancer (ALB) on AWS.

The objective was to make application servers accessible to the internet through a secure and scalable load balancing layer while keeping backend instances controlled through security group rules.

Architecture Diagram

Step 1: VPC and Subnet Setup

Created a VPC with both:

Public Subnets (for Load Balancer)

Private Subnets (for Backend EC2 instances)

Ensured subnets were distributed across multiple Availability Zones for high availability.

Step 2: Launch Backend EC2 Instances

Launched EC2 instances.

Installed Apache:

sudo dnf install httpd -y
sudo systemctl start httpd
sudo systemctl enable httpd

Configured simple test web page:

echo "Backend Server 1" | sudo tee /var/www/html/index.html
Step 3: Security Group Configuration

Configured separate security groups:

Load Balancer Security Group

Allow HTTP (Port 80) from 0.0.0.0/0

Backend EC2 Security Group

Allow HTTP (Port 80) only from Load Balancer Security Group

No direct public access

Step 4: Create Target Group

Created a Target Group:

Target Type: Instance

Protocol: HTTP

Port: 80

Health Check Path: /

Registered backend EC2 instances to the Target Group.

Step 5: Create Internet-Facing Application Load Balancer

Created an Application Load Balancer with:

Scheme: Internet-facing

Listeners: HTTP (Port 80)

Attached to Public Subnets

Linked to created Target Group

Step 6: Verify Load Balancing

Accessed the DNS name of the Load Balancer from a browser.

Traffic was distributed across backend instances, confirming successful attachment.

High Availability Features

Internet-facing Application Load Balancer

Backend instances across multiple Availability Zones

Health checks for automatic traffic routing

Secure communication via security group referencing

Security Considerations

Backend instances are not publicly accessible

Only Load Balancer can communicate with backend servers

Principle of least privilege applied in security group rules

Public exposure limited to Load Balancer only
