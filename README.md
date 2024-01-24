**DEPLOYMENT GUIDELINE FOR BainCapital AWS STAGING ENVIRONMENT**



**Table of Contents** 

1. **Introduction**
1. **Prerequisite**
   1. **AWS CLI Installation**
   1. **Credentials Setup**
   1. **Terraform Installation**
1. **Infrastructure Deployment**
   1. **Directory Structure**
   1. **Execution Steps**
1. **Continuous Integration - Continuous Deployment**
   1. **Execution Steps**
1. **Conclusion**







1. **Introduction:**
   1. This document serves as a comprehensive resource to assist you in deploying and managing the infrastructure of BainCapital Staging Environment. The deployment process is orchestrated through Terraform, a powerful Infrastructure as Code (IaC) tool, and a Continuous Integration/Continuous Deployment (CI/CD) pipeline.
   1. Here, the infrastructure is created and managed using Terraform, which provisions the necessary cloud resources. Once the infrastructure is set up, the deployment process is automated using AWS CodePipeline. This CodePipeline acts as a continuous integration and continuous deployment (CI/CD) tool, orchestrating various stages of the deployment workflow.
   1. Amazon EKS, being the crucks of the infrastructure, is used to manage containerized applications, utilizing resources like Pods and ELBs. The containerized application, deployed within EKS, communicates seamlessly with essential AWS services such as RDS, ElastiCache, and OpenSearch for database, caching, and analytics functionalities, ensuring a robust and scalable deployment.



1. **Prerequisite**
   1. Admin Access for the AWS Account with Management Console access.
   1. Access to Terraform Codebase.
   1. AWS CLI
      1. Download and run MSI Installer for Windows 
         1. <https://awscli.amazonaws.com/AWSCLIV2.msi> 
      1. For Linux
         1. curl "https://awscli.amazonaws.com/awscli-exe-linux-x86\_64.zip" -o "awscliv2.zip"
         1. unzip awscliv2.zip
         1. sudo ./aws/install
         1. Confirm the installation: aws --version
   1. Access key & Secret Access Key (With Administrator Access) to configure with Terraform.
      1. Run the below command in powershell/command line to set the credentials for Terraform
         1. SET AWS\_ACCESS\_KEY\_ID=<you access key here>
         1. SET AWS\_SECRET\_ACCESS\_KEY=<your secret access key here>
         1. ![](Aspose.Words.c96d2bb7-5465-4157-a993-54e1cfd84523.001.png)
   1. Terraform setup in local system
      1. Installation Steps
         1. To install Terraform, find the appropriate package for your system and download it as a zip archive from the link below.
            1. <https://developer.hashicorp.com/terraform/install>
         1. After downloading Terraform, unzip the package. Terraform runs as a single binary named terraform. Any other files in the package can be safely removed and Terraform will still function.
         1. Extract the zip file to a location in your C drive.
         1. Updating the GLOBAL PATH Variable: Click on the Start menu and search for Settings. Open the Settings app.
         1. Select the System icon, then on the left menu, select the About tab. Under Related settings on the right, select Advanced system settings. 

1. ![](Aspose.Words.c96d2bb7-5465-4157-a993-54e1cfd84523.002.png)


1. On the System Properties window, select Environment Variables.
1. Select the PATH variable, then click Edit.
1. Click the New button, then type in the path from Step 3 where the Terraform executable is located. In the screenshot below, this is C:\terraform.

1. ![](Aspose.Words.c96d2bb7-5465-4157-a993-54e1cfd84523.003.png)

1. Click OK three times to exit and save the settings.
1. Verify the Installation
1. ![](Aspose.Words.c96d2bb7-5465-4157-a993-54e1cfd84523.004.png)
1. For Debian/Ubuntu 
   1. wget -O- https://apt.releases.hashicorp.com/gpg | sudo gpg --dearmor -o /usr/share/keyrings/hashicorp-archive-keyring.gpg
   1. echo "deb [signed-by=/usr/share/keyrings/hashicorp-archive-keyring.gpg] https://apt.releases.hashicorp.com $(lsb\_release -cs) main" | sudo tee /etc/apt/sources.list.d/hashicorp.list
   1. sudo apt update && sudo apt install terraform
1. If you are using a different Linux distribution such as CentOS or Fedora, please adhere to the provided instructions.
   1. <https://developer.hashicorp.com/terraform/install>




1. **Infrastructure Deployment**
   1. **Directory Structure**
      1. ![](Aspose.Words.c96d2bb7-5465-4157-a993-54e1cfd84523.005.png)

