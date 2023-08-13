
# Project Documentation: Modular and Scalable Virtual Network Architecture with Amazon VPC

## Table of Contents
1. [Introduction](#introduction)
2. [Prerequisites](#prerequisites)
3. [Pre-Deployment Setup](#pre-deployment-setup)
   - [Customize Application Dependencies](#customize-application-dependencies)
   - [Install and Configure CloudWatch Agent](#install-and-configure-cloudwatch-agent)
   - [Install AWS SSM Agent](#install-aws-ssm-agent)
4. [VPC Deployment](#vpc-deployment)
   - [Build VPCs](#build-vpcs)
   - [Create Subnets](#create-subnets)
   - [Create NAT Gateway](#create-nat-gateway)
   - [Create Transit Gateway](#create-transit-gateway)
   - [Internet Gateways and Route Tables](#internet-gateways-and-route-tables)
   - [Enable VPC Flow Logs](#enable-vpc-flow-logs)
5. [Bastion Host Deployment](#bastion-host-deployment)
   - [Create Security Group](#create-security-group)
   - [Deploy Bastion Host](#deploy-bastion-host)
6. [Application Servers Deployment](#application-servers-deployment)
   - [Create S3 Bucket](#create-s3-bucket)
   - [Create Launch Configuration](#create-launch-configuration)
   - [Create Auto Scaling Group](#create-auto-scaling-group)
   - [Create Target Group and Network Load Balancer](#create-target-group-and-network-load-balancer)
   - [Update Route53](#update-route53)
7. [Conclusion](#conclusion)

## 1. Introduction
This project aims to deploy a robust, modular, and scalable virtual network architecture using Amazon Virtual Private Cloud (VPC). By following best practices and a step-by-step approach, we will create a secure environment for hosting highly available and auto-scalable applications.

## 2. Prerequisites
Before proceeding with the deployment, ensure the following prerequisites are met:
- An active AWS account to create infrastructure resources on AWS cloud.

## 3. Pre-Deployment Setup
### 3.1. Customize Application Dependencies
SSH into the EC2 instance and install essential dependencies:
```bash
sudo yum update -y
sudo yum install -y httpd git

```


## 3.2. Install and Configure CloudWatch Agent
Follow AWS documentation to install and configure the CloudWatch Agent on the EC2 instance.

## 3.3. Install AWS SSM Agent
Follow AWS documentation to install the AWS Systems Manager Agent on the EC2 instance.

## 4. VPC Deployment
## 4.1. Build VPCs
Create two VPCs using AWS CLI:
```bash
vaws ec2 create-vpc --cidr-block 192.168.0.0/16
aws ec2 create-vpc --cidr-block 172.32.0.0/16
```

## 4.2. Create Subnets
Create public and private subnets across different availability zones in each VPC.

## 4.3. Create NAT Gateway
Create NAT Gateway in the public subnet of the first VPC:
```bash
aws ec2 create-nat-gateway --subnet-id <public-subnet-id> --allocation-id <eip-allocation-id>
```

## 4.4. Create Transit Gateway
Create a Transit Gateway and associate both VPCs:
```bash
aws ec2 create-transit-gateway
aws ec2 create-transit-gateway-vpc-attachment --transit-gateway-id <tgw-id> --vpc-id <vpc-id>
```
## 4.5. Internet Gateways and Route Tables
Create Internet Gateways for both VPCs and attach them.
Configure route tables to route traffic through NAT Gateway and Internet Gateway.

## 4.6. Enable VPC Flow Logs
Enable flow logs for both VPCs and push them to CloudWatch Log Groups:
```bash
aws ec2 create-flow-logs --resource-type VPC --resource-id <vpc-id> --traffic-type ALL
```
## 5. Bastion Host Deployment
## 5.1. Create Security Group
Create a security group for the bastion host:
```bash
aws ec2 create-security-group --group-name BastionSG --description "Bastion Security Group"
aws ec2 authorize-security-group-ingress --group-id <bastion-sg-id> --protocol tcp --port 22 --source <your-ip>/32
```
## 5.2. Deploy Bastion Host
Launch an EC2 instance for the bastion host in the public subnet.

## 6. Application Servers Deployment
## 6.1. Create S3 Bucket
Create an S3 bucket to store application configuration.

## 6.2. Create Launch Configuration
Create a launch configuration for your Auto Scaling Group.

## 6.3. Create Auto Scaling Group
Create an Auto Scaling Group with the launch configuration and desired settings.

## 6.4. Create Target Group and Network Load Balancer
Create a target group and a network load balancer in the public subnet.

## 6.5. Update Route53
Update Route53 hosted zone with a CNAME record routing the traffic to the NLB.

# 7. Conclusion
This project has successfully deployed a modular and scalable virtual network architecture using Amazon VPC. The architecture provides a robust foundation for hosting highly available and auto-scalable applications, while adhering to best practices for security and efficiency.
