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
<img width="927" height="150" alt="0" src="https://github.com/user-attachments/assets/a6882d45-dcd0-41de-bde6-c0b558f9142b" />
<img width="688" height="184" alt="0-0" src="https://github.com/user-attachments/assets/173524d0-9445-49bb-b9a8-939314d7fcd9" />

Each configuration file defines how to provision and manage infrastructure resources, and describe the desired state of your infrastructure. 

- **provider.tf**  : defines the AWS region and provider setup.
- **vpc.tf** : defines the VPC and subnets.
- **igw_nat.tf** : defines the Internet Gateway, Elastic IP, NAT Gateway.
- **routes.tf** : defines the Public & private route tables and their associations.
- **security_group.tf** : defines the inbound and outbound rules. It allows HTTP (80) and SSH (22) traffic
- **ec2.tf** : defines how to launch the EC2 instance and configure it with Nginx (user data)
- **outputs.tf** : contains information like Public IP of EC2, subnet IDs, etc.

## Step-by-Step Instructions : 
### Step 1: Configure the AWS Provider
- On your configuration file provider.tf , write the code below. This tells Terraform to use AWS as a provider and the region us-east-1 as a region where all the resources will be created.

<img width="643" height="107" alt="2" src="https://github.com/user-attachments/assets/3b91e86f-94d5-4f9b-86fe-047615c22cb0" />

### Step 2 : Create a VPC and Subnets
On your vpc.tf configuration file, add the code below. This will create a virtual network on AWS and configure subnets. It will assign a public IP address to your public subnet and use it for your EC2. The private subnet is not exposed to the internet.
<img width="753" height="398" alt="4" src="https://github.com/user-attachments/assets/3ea81ee5-99b8-4b96-9893-4cbc1af11549" />

### Step 3 : Set Up Internet Gateway and NAT Gateway
On your igw_nat.tf configuration file, add the code below. This tells Terraform to :
- Configure an Internet Gateway. It will allow the Public subnet to have access to the internet. 
- Set up an Elastic IP and a NAT Gateway. The NAT Gateway is used to allow outbound internet access from the private subnet, and reach indirect internet securely.
<img width="701" height="287" alt="6" src="https://github.com/user-attachments/assets/88ca9f27-9752-452c-ba15-f69b0f4381f7" />


### Step 4 : Create Route Tables and Associate Subnets
On your routes.tf configuration file, add the code below. This will configure public and private route tables for your VPC. 
- The Public Route Table will send all internet traffic (0.0.0.0/0) through the Internet Gateway. It allows direct internet access to the resources placed in the Public Subnet.
- The Private Route Table will send all internet traffic (0.0.0.0/0) through the NAT Gateway. It allows indirect internet access to the resources placed in the Private Subnet.
- The Public subnet uses IGW for internet access. The Private subnet uses NAT for secure, outbound-only access.
<img width="642" height="504" alt="8" src="https://github.com/user-attachments/assets/74203608-1e8a-4d94-85f6-584d8e76c89b" />

### Step 5 : Create a Security Group for EC2
On your security_group.tf configuration file, add the code below. This will configure a security group for the resources placed on the public subnet (EC2 Instance) in order to allow HTTP inbound traffic on port 80 and SSH connection on Port 22.
<img width="640" height="450" alt="10" src="https://github.com/user-attachments/assets/42165ffc-bc4f-439e-825a-945005bd7e2b" />

### Step 6 : Launch EC2 Instance and Install Nginx
- On your ec2.tf configuration file, add the code below. This will launch your EC2 Instance in the Public Subnet, associate it with the security group created previously and configure Nginx Web Server.
- The “user-data” feature will auto-installs Nginx, starts it, and shows a custom HTML message.

<img width="789" height="352" alt="12" src="https://github.com/user-attachments/assets/14f54610-f7b9-49ac-aff9-91fe1e21e6ef" />

### Step 7 : Output the Public IP
On your outputs.tf file, write the code below. The output.tf configuration file is optional but helpful, it allows users to understand the configuration and review its expected outputs.
<img width="683" height="236" alt="14" src="https://github.com/user-attachments/assets/cb810004-041b-4926-abba-6683c5ec5bad" />

### Step 8 : Deploy Your Infrastructure
Now that we have defined the configuration files, we will deploy the infrastructure.
- Run terraform init command : to Initialize the working directory/project, download the necessary plugins and prepare terraform.
<img width="623" height="308" alt="15" src="https://github.com/user-attachments/assets/b57eef6c-c338-49a0-8fe6-6562002227d9" />

- Run terraform plan command : to preview what changes will happen, before Terraform applies them.
<img width="536" height="126" alt="16" src="https://github.com/user-attachments/assets/dee6614e-c9fb-4385-b307-2bb4ef427934" />

**Error** : Running the command Terraform plan allows you to preview the changes as well as the errors you might encounter, before applying the code. 

**Troubleshooting** : to troubleshoot the “unsupported argument” error : 
- Go to the official website of HashiCorp : https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/eip#vpc
- Then : Terraform Registry -> Browse by Providers (or Modules) -> Select AWS -> Select your resource, then Check the most up to date Terraform code. 
<img width="560" height="256" alt="17" src="https://github.com/user-attachments/assets/5c600c1e-58c7-47b5-9732-1460f3065c0a" />

