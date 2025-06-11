# day-one-sivanithin-ec2
##Deploying a Secure, Public Web Server on AWS
#Setup: AWS Key Pairnamed it as day-one-sivanithin-kp
in the private key format we choose .pem
![Screenshot (60)](https://github.com/user-attachments/assets/927a7ebe-b3eb-48ff-8aa0-624e7b40fcaf)
#Step 1: Create the Network Foundation (VPC)
vpc name:day-one-sivanithin-vpc
in IPv4 CIDR block we used 10.0.0.0/16 for  ~65,000 private IP addresses
![Screenshot (61)](https://github.com/user-attachments/assets/7a6433b9-a322-4e1e-a6e3-70c426e3ec06)
#Step 2: Create a Public Subnet
name:day-one-sivanithin-subnet
insubnet we choosed vpcid which we created earlier VPC:day-one-sivanithin-vpc 
availability zone should be same as default our zone
IPv4 CIDR block: 10.0.1.0/24 gives us 250 addresses
![Screenshot (62)](https://github.com/user-attachments/assets/8335a9a9-8050-4b42-a003-c08a109a6c5d)
#Step 3: Create an Internet Gateway (IGW)
name:day-one-sivanithin-internetgateway
after creating the gateway it will be detach mode so by selecting the gateway and we click on actions and attach it to our VPC  
![image](https://github.com/user-attachments/assets/4258511c-6110-411b-9c74-e3c4d6b8a42a)
#step 4:Configure a Public Route Table
i created a new route table named day-one-sivanithin-routetable
and added routes to table destination  `0.0.0.0/0` (This means "anywhere on the internet").
setting Target: Select **Internet Gateway** and then choose day-one-sivanithin-internetgateway
associated our table with public subnet day-one-sivanithin-subnet
![Screenshot (64)](https://github.com/user-attachments/assets/b8a6e340-aa3c-40e6-984a-d1bf3e92dbdf)
#Step 5: Create a Web Server Security Group
Security group name:day-one-sivanithin-securitygroup
Description:Allows HTTP and SSH access
VPC:Ensure day-one-sivanithin-vpc is selected.
Inbound rules:Click Add rule.
    - **Rule 1 (SSH):**
        - **Type:** SSH
        - **Source:** My IP (This automatically populates your current IP address for better security).
    - **Rule 2 (HTTP):**
        - **Type:** HTTP
        - **Source:** Anywhere-IPv4 (`0.0.0.0/0`)
![Screenshot (65)](https://github.com/user-attachments/assets/e2b0e380-d8b4-4481-8f0d-8fc1f7d5c247)
#Step 6: Launch the EC2 Instance (The Web Server)
name:day-one-sivanithin-ec2
Application os image:we choose Amazon Linux 2 AMI
Instance type:t2.micro
Key pair:day-one-sivanithin-kp
Network settings:
    VPC:day-one-sivanithin-vpc
    Subnet:day-one-sivanithin-subnet
    Auto-assign public IP:Enable
    Firewall (security groups):Select existing security group.
Advanced details we copy and pasted 
#!/bin/bash
yum update -y
yum install -y httpd
systemctl start httpd
systemctl enable httpd
echo "<h1>Hello World from $(hostname -f)</h1>" > /var/www/html/index.html
#step 7 : we launched the instance
![Screenshot (66)](https://github.com/user-attachments/assets/cdf9304f-2d0c-4d30-864d-3d853bef32c8)
#step8: we see a page that says: "Hello World from [your-instance's-private-hostname]".
![Screenshot (67)](https://github.com/user-attachments/assets/450d9d26-4ad4-42e3-adae-59719e0aa2b8)


