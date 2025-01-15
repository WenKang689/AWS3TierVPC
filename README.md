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

![image](https://github.com/user-attachments/assets/f88ba4f8-9c3e-46b4-9ff7-4188bea898d3)

6. Click "Create VPC".

## Step 2: Create Internet Gateway

## Step 3: Enable Auto-assign Public IP for Public Subnet

## Step 4: Create and Configure Public Route Table

## Step 5: Configure NAT Gateway

## Step 6: Create and Configure Private Route Table

## Step 7: Create Security Groups

## Step 8: Launch EC2 Instances

## Step 9: Add an Appliction Load Balancer

## Step 10: Set up RDS

## Step 11: Configure Auto Scaling Group