- Once the error is fixed, re-run the command terraform plan.
- Run terraform apply command : to apply the changes and build the infrastructure.
- Type yes when prompted.
<img width="1006" height="878" alt="24" src="https://github.com/user-attachments/assets/fb27da60-ac6f-4def-b15b-98fa6726a18c" />
<img width="760" height="836" alt="25" src="https://github.com/user-attachments/assets/46b2a692-6612-4c27-af85-0cd5d613fad7" />
<img width="634" height="855" alt="26" src="https://github.com/user-attachments/assets/4fcf39ae-1439-4262-8ef3-67d8b9d438f1" />
<img width="800" height="868" alt="27" src="https://github.com/user-attachments/assets/e0731d6d-a8fa-4548-ae32-8a388f55f7f9" />
<img width="717" height="865" alt="28" src="https://github.com/user-attachments/assets/70faa5ec-6de2-4904-a867-f445983a8278" />
<img width="690" height="867" alt="29" src="https://github.com/user-attachments/assets/4d2ddd97-8b27-4aaa-8e76-db3474439472" />
<img width="851" height="865" alt="30" src="https://github.com/user-attachments/assets/1e01fcd8-f101-4457-8dce-0c02779a6f2f" />

- Check your infrastructure on the AWS Console. The VPC was created as well as its components : 
- **VPC**
<img width="1448" height="787" alt="31" src="https://github.com/user-attachments/assets/5c633cbd-62e7-491c-9a3e-4059cc57edf7" />

- **Subnets : a Public subnet and a Private subnet**
<img width="1428" height="473" alt="32" src="https://github.com/user-attachments/assets/eda78521-41cb-4cd2-82bf-e686f9406d8c" />

- **Route Tables : a Private Route Table and a Public Route Table**
<img width="1436" height="459" alt="33" src="https://github.com/user-attachments/assets/7e5b28cc-db08-4c59-8a40-129537aca49d" />

- **Internet Gateway**
<img width="1451" height="686" alt="34" src="https://github.com/user-attachments/assets/c839e3a2-9d25-4d8f-8da4-bd527ca01a7c" />

- **Nat Gateway**
<img width="1439" height="695" alt="35" src="https://github.com/user-attachments/assets/ceaee763-b903-4f24-9591-391533b245c3" />

- **Security Group**
<img width="1440" height="739" alt="36" src="https://github.com/user-attachments/assets/2be87372-cd7f-4944-9bc6-40a3cd6908d2" />

### Step 9 : Open the Web Server in a Browser
- Test the Web Server in the browser using the Public IP address. Terraform will show the public IP in the output.
<img width="555" height="82" alt="37" src="https://github.com/user-attachments/assets/f4f19e81-197e-41c0-9da1-4ce732128d34" />

- Open your browser and go to: http://<your-public-ip> . You should expect this output : Lab 3 - Web Server Deployed with Terraform
<img width="1393" height="691" alt="38" src="https://github.com/user-attachments/assets/b752fd38-b527-4888-a153-bc06e3abfed5" />

### Step 10 : Destroy (Cleanup)
Now that your web server application was deployed successfully, clean up your infrastructure ! This prevents charges on your AWS account.
- Run terraform destroy command : to delete the infrastructure and remove all the resources managed by terraform.
- Type yes when prompted.
<img width="981" height="872" alt="39" src="https://github.com/user-attachments/assets/e8318147-bed0-470f-9bce-bf3c63e3c85b" />
<img width="943" height="874" alt="40" src="https://github.com/user-attachments/assets/0f498f7b-024f-45b0-968a-316a0717c224" />
<img width="832" height="874" alt="41" src="https://github.com/user-attachments/assets/e7b948d7-2b78-4713-924c-36ebcb18dc9d" />
<img width="825" height="852" alt="42" src="https://github.com/user-attachments/assets/3f284db2-f5ac-434c-897c-260720876447" />
<img width="882" height="870" alt="43" src="https://github.com/user-attachments/assets/8993d5e1-6e09-4055-9142-287c2dbf1531" />
<img width="960" height="861" alt="44" src="https://github.com/user-attachments/assets/e202d186-2ff4-4c89-ab5c-ace11aa5f670" />
<img width="845" height="865" alt="45" src="https://github.com/user-attachments/assets/0555afe1-a1b9-47c4-b151-8b0723194620" />
<img width="704" height="498" alt="46" src="https://github.com/user-attachments/assets/818bc2f9-7022-4753-a742-399da45e3572" />

- Check your infrastructure on AWS Console. The VPC was deleted as well as its components.
<img width="1453" height="534" alt="47" src="https://github.com/user-attachments/assets/a7690eea-78e4-46b8-901e-7b7f0a86a76c" />

### Summary
This breakdown provides a step-by-step guide to Build a Full Web Server Infrastructure on AWS using Terraform. By completing this lab, we had an overview on how to : 

- Build the infrastructure from scratch using a modular and production-style Terraform setup, from creating the VPC and configuring public and private subnets, to connecting to the internet using Internet Gateway and NAT Gateway, to understanding how routing works in AWS and what security best practices to set up with security groups.
- Organize Terraform code into separate files like in real-word production to keep the code clean, maintainable and reusable. Separating configuration files in blocks has several benefits like enhancing scalability, a better organization and team collaboration. It improves readability, reusability and maintainability of the code. It allows different teams to work on different parts of the project at the same time. Helps manage large and complex infrastructure, but also, promotes faster troubleshooting.

Building Infrastructure with Terraform as an IaC tool is an approach that not only provision, manage, and replicate cloud resources in a predictable and automated way. But also, reduces manual errors and enhances the scalability and maintainability of the infrastructure. This approach of building infrastructure using IaC, demonstrated how it can simplify and automate the process of deploying and managing a well-structured private network environment that requires high security and scalability and more complex cloud infrastructures. But also how Terraform lays the foundation for modern DevOps practices such as CI/CD, monitoring, and infrastructure testing.
