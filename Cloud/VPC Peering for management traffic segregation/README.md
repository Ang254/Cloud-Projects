# Project: VPC Peering for Management Traffic Segregation

## Overview
This project demonstrates how to segregate management and application traffic in AWS using VPC peering. 

- Production (VPC-PROD) and Development (VPC-DEV) environments are isolated in separate VPCs.
- Private instances are shielded from the internet.
- Management and inter-VPC communication are routed securely via VPC peering.
- Public subnets allow controlled internet access where needed.

---

## Architecture Diagram
![Architecture Diagram](screenshots/P3.png)

---

## Components

- **Production VPC - VPC-PROD**
  - CIDR: `10.0.0.0/16`
  - Hosts bastion or jump servers for administrative access
  - Allows outbound access to peered VPCs
  - Private Subnet: EC2 instances, databases, internal services
  - Public Subnet: Bastion host or jump server
  - Internet Gateway (IGW) for outbound internet access (public subnet)

- **Development VPC - VPC-DEV**
  - CIDR: `20.0.0.0/16`
  - Public Subnet: Development servers
  - Internet Gateway (IGW)

- **VPC Peering**
  - Allows private communication between VPC-PROD and VPC-DEV
  - No traffic goes over the public internet
    
- **Route Tables**
  - VPC-PROD routes private traffic to VPC-DEV via peering
  - VPC-DEV routes traffic to VPC-PROD via peering
    
- **Security Groups**
  - Instances allow SSH only from management VPC or bastion
  - Public instances allow outbound internet traffic

---

## Implementation Steps

1. **Create VPCs**
![VPC Diagram](screenshots/P3.png)

2. **Create Subnets**
![Subnets](screenshots/P3.png)

3. **Create Internet gateways and Route tables**
- Created and attached IGWs to their VPCs
- Added routes to IGW in the route tables and associated the respective subnets
![RouteTable](screenshots/P3.png)

4. **Create Security group**
- Created security group allowing ssh traffic
![sg](screenshots/P3.png)

5. **Create EC2 Instances**
- Created a bastion host in the public subnet of VPC-Prod and added security group created.
- Created a private instance with an additional network interface in the private subnet of VPC-Prod and added security group created.

![PrivateEC2](screenshots/P3.png)
  
- Tested ssh into private instance from bastion host and only one IP was able to connect to private instance

![sshToPrivate](screenshots/P3.png)
