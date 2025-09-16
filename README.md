# Chydigitalcoud Enterprise AWS Cloud Network

# **Design and Deployment of a Virtual Network + MediCare Architecture Using AWS VPC and Deploying a Website on the Network**

## Project Overview

This project focuses on the design and deployment of a secure, scalable virtual network architecture on **Amazon Web Services (AWS)** using **Virtual Private Cloud (VPC)**. The goal was to simulate a production-ready environment, configure networking components, apply security best practices, and host a static website using **NGINX** on an **EC2 Ubuntu server**, connected with a custom domain.

## Objectives

- Design a **custom AWS VPC** with public and private subnets.
- Configure **routing**, **internet gateways**, **NAT gateways**, and **network ACLs** for traffic control.
- Set up a **Linux EC2 instance** as a web server and deploy a static website.
- Attach a **custom domain** purchased via **Namecheap**, integrated with **AWS Route 53** and verified via **DNSChecker**.
- Apply **network security best practices** using security groups and NACLs.
- Version control infrastructure/configurations using **Git** and **GitHub**.
- Document the entire process with potential use in future deployments or client presentations.

### Tools & Technologies Used

| **Tool / Service** | **Purpose / Description** |
| --- | --- |
| **AWS VPC** | Creation of isolated virtual network infrastructure |
| **Subnets** | Segregation of resources into public and private zones |
| **Internet Gateway (IGW)** | Provides internet access for public subnet resources |
| **Route Tables** | Controls routing between subnets and internet |
| **NACLs (Network ACLs)** | Stateless access control at the subnet level |
| **Security Groups** | Stateful firewall for EC2 instance access control |
| **EC2 (Ubuntu Linux Server)** | Hosting environment for web server (NGINX) |
| **MobaXterm** | SSH access to EC2 instance from a Windows environment |
| **NGINX** | Lightweight web server to host static website content |
| **Git & GitHub** | Version control and source code repository |
| **Namecheap** | Domain name registration |
| **DNSChecker.org** | DNS propagation verification tool |
| **AWS Console** | Web interface for configuring and managing AWS services |

## Architecture Diagram

![image.webp](attachment:19bea9d6-b37d-47cb-b5cf-6d1383d7cd60:image.webp)

## Implementation Steps

## **VPC Creation**

- Created a custom VPC
- CIDR block `10.0.0.0/16`.
- Name tag: Chydigitalcloud-Enterprise
- Tenancy: Default

![vpc.png](attachment:03c0bf7c-2a83-41cb-ab5f-94c7722c4d50:vpc.png)

## Create  Subnets

**Subnet Design**

- Public Subnet: `10.0.1.0/24`
- Private Subnet: `10.0.2.0/24`

| **Name** | **CIDR** | **AZ** | **Publi**c IP? |
| --- | --- | --- | --- |
| Chydigital-public | 10.0.1.2/24 | N.Virginia | Yes |
| Chydigital-private | 10.0.2.0/24 | N.Virginia | No |
- **Auto-assign public IPv4**: ✅ for public, ❌ for private

![subnets.png](attachment:719d5619-59c9-4c06-8f1a-6d35fce341cf:subnets.png)

### 3. Internet Gateway

- **Name tag:** Chydigital-IGW
- Attach to :Chydigitalcloud-Enterprise

![igw.png](attachment:ecf8d41c-7be7-482b-b1eb-4619230c4130:igw.png)

## 4. Route Tables

**Route-Table-Pod8**

- **Associations:** Chydigital-public
- **Associations:** chydigital-private

## **Routes:**

- 0.0.0/0 → IGW
- **(10.0.0.0/16)        Local-RT**

**Public Subnet Association**

 Linked the public subnet to a route table with an Internet Gateway (IGW), enabling internet access.

**Security Note** This setup exposes resources to the public internet ideal only for services meant to be externally accessible (e.g., web servers). Access should be tightly controlled.

![route table.png](attachment:ad285631-0a59-4e69-bc6d-c968c590487f:route_table.png)

![RT asso.png](attachment:c45974cf-8b57-4ac1-a514-93a73f24d83e:RT_asso.png)

![igw to rt.png](attachment:95397076-a873-48f3-a1dc-d638bc7817b8:igw_to_rt.png)

## 5. Network ACLs & Security Groups

### Network ACLs

- **Public-NACL** (Chydigital-security guide)

**Inbound:**

- Rule 100: SSH (22) == allowed from 0.0.0.0/0
- Rule 110: HTTP (80) == allowed from 0.0.0.0/0
- Rule 120: HTTPS (443) == allowed from 0.0.0.0/0
- Rule 130: Custom TCP 1024 - 65535 == allowed from 0.0.0.0/0

**Outbound:**

- Same as Inbound All → 0.0.0/0
- Default deny all or restrict as needed

Note:  Used **Network ACLs** for stateless subnet-level traffic control ( Best Practice)

![Nacl In.png](attachment:41345d6b-6b0a-4c26-a355-406a88f72ae2:Nacl_In.png)

