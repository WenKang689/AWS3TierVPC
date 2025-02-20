# AWS 3-Tier VPC

This is a hands on project creating a 3-Tier Virtual Private Cloud(VPC) in Amazon Web Services(AWS) using AWS Management Console.

# Architecture Overview

This is an overview diagram showing the 3-Tier VPC Architecture.

![3TierArchitecture](https://github.com/user-attachments/assets/3656370c-684a-46a2-be0d-8fa7b995cd02)

This diagram shows the key components of the architecture, including:

Public and Private subnets across two Availability Zones
Internet Gateway for public internet access
NAT Gateways for outbound internet access from private subnets
Application Load Balancer in the public subnets
EC2 instances in each tier
RDS instance in the private subnet

# Building 3-Tier VPC in AWS Step-by-step 

## Step 1: Create an VPC

1. Navigate to the VPC dashboard in the AWS Management Console
2. Click "Create VPC"
3. Choose "VPC and more"
4. Configure your VPC settings:
   
Name tag: My3TierVPC

IPv4 CIDR block: 10.0.0.0/16

Number of Availability Zone: 2

![image](https://github.com/user-attachments/assets/7f33f773-b582-41f9-8f4f-f2e3fcbe95d5)

Number of Public Subnet: 2

Number of Private Subnet: 4

![image](https://github.com/user-attachments/assets/d13cbec5-673b-4cb8-8bdc-1988ae8f8fab)

NAT Gateways: 1 per AZ

VPC Endpoints: S3 Gateway

DNS options: Opt in Enable DNS Hostnames and Resolution

![Image](https://github.com/user-attachments/assets/eee17304-905f-40f6-aced-81cbe1304678)

6. Click "Create VPC".

## Step 2: Enable Auto-assign Public IP for Public Subnet

1. In the VPC Dashboard, navigate to "Subnets"
2. Select 1 of your public subnets

![Image](https://github.com/user-attachments/assets/66d27411-29bc-407c-986e-195900ef68f8)

3. Click "Actions" and choose "Edit subnet settings"

![Image](https://github.com/user-attachments/assets/02b04b7d-78b6-4465-8f03-63c14ff5c943)

4. Check the box for "Enable auto-assign public IPv4 address"
5. Click "Save"

![image](https://github.com/user-attachments/assets/e1c3d26d-29d5-44e2-afb2-b2f6062b1976)

6. Repeat this step on second public subnet.

## Step 3: Create Security Groups

1. In the VPC dashboard, navigate to "Security Groups"
2. Click "Create security group"

![image](https://github.com/user-attachments/assets/2c16eba1-792c-40d3-b7f8-ec3c26f2f722)

3. Create the following security groups:

a. Web Tier Security Group:

Name: Web-SG

Description: Allow HTTP/HTTPS from anywhere

VPC: Select your VPC

Inbound rules:

Allow HTTP (80) from anywhere

Allow HTTPS (443) from anywhere

Outbound rules:

Allow all traffic

![image](https://github.com/user-attachments/assets/56eef424-5986-4471-a26f-26d151c8771b)


b. Application Tier Security Group:

Name: App-SG

Description: Allow traffic from Web tier

VPC: Select your VPC

Inbound rules:

Allow SSH from Web-SG

Outbound rules:

Allow all traffic

![image](https://github.com/user-attachments/assets/eb37147a-0879-4320-8190-9ce2afce4607)


c. Database Tier Security Group:

Name: DB-SG

Description: Allow traffic from App tier

VPC: Select your VPC

Inbound rules:

Allow MySQL/Aurora (3306) from App-SG

Outbound rules:

Allow all traffic


![image](https://github.com/user-attachments/assets/d2be57f5-a772-40b5-888d-5863352e2151)


4. Click "Create security group" for each.

## Step 4: Add an Appliction Load Balancer

Adding an Application Load Balancer (ALB) distributes incoming application traffic across multiple targets, such as EC2 instances, in multiple Availability Zones. This improves the application's fault tolerance.

1. Navigate to the EC2 dashboard and select "Load Balancers"

![image](https://github.com/user-attachments/assets/752f850b-4253-4869-a252-bac32d16f036)

2. Click "Create Load Balancer" and choose "Application Load Balancer"

![image](https://github.com/user-attachments/assets/0c25f8e1-90ea-4993-bb09-fe318abc3bb0)

![image](https://github.com/user-attachments/assets/b401cb60-999b-4079-8d40-dc861c2e0149)

3. Basic Configurations

Load Balancer Name: ALB

Scheme: Internet-facing

IP Address Type: IPv4

![image](https://github.com/user-attachments/assets/24cd7963-d363-48cb-ad98-7408d960a250)

4. Network Mapping

Select your VPC, and select at least two public subnets from different AZs

![image](https://github.com/user-attachments/assets/f9b4c5f8-9223-4560-89c8-ba119c8aa931)

5. Security Groups

Create a new security group for the ALB:

Name: ALB-SG

Description: Allow HTTP and HTTPS from anywhere

Inbound rules: Allow HTTP (80) and HTTPS (443) from anywhere

Outbound rules: Allow all traffic

![image](https://github.com/user-attachments/assets/ccbfb583-2b19-4a48-a866-0bca91702fba)

![image](https://github.com/user-attachments/assets/c7e2d0f4-cd7a-45c7-a23d-46aa198f12a7)

Click "Create Security Group", and select the security group at ALB page.

6. Listeners and Routing

Protocol: HTTP

Port: 80

Click "Create Target Group".

![image](https://github.com/user-attachments/assets/cd43c062-d0f7-49c1-8022-1b5748fafeb4)

Create a target group:

Target Type: Instances

Name: TG

Protocol: HTTP

Port: 80

![image](https://github.com/user-attachments/assets/b9d950a4-005e-4f89-8fc9-4521d66e0a7c)

Select your VPC and configure health checks as needed. Click Next.

![image](https://github.com/user-attachments/assets/a35dcee4-dbad-4b5d-9778-d7d28a4558ff)

Skip register targets. Click "Create Target Group".

![image](https://github.com/user-attachments/assets/0081fb3d-1e80-43c0-9600-25a17fe89291)

7. Navigate back to Create ALB page. Refresh and select the TG created.

![image](https://github.com/user-attachments/assets/b9e688cd-6607-4410-b06f-d2ac6c05520c)

8. Click "Create Load Balancer".

9. After creating Application Load Balancer, we need to edit the APP-SG security group to allow traffic from Application Load Balancer.

10. Navigate to EC2 dashboard. Select security group and select App-SG. Click "Edit Inbound Rules".

![image](https://github.com/user-attachments/assets/e316e433-c8db-4dc2-8f60-a602f8b88159)

11. Add a new rule, allowing HTTP from ALB security group. Click "Save Rules".

![image](https://github.com/user-attachments/assets/23980cdf-6722-4879-a511-4f334041ecf8)


## Step 5: Configure Auto Scaling Group

1. Navigate to the EC2 dashboard and select "Auto Scaling Groups". Click "Create Auto Scaling Group".

![image](https://github.com/user-attachments/assets/6470e9ca-3cf4-43c6-9835-d0dd6002f8eb)

2. Enter the auto scaling group name and click "Create a Launch Template".

![image](https://github.com/user-attachments/assets/7f6b8408-3358-4600-ac1b-fa125b7b8528)

3. Enter the template name and description.

![image](https://github.com/user-attachments/assets/392e7ab3-7e2a-4247-afdc-b60d5d9f4703)

AMI: Amazon Linux 2023 AMI

![image](https://github.com/user-attachments/assets/e2b821d5-a0eb-4142-9975-2b145110d3e4)

5. Instance Type: t3.micro. Create a new key pair.

![image](https://github.com/user-attachments/assets/5b590fbf-fa8f-4c8b-a1ff-88066ce16e96)

Name: Web-EC2-KP

Key Pair Type: RSA

Private Key File Format: .pem

Click "Create key pair".

![image](https://github.com/user-attachments/assets/d671e8a6-b796-4423-a473-31f5485e59b7)

6. Select the key pair created.

Subnet: Do not include in launch template

Security Groups: App-SG

![image](https://github.com/user-attachments/assets/72bb3481-6346-42dc-83d6-d2bccd1f798c)

7. Expand Advanced Details and paste the following user data:

```Bash
#!/bin/bash
# Update the system
yum update -y

# Install Apache (httpd)
yum install -y httpd

# Start the Apache service
systemctl start httpd

# Enable Apache to start on boot
systemctl enable httpd

# Create a simple health check page with the AZ variable
echo "<h1>Hello World from $(hostname -f)</h1>" > /var/www/html/index.html
```

![image](https://github.com/user-attachments/assets/e92b7a6b-d862-4029-adc9-eae0e61fc17e)

8. Click "Create Launch Template".

9. Navigate back to create auto scaling group page. Refresh and select the launch template created. Click next.

![image](https://github.com/user-attachments/assets/b746175e-dc67-474a-8e91-1e7877af0cf5)

10. Select your VPC. Select a private subnet that represents app tier in each availability zone. Click next.

![image](https://github.com/user-attachments/assets/68a7f2b4-bdb3-4c0d-bff3-8548c0a305a2)

11. Select "Attach to an existing load balancer". Select "Choose from application or network load balancer target groups". Select the target group.

![image](https://github.com/user-attachments/assets/1f71453a-81e1-4089-9e58-614e57966fab)

12. Turn on Elastic Load Balancing Health Checks and click next.

![image](https://github.com/user-attachments/assets/5d482160-fe34-498f-9f18-b3363287ece3)

13. Adjust the scaling policy.

![image](https://github.com/user-attachments/assets/c466292f-474d-453e-8c65-9947cf96217e)

14. Enable group metrics collection within CloudWatch and click next.

![image](https://github.com/user-attachments/assets/c04b84c1-2bff-46d5-a579-d8f1c6c9c353)

15. Click "Create Auto Scaling Group".

![image](https://github.com/user-attachments/assets/ce291e77-52e6-4a4a-b95a-ff6c3e7a46c3)

## Step 6: Set up RDS

1. Navigate to RDS Dashboard. Select "Databases" and click "Create Database".

![image](https://github.com/user-attachments/assets/c198defc-1c6f-4a3f-b89f-c78d2ca49c5d)

2. Select "Standard Create". Select MySQL database and MySQL Community Edition.

![image](https://github.com/user-attachments/assets/eb85caaa-18c7-422f-bbbf-4198fbaf16eb)

DB Instance Size: Free Tier

DB Instance Identifier: DB-3TVPC

Master Username: WK

Opt-in Auto Generate Password.

![image](https://github.com/user-attachments/assets/97973e22-a7ee-4644-96a4-0da1869b6883)

3. Select your VPC, and select DB-SG as security group. Click "Create Database".

![image](https://github.com/user-attachments/assets/9db46468-9300-40d3-8fc6-37afba5f1bb8)

4. While waiting the database creation, click "View Credential Details" above to view and copy your master password.

![image](https://github.com/user-attachments/assets/f80f57ca-c6c9-4244-85b8-e9e3760ce70c)

4. After the RDS created, select the subnet groups. Select the subnet group created. Click "Edit".

![image](https://github.com/user-attachments/assets/22105495-3cc9-4637-8aad-fac652a2f968)

5. Only select the private subnet for database. Click "Save".

## Step 7: Update Security Group

Since EC2 Instances will receive traffic from Application Load Balancer, we need to modify security group for EC2 Instances created in private subnet.

1. Navigate to EC2 Instance dashboard and select Security Groups. Select App-SG and click "Edit inbound rules"

![image](https://github.com/user-attachments/assets/27acc1eb-1016-4cd7-aecd-b3775aa28b16)

2. Change the source to ALB-SG to allow traffic from Application Load Balancer. Click "Save Rules".

![image](https://github.com/user-attachments/assets/66fc1d4c-fa0c-4d93-94ee-a77e54790f7e)

## Step 8: Create Bastion Host

We need a bastion host in order to SSH into EC2 instances in private subnet for testing later.

1. Navigate to EC2 dashboard, Select "Instances" and click "Launch Instances".

![image](https://github.com/user-attachments/assets/72cbe7c3-41b2-4d2c-9ac4-bc3d13a05d02)

2. Name the instance as Bastion and select Amazon Linux 2023 AMI

![image](https://github.com/user-attachments/assets/c23301bd-ae3d-4f13-92f2-5eb499a48378)

Instance Type: t3.micro

Key Pair: Web-EC2-KP

![image](https://github.com/user-attachments/assets/90af419c-0e43-4c7b-83d3-7fbe05b12191)

3. Select you VPC, any public subnet, and Web-SG security group.

![image](https://github.com/user-attachments/assets/5f6bdebb-d5f6-4347-893d-e36a335e4530)

4. Launch the instance.


# Testing 3-Tier VPC

This is a step-by-step guide to test the 3-Tier VPC involves validating the functionality and connectivity of all the components.

## Step 1: Verify Public Subnet and Internet Accessibility

1. Open command prompt. Change directory to where key pair downloaded.

![image](https://github.com/user-attachments/assets/406cb927-1384-4571-8df0-a5a4df916297)

2. SSH into bastion host in public subnet. Change the <public ip address of bastion host> according to your instance.

```
ssh -i Web-EC2-KP.pem ec2-user@<public ip address of bastion host>
```

![image](https://github.com/user-attachments/assets/ba69780f-1632-47e1-9c8a-59445f36fc0b)

5. Try to ping any website to ensure Internet Connectivity.

```
ping google.com
```

![image](https://github.com/user-attachments/assets/1bd0d92a-6712-4d32-ab79-abaaa3a999e9)

6. Press CTRL+C to pause the ping.


## Step 2: Verify Private Subnet Accessibility

1. Open command prompt. Change directory to where key pair downloaded.

![image](https://github.com/user-attachments/assets/406cb927-1384-4571-8df0-a5a4df916297)

2. Add the key pair of private EC2 instance.

```
ssh-add App-EC2-KP.pem
```

![image](https://github.com/user-attachments/assets/e9da618f-b2c2-44e0-a574-6f11661cbbea)

3. SSH into bastion host in public subnet with agent forwarding. Change the <public ip address of bastion host> according to your instance.

```
ssh -A -i Web-EC2-KP.pem ec2-user@<public ip address of bastion host>
```

![image](https://github.com/user-attachments/assets/e5117467-0e12-46c9-b0a4-24d3ffb5a17f)

4. SSH into EC2 instance in private subnet.Change the <private ip address of private EC2 instance> according to your instance.

```
ssh ec2-user@<private ip address of private EC2 instance>
```

![image](https://github.com/user-attachments/assets/902c3d2a-c1cf-41f6-a22b-5c9b870ceb96)

5. Try to ping any website to ensure Internet Connectivity.

```
ping google.com
```

## Step 3: Testing Application Load Balancer (ALB)

1. Navigate to EC2 dashboard. Select Load Balancers. Copy the DNS Name of the Application Load Balancer.

![image](https://github.com/user-attachments/assets/8a2eb020-159c-4b64-9cee-61f6d598582d)

2. Open a new tab and paste the URL. Press enter, and you will see the same page if Application Load Balancer successfully to send traffic to your private EC2 instances.

![image](https://github.com/user-attachments/assets/dc9d0cc1-e5cd-4e93-ace1-0f2743e38c87)

3. Refresh the page. The IP address shown in the page should be change, which means that the Application Load Balancer is working.

![image](https://github.com/user-attachments/assets/5141407e-a446-4786-ab90-a242f27e2d31)

## Step 4: Validating RDS Connectivity

1. Before that, we need to install mysql client in private EC2 instance. Firstly, open command prompt and ssh into private EC2 instance as the step above.

2. Next, run the following command. Replace <Username> with your master username.

```
sudo dnf install https://dev.mysql.com/get/mysql84-community-release-el9-1.noarch.rpm -y

sudo dnf makecache

sudo dnf install mysql-community-server -y

sudo systemctl enable mysqld --now

mysql -h db-3tvpc.c5mme2eiedw7.ap-southeast-5.rds.amazonaws.com -P 3306 -u <Username> -p

```

3. Enter the password you copied before.

![image](https://github.com/user-attachments/assets/f2cd932b-b0ea-45b5-bfaf-8dc67581b8ec)

![image](https://github.com/user-attachments/assets/a840580d-4be8-4252-bd88-977f05bc88e5)

![image](https://github.com/user-attachments/assets/5c27ea0c-62fc-45c5-a362-d9875fa978d5)

4. Try some sql queries here.

```
SHOW DATABASES;
```

![image](https://github.com/user-attachments/assets/c9731798-4638-410c-880f-0d1a04c9c4c9)


## Step 5: Verifying Auto Scaling Group

1. Open command prompt and ssh into private EC2 instance as the step above.

2. Run the following command. Replace <ALB-DNS-Name> with your ALB DNS name.

```
ab -n 1000000 -c 10000 http://<ALB-DNS-Name>/
```

![image](https://github.com/user-attachments/assets/96dbf696-b294-471e-8355-3c9ea9a29390)

2. Navigate to EC2 Dashboard. Select Auto Scaling Groups and choose your auto scaling group.

![image](https://github.com/user-attachments/assets/c8bd27ed-4d72-4c02-94c1-8bd40a600b8b)

3. Click "Activity" tab. Monitor the auto scaling tab until it launching new instances.

![image](https://github.com/user-attachments/assets/6e6add6e-f28e-4c70-920d-09bc854714ae)

4. If ASG not scaling out, try to run the command few more times until the ASG scales out.
