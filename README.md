# AWS 3-Tier VPC

This is a hands on project creating a 3-Tier Virtual Privare Cloud(VPC) in Amazon Web Services(AWS) using AWS Management Console.

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

Allow custom TCP (e.g., 8080) from Web-SG

Outbound rules:

Allow all traffic

![image](https://github.com/user-attachments/assets/a13bbfc6-1070-474a-a298-c429d31e9ab8)


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

Name: Web-EC2-KP

Key Pair Type: RSA

Private Key File Format: .pem

Click "Create key pair".

![image](https://github.com/user-attachments/assets/5b590fbf-fa8f-4c8b-a1ff-88066ce16e96)

6. Select the key pair created.

Subnet: Do not include in launch template

Security Groups: App-SG

7. Click "Create Launch Template".

![image](https://github.com/user-attachments/assets/a88bb438-d9e4-4200-9797-ac80ea973fca)

8. Navigate back to create auto scaling group page. Refresh and select the launch template created. Click next.

![image](https://github.com/user-attachments/assets/b746175e-dc67-474a-8e91-1e7877af0cf5)

9. Select your VPC. Select a private subnet that represents app tier in each availability zone. Click next.

![image](https://github.com/user-attachments/assets/68a7f2b4-bdb3-4c0d-bff3-8548c0a305a2)

10. Select "Attach to an existing load balancer". Select "Choose from application or network load balancer target groups". Select the target group.

![image](https://github.com/user-attachments/assets/1f71453a-81e1-4089-9e58-614e57966fab)

11. Turn on Elastic Load Balancing Health Checks and click next.

![image](https://github.com/user-attachments/assets/5d482160-fe34-498f-9f18-b3363287ece3)

12. Adjust the scaling policy.

![image](https://github.com/user-attachments/assets/c466292f-474d-453e-8c65-9947cf96217e)

13. Enable group metrics collection within CloudWatch and click next.

![image](https://github.com/user-attachments/assets/c04b84c1-2bff-46d5-a579-d8f1c6c9c353)

14. Click "Create Auto Scaling Group".

![image](https://github.com/user-attachments/assets/ce291e77-52e6-4a4a-b95a-ff6c3e7a46c3)

## Step 6: Set up RDS

1. Navigate to RDS Dashboard. Select "Databases" and click "Create Database".

![image](https://github.com/user-attachments/assets/c198defc-1c6f-4a3f-b89f-c78d2ca49c5d)

2. Select "Easy Create". Select MySQL database and MySQL Community Edition.

![image](https://github.com/user-attachments/assets/3bb56a00-4af6-4623-a947-622f2a89f87e)

DB Instance Size: Free Tier

DB Instance Identifier: DB-3TVPC

Master Username: WK

Opt-in Auto Generate Password. Click create database.

![image](https://github.com/user-attachments/assets/d3ffa431-4770-4eab-bc71-d4516f1247b3)

3. While waiting the database creation, click "View Credential Details" above to view your master password.

![image](https://github.com/user-attachments/assets/f80f57ca-c6c9-4244-85b8-e9e3760ce70c)


# Testing 3-Tier VPC

This is a step-by-step guide to test the 3-Tier VPC involves validating the functionality and connectivity of all the components.

## Step 1: Verify Public Subnet and Internet Accessibility

## Step 2: Verify Private Subnet Accessibility

## Step 3: Testing Application Load Balancer (ALB)

## Step 4: Testing Security Groups and Network ACLs

## Step 5: Validating RDS Connectivity

## Step 6: Verifying Auto Scaling Group

## Step 7: Testing Monitoring and Logging