![Nacl out.png](attachment:b82a0323-3654-4cd4-977f-849f8d690702:Nacl_out.png)

## 6. Security Groups

Applied least-privilege security group rules for EC2 and SSH access.

- **Windows: Chydigital-security-Grp**
- VPC: Chydigitalcloud-Enterprise

**Inbound Rules**

- HTTP (80) & HTTPS (443) → 0.0.0/0
- SSH 22 → *Your IP*
- 

**Outbound Rules**

- All traffic → 0.0.0/0

Note: SSH was restricted to my IP only to reduces exposure to brute-force attacks or unauthorized access attempts. By narrowing access to a known, trusted source, it adds a critical layer of security, ensuring that only the intended administrator can remotely access the server.

Best Practice: Always avoid open SSH access (0.0.0.0/0) in production environments.

![sec grp.png](attachment:df358500-c07d-4e6f-860c-c2c67e72bf38:sec_grp.png)

![sec grp in.png](attachment:2f6a6433-e04e-4da3-a8d0-ee0c4c177b2e:sec_grp_in.png)

![sec grp out.png](attachment:6feb6705-b4a4-4d59-a243-d8411f8e32fc:sec_grp_out.png)

# 7.EC2 Launch & Web server Configuration

- Name: Chydigital-Web_server
- Subnet: Public Subnet
- Auto-assigned public IP Address - Enabled
- Key Pair: Created and download new key per.pem file

Note: Never share or exposed your Key per

![ec2.png](attachment:72397c35-e701-4fb0-9ea8-169ae617c577:ec2.png)

![ec2 run.png](attachment:325091b0-12e4-4b6b-a3f6-36a45879843c:ec2_run.png)

## 8. Access and Configure The EC2 Web Server

**Access**

Used **MobaXterm** to SSH into the server.

- Use MobaXterm
- load your .pem key
- SSH to Public IP

![project2.15.webp](attachment:0fb908a2-3d56-4495-87e5-98697ab6d4c3:project2.15.webp)

**Configuration of EC2 Web server & Deployment**

Installed and configured **NGINX**

- Install web server and Git:
- sudo apt update -y
- sudo apt install nginx -y
- sudo apt install git -y

Clone and deploy site:
git clone https://github.com/digitalwitchdemo/mediplus.git

- cd mediplus
- sudo mv ./* /var/www/html
- sudo systemctl restart nginx

![sudo upgrade.png](attachment:6db5397a-2b3f-43a0-8c9a-fdcbe82d01ae:sudo_upgrade.png)

![nginx active.png](attachment:8e281656-088b-427a-a1c0-e14b216c414a:nginx_active.png)

![p.png](attachment:a129fa2a-82a3-46b5-8fc5-dccb1f592844:p.png)

1. **Verify**
- Browse to http://18.204.44.57/#
- Should see MediCare homepage

## Domain Name Integration

1. **Purchase Domain**
- Domain: chydigitalcloud.online
1. Configured DNS records to point to EC2 Public IP
- Create an A Record
1. Verified using **DNSChecker.org**

![deployed site.png](attachment:13cecaad-5d54-4cc9-8063-a94583b6a648:deployed_site.png)

![A record.png](attachment:6ec9acc2-b367-4b25-859f-23aa719b8b76:A_record.png)

![dns.png](attachment:18ff6470-0e7f-414c-9767-30c0e25fb4d4:dns.png)

![live.png](attachment:502d2af6-5621-42c1-b0dc-24fd77cf715d:live.png)

## Security Best Practices Applied

- Separated public/private subnets to isolate resources.
- Implemented **least privilege** in Security Groups:
    - Port 22 (SSH): Restricted to a specific IP.
    - Port 80 (HTTP): Open to all.
- Disabled unused services on the server.
- Used **Network ACLs** for stateless subnet-level traffic control.
- Routinely updated Ubuntu packages for security patches.
- Maintained Git version control to monitor changes.

## Challenges Encountered

- **DNS Propagation Delay**: Took time for domain records to reflect globally.
- **Security Rule Conflicts**: Initial misconfiguration in NACLs blocked desired traffic.
- **Ubuntu Firewall Rules**: Needed to allow HTTP/HTTPS explicitly.
- **SSL Configuration**: HTTPS setup was deferred due to complexity in DNS challenges

## Lessons Learned

- Proper VPC planning is foundational for cloud architecture.
- Security Group and NACL configurations require careful testing.
- Documentation during deployment saves time during troubleshooting.
- Domain verification can vary by provider—knowing DNS tools helps.
- Git is crucial not just for code, but also for version-controlling infrastructure files and notes.

## Final Notes

This project was an insightful hands-on simulation of real-world cloud network infrastructure design. It reinforces the need for strong fundamentals in networking and security in cloud engineering roles. This will form the basis for future automation and scaling tasks using **Infrastructure as Code (IaC)** tools such as **Terraform**.

© 2025 Chioma C. Okechukwu-Amaefule

All rights reserved.