![](Aspose.Words.c96d2bb7-5465-4157-a993-54e1cfd84523.006.png)

1. Terraform project is organized using a modular structure for clarity and maintainability. Each module is encapsulated with its own set of Terraform files variable values (`terraform.tfvars`), and backend configuration. Here's a brief explanation of each file type:
   1. **provider.tf:** This file typically encompasses both the provider configuration and backend settings, including specifications for remote state.
   1. **main.tf:** This file contains the main Terraform code for the respective module
   1. **data.tf:** This file is used for fetching required data and remote state from the remote backend needed for the module. 
   1. **output.tf:** This file contains the definitions for the outputs of Terraform resources. 
   1. **variable.tf:** This file contains variable declarations for your module. 
   1. **terraform.tfvars:** This file is used to store values for the variables defined in `variable.tf`. 

Contents of the variables.tf file are

**vpc\_name** - Name for the Virtual Private Cloud (VPC) to be created.

**vpc\_cidr** -   CIDR block that defines the IP address range for the VPC.

**azs**  - List of Availability Zones to be used for distributing resources within the VPC.

**private\_subnets** - List of private subnets within the VPC where backend resources will be deployed.

**public\_subnets - List of public subnets within the VPC for resources that require public internet access.**

**Tags- Tags to be applied to AWS resources, providing metadata for organization and identification.**

**eks\_cluster\_name - Name for the Amazon EKS (Elastic Kubernetes Service) cluster to be created within the VPC.**

1. **Execution Steps**
   1. Navigate to the root directory of the main project folder using the terminal.
   1. You can see the “modules” directory. This directory contains Terraform code for respective services.
   1. **Initialize and Apply the VPC:**
      1. Change directory to `module/vpc` within the BainCapital Project.
      1. Run `terraform init` to initialize the VPC module.
      1. Run `terraform apply -auto-approve` to create the Virtual Private Cloud (VPC) and associated resources.
      1. Follow the steps as highlighted in pink color in below image.
      1. ![](Aspose.Words.c96d2bb7-5465-4157-a993-54e1cfd84523.007.png)

1. **Initialize and Apply the Security group:**
   1. Change directory to `module/sg` within the BainCapital Project.
   1. Run `terraform init` to initialize the sg module.
   1. Run `terraform apply -auto-approve` to create the Security Group and associated resources.
1. **Initialize and apply the S3:**
   1. Change directory to `module/s3` within the BainCapital Project.
   1. Run `terraform init` to initialize the s3 module.
   1. Run `terraform apply -auto-approve` to create the S3 and associated resources.
1. **Initialize and Apply the ECR:**
   1. Change directory to `module/ecr` within the BainCapital Project.
   1. Run `terraform init` to initialize the ecr module.
   1. Run `terraform apply -auto-approve` to create the Elastic Container Registries and associated resources.
1. **Initialize and Apply the RDS:**
   1. Change directory to `module/rds` within the BainCapital Project.
   1. Run `terraform init` to initialize the rds module.
   1. Run `terraform apply -auto-approve` to create the RDS and associated resources.
1. **Initialize and Apply the Opensearch:**
   1. ` `Change directory to `module/opensearch ` within the BainCapital Project.
   1. ` `Run `terraform init` to initialize the opensearch module.
   1. ` `Run `terraform apply -auto-approve` to create the Opensearch and associated resources.
1. **Initialize and Apply the Elasticache:**
   1. Change directory to `module/elasticache` within the BainCapital Project.
   1. Run `terraform init` to initialize the elasticache module.
   1. Run `terraform apply -auto-approve` to create the elasticache and associated resources.
1. **Initialize and Apply the EKS:**
   1. Change directory to `module/eks` within the BainCapital Project.
   1. Run `terraform init` to initialize the eks module.
   1. Run `terraform apply -auto-approve` to create the eks and associated resources.

1. **Continuous Integration - Continuous Deployment**
   1. **Execution Steps**
      1. This pipeline is set to automatically initiate upon the merging of any changes into the staging branch of the repository.
      1. Manual Execution for manual execution, you can initiate the process by clicking on "Release Change" in the CodePipeline console.
         1. Navigate to the codepipeline console and select the pipeline you want to execute.
      1. ![](Aspose.Words.c96d2bb7-5465-4157-a993-54e1cfd84523.008.png)
**



1. **Conclusion**
   1. Throughout this Document We have covered critical aspects such as 
      1. **Prerequisites:**  A checklist of prerequisites for both Terraform infrastructure setup and manual pipeline creation
      1. **Infrastructure Deployment:** The steps needed to be performed in order to spin up AWS resource via Terraform.

