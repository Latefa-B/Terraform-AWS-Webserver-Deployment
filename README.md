# Step-by-Step Guide to Build a Full Web Server Infrastructure with Terraform
Building a Full Web Server Infrastructure using Terraform as an Infrastructure as Code (IaC) tool, is a powerful approach to provision and manage such a robust Cloud Infrastructure. Deploying and managing it can be complex and time-consuming, but Terraform enables us to automate the process, in a consistent, efficient and repeatable way, reducing human errors.
This comprehensive step-by-step guide walks you through the process of  building a Full Web Server Infrastructure  on AWS using a modular and production-style Terraform setup. In this project, we will be using AWS services like : VPC, Subnets, Route Tables, IGW, NAT Gateway, EC2 and Security Group. The aim of this project is to : 
- Build the infrastructure from scratch using a modular and production-style Terraform setup. The infrastructure consists of a private network and a web server.
- Organize Terraform code into separate files like in real-word production to keep the code  clean, maintainable and reusable.
- Understand how to define a custom VPC in Terraform’s Configuration files.

## Prerequisites 
In order to proceed to the lab, we need to make sure beforehand to : 
- Have Terraform and AWS CLI installed. 
- Have AWS credentials (aws configure) configured.
- Create a new working directory. Inside the folder, create the configuration files : provider.tf vpc.tf igw_nat.tf routes.tf security_group.tf ec2.tf outputs.tf


Each configuration file defines how to provision and manage infrastructure resources, and describe the desired state of your infrastructure. 

- **provider.tf**  : defines the AWS region and provider setup.
- **vpc.tf** : defines the VPC and subnets.
- **igw_nat.tf** : defines the Internet Gateway, Elastic IP, NAT Gateway.
- **routes.tf** : defines the Public & private route tables and their associations.
- **security_group.tf** : defines the inbound and outbound rules. It allows HTTP (80) and SSH (22) traffic
- **ec2.tf** : defines how to launch the EC2 instance and configure it with Nginx (user data)
- **outputs.tf** : contains information like Public IP of EC2, subnet IDs, etc.

## Step-by-Step Instructions : 
Step 1: Configure the AWS Provider

On your configuration file provider.tf , write the code below. This tells Terraform to use AWS as a provider and the region us-east-1 as a region where all the resources will be created.





Step 2 : Create a VPC and Subnets
On your vpc.tf configuration file, add the code below. This will create a virtual network on AWS and configure subnets. It will assign a public IP address to your public subnet and use it for your EC2. The private subnet is not exposed to the internet.







Step 3 : Set Up Internet Gateway and NAT Gateway
On your igw_nat.tf configuration file, add the code below. This tells Terraform to :
Configure an Internet Gateway. It will allow the Public subnet to have access to the internet. 
Set up an Elastic IP and a NAT Gateway. The NAT Gateway is used to allow outbound internet access from the private subnet, and reach indirect internet securely.


Step 4 : Create Route Tables and Associate Subnets
On your routes.tf configuration file, add the code below. This will configure public and private route tables for your VPC. 
The Public Route Table will send all internet traffic (0.0.0.0/0) through the Internet Gateway. It allows direct internet access to the resources placed in the Public Subnet.
The Private Route Table will send all internet traffic (0.0.0.0/0) through the NAT Gateway. It allows indirect internet access to the resources placed in the Private Subnet.
The Public subnet uses IGW for internet access. The Private subnet uses NAT for secure, outbound-only access.




Step 5 : Create a Security Group for EC2
On your security_group.tf configuration file, add the code below. This will configure a security group for the resources placed on the public subnet (EC2 Instance) in order to allow HTTP inbound traffic on port 80 and SSH connection on Port 22.


Step 6 : Launch EC2 Instance and Install Nginx
On your ec2.tf configuration file, add the code below. This will launch your EC2 Instance in the Public Subnet, associate it with the security group created previously and configure Nginx Web Server.
The “user-data” feature will auto-installs Nginx, starts it, and shows a custom HTML message.



Step 7 : Output the Public IP
On your outputs.tf file, write the code below. The output.tf configuration file is optional but helpful, it allows users to understand the configuration and review its expected outputs.


Step 8 : Deploy Your Infrastructure
Now that we have defined the configuration files, we will deploy the infrastructure.
Run terraform init command : to Initialize the working directory/project, download the necessary plugins and prepare terraform.

Run terraform plan command : to preview what changes will happen, before Terraform applies them. 
Error : Running the command Terraform plan allows you to preview the changes as well as the errors you might encounter, before applying the code. 


Troubleshooting the Error : to troubleshoot the “unsupported argument” error, got the official website of HashiCorp Terraform Registry -> Browse by Providers (or Modules) -> Select AWS -> Select your resource, then Check the most up to date Terraform code. 
https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/eip#vpc


Once the error is fixed, re-run the command terraform plan.







Run terraform apply command : to apply the changes and build the infrastructure.
Type yes when prompted.















Check your infrastructure on the AWS Console. The VPC was created as well as its components : 

VPC



Subnets : a Public subnet and a Private subnet.


Route Tables : a Private Route Table and a Public Route Table

Internet Gateway


Nat Gateway


Security Group

Step 9 : Open the Web Server in a Browser

Test the Web Server in the browser using the Public IP address. Terraform will show the public IP in the output.



Open your browser and go to: http://<your-public-ip> . You should expect this output : Lab 3 - Web Server Deployed with Terraform
Step 10 : Destroy (Cleanup)
Now that your web server application was deployed successfully, clean up your infrastructure ! This prevents charges on your AWS account.
Run terraform destroy command : to delete the infrastructure and remove all the resources managed by terraform.
Type yes when prompted.








Check your infrastructure on AWS Console. The VPC was deleted as well as its components.


Summary
This breakdown provides a step-by-step guide to Build a Full Web Server Infrastructure on AWS using Terraform. By completing this lab, we had an overview on how to : 

Build the infrastructure from scratch using a modular and production-style Terraform setup, from creating the VPC and configuring public and private subnets, to connecting to the internet using Internet Gateway and NAT Gateway, to understanding how routing works in AWS and what security best practices to set up with security groups.
Organize Terraform code into separate files like in real-word production to keep the code clean, maintainable and reusable. Separating configuration files in blocks has several benefits like enhancing scalability, a better organization and team collaboration. It improves readability, reusability and maintainability of the code. It allows different teams to work on different parts of the project at the same time. Helps manage large and complex infrastructure, but also, promotes faster troubleshooting.
Building Infrastructure with Terraform as an IaC tool is an approach that not only provision, manage, and replicate cloud resources in a predictable and automated way. But also, reduces manual errors and enhances the scalability and maintainability of the infrastructure. This approach of building infrastructure using IaC, demonstrated how it can simplify and automate the process of deploying and managing a well-structured private network environment that requires high security and scalability and more complex cloud infrastructures. But also how Terraform lays the foundation for modern DevOps practices such as CI/CD, monitoring, and infrastructure testing.
