# day-one-sivanithin-ec2

## Deploying a Secure, Public Web Server on AWS

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
