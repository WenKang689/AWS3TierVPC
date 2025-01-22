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

## Step 7: Create Security Groups

## Step 8: Launch EC2 Instances

## Step 9: Add an Appliction Load Balancer

## Step 10: Set up RDS

## Step 11: Configure Auto Scaling Group
