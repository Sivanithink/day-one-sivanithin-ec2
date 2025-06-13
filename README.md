# day-one-sivanithin-ec2

## Deploying a Secure, Public Web Server on AWS(Class)

### Setup: AWS Key Pair

- Named it as `day-one-sivanithin-kp`
- In the private key format, we chose `.pem`

![Screenshot (60)](https://github.com/user-attachments/assets/927a7ebe-b3eb-48ff-8aa0-624e7b40fcaf)

### Step 1: Create the Network Foundation (VPC)

- VPC name: `day-one-sivanithin-vpc`
- IPv4 CIDR block: `10.0.0.0/16` (~65,000 private IP addresses)

![Screenshot (61)](https://github.com/user-attachments/assets/7a6433b9-a322-4e1e-a6e3-70c426e3ec06)

### Step 2: Create a Public Subnet

- Name: `day-one-sivanithin-subnet`
- VPC ID: selected the one created earlier - `day-one-sivanithin-vpc`
- Availability Zone: should be same as our default zone
- IPv4 CIDR block: `10.0.1.0/24` (gives ~250 addresses)

![Screenshot (62)](https://github.com/user-attachments/assets/8335a9a9-8050-4b42-a003-c08a109a6c5d)

### Step 3: Create an Internet Gateway (IGW)

- Name: `day-one-sivanithin-internetgateway`
- After creating, it will be in detached mode. Click on Actions and attach it to the VPC.

![image](https://github.com/user-attachments/assets/4258511c-6110-411b-9c74-e3c4d6b8a42a)

### Step 4: Configure a Public Route Table

- Created a new route table: `day-one-sivanithin-routetable`
- Added route with:
  - Destination: `0.0.0.0/0`
  - Target: select Internet Gateway → `day-one-sivanithin-internetgateway`
- Associated it with the public subnet: `day-one-sivanithin-subnet`

![Screenshot (64)](https://github.com/user-attachments/assets/b8a6e340-aa3c-40e6-984a-d1bf3e92dbdf)

### Step 5: Create a Web Server Security Group

- Name: `day-one-sivanithin-securitygroup`
- Description: Allows HTTP and SSH access
- VPC: `day-one-sivanithin-vpc`

Inbound rules:
- SSH:
  - Type: SSH
  - Source: My IP
- HTTP:
  - Type: HTTP
  - Source: Anywhere (0.0.0.0/0)

![Screenshot (65)](https://github.com/user-attachments/assets/e2b0e380-d8b4-4481-8f0d-8fc1f7d5c247)

### Step 6: Launch the EC2 Instance (The Web Server)

- Name: `day-one-sivanithin-ec2`
- OS Image: Amazon Linux 2 AMI
- Instance type: `t2.micro`
- Key pair: `day-one-sivanithin-kp`

Network settings:
- VPC: `day-one-sivanithin-vpc`
- Subnet: `day-one-sivanithin-subnet`
- Auto-assign Public IP: Enabled
- Security group: select existing → `day-one-sivanithin-securitygroup`

Advanced → User data script:

```bash
#!/bin/bash
yum update -y
yum install -y httpd
systemctl start httpd
systemctl enable httpd
echo "<h1>Hello World from $(hostname -f)</h1>" > /var/www/html/index.html
```

### Step 7: Launched the instance

![Screenshot (66)](https://github.com/user-attachments/assets/cdf9304f-2d0c-4d30-864d-3d853bef32c8)

### Step 8: Access the Web Server

We see a page that says:  
**"Hello World from [your-instance's-private-hostname]"**

![Screenshot (67)](https://github.com/user-attachments/assets/450d9d26-4ad4-42e3-adae-59719e0aa2b8)

<!-- ============================ -->
<!-- START OF TAKE-HOME ASSIGNMENT -->
<!-- ============================ -->


# Day 1 DevOps AWS Assignment – Two-Tier Architecture(Take Home Assignment)

## Assignment Title:
Building a Two-Tier Web Application Infrastructure on AWS

In this task, I had to build a basic two-tier architecture using AWS services. The setup includes a public-facing web server that handles user requests and a private database server that cannot be accessed from the internet. The main idea was to set up a secure and working environment with a VPC, subnets, route tables, security groups, and EC2 instances.

---

## Part 1: Network Foundation

- Created a custom VPC named `day-one-sivanithin-two-tier-vpc` with CIDR block `10.10.0.0/16`.
###VPC
![Screenshot (78)](https://github.com/user-attachments/assets/5d2c923a-1a7e-43ac-8459-95d1e5bfe614)
- Added two subnets:
  - Public Subnet: `10.10.1.0/24`
![Screenshot (80)](https://github.com/user-attachments/assets/9a46983a-9fe1-433b-b084-908add46a366)

  - Private Subnet: `10.10.2.0/24`
![Screenshot (81)](https://github.com/user-attachments/assets/e450e467-8588-4f8b-b5f2-ff33a7f83fac)

- Created an Internet Gateway and attached it to the VPC.
![Screenshot (82)](https://github.com/user-attachments/assets/edf78a8a-1bf4-4e8e-8ca3-467a7489fce1)


- Updated the main route table to send internet traffic (`0.0.0.0/0`) to the Internet Gateway and associated it only with the **public subnet**.

- As a result, only the public subnet has internet access, while the private subnet remains isolated.

###
---

## Part 2: Security Groups

- **day-one-sivanithin-webapp-sg**:
  - Allowed HTTP (port 80) from anywhere.
  - Allowed SSH (port 22) from my IP only.

- **day-one-sivanithin-database-sg**:
  - Allowed MySQL/Aurora (port 3306) only from the webapp-sg security group.
  - No internet access and no SSH allowed.


This ensures only the Web Server can communicate with the Database on port 3306, and the DB is completely private.

---

## Part 3: EC2 Instances

- Launched **Web Server** in the public subnet:
  - t2.micro
  - Assigned `day-one-sivanithin-webapp-sg`
  - Public IP enabled
  - Installed web server using user-data script
![Screenshot (89)](https://github.com/user-attachments/assets/40a4b258-a83c-429b-94c2-0e5cb4e36a27)
![Screenshot (86)](https://github.com/user-attachments/assets/b34cf62a-40c2-467c-a5c2-18b0bfb8d4b5)

- Launched **DB Server** in the private subnet:
  - t2.micro
  - Assigned `day-one-sivanithin-database-sg`
  - No public IP
![Screenshot (90)](https://github.com/user-attachments/assets/bfbca8fc-30b0-43f9-a1b0-46ac36677792)

---

## Part 4: Verification & Observations

- Accessed the Web Server from browser using its public IP – working fine.
![Screenshot (91)](https://github.com/user-attachments/assets/74599ca6-ba15-4275-ac4b-3fa04252586f)

- SSH into the Web Server using its public IP – successful.
- From Web Server, tried to **ping** the private IP of the DB Server:
  - This did **not work**, and I understood that this is due to ICMP not being allowed in the database security group.
  - Ping uses ICMP, but we only allowed TCP port 3306.
- Tried pinging `8.8.8.8` from the DB Server – **failed**, which is expected since it has no internet access.

---

## Important Note

Even though pinging the DB Server didn’t work, it doesn't mean the connection to MySQL is blocked. It just means ICMP is not allowed (which is fine in production environments). The main thing is that port 3306 is accessible from the Web Server, which meets the assignment’s requirements.

---

## What I Learned

- How to properly design a two-tier architecture using VPC, subnets, and EC2.
- The importance of keeping the database private and only allowing necessary ports.
- How security groups work and how to restrict traffic between resources.
- Understood how ICMP is different from TCP, and why ping might not work but other protocols can.
- Gained clarity on isolating networks and controlling access tightly.

---

## Deliverables

1. Screenshot of EC2 instances list showing both Web and DB servers.

![Screenshot (89)](https://github.com/user-attachments/assets/40a4b258-a83c-429b-94c2-0e5cb4e36a27)
![Screenshot (90)](https://github.com/user-attachments/assets/bfbca8fc-30b0-43f9-a1b0-46ac36677792)
2. Screenshot of `database-sg` showing port 3306 allowed only from `webapp-sg`.
![Screenshot 2025-06-12 222019](https://github.com/user-attachments/assets/3976fe83-43fc-4e26-b5ea-9df8fb2d0bf7)

![Screenshot (96)](https://github.com/user-attachments/assets/c934e27f-bc79-4740-aa7c-1f7f116546a8)

3. Screenshot of terminal showing SSH access into Web Server and attempt to ping DB Server.
## SSH Access and Ping Test from Web Server to DB Server

In this step, I successfully connected to the Web Server using SSH from my local machine with the public IP. After logging into the Web Server, I tried to ping the private IP address of the DB Server to test the connection between the two servers.

However, the ping command didn’t work as expected. I later understood that AWS blocks ICMP (ping) by default unless explicitly allowed in the Security Group. In this assignment, we only allowed MySQL (port 3306) traffic from the Web Server to the DB Server, not ICMP.

Even though ping failed, it doesn't mean the connection between the servers is broken. In real setups, web servers usually connect to the database using the MySQL port, not ping. So the architecture is still correct — the Web Server can access the DB Server on the right port, and the DB Server stays private without direct internet access.

4. Public IP of Web Server: 
![Screenshot (89)](https://github.com/user-attachments/assets/3f4795c7-e009-4c37-baf2-291e36a9a78d)

![Screenshot (88)](https://github.com/user-attachments/assets/c87fdc60-e623-4922-9f93-88b65534b5c0)


















