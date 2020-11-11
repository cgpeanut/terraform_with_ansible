Deploying to AWS with Terraform and Ansible

Description
You were recently hired as an Infrastructure Automation Engineer at a SaaS company.
The company is trying to move away from cloud-provider-specific infrastructure as code.
They want to test out Terraform for infrastructure deployment as it is cloud agnostic
and Ansible as it is OS agnostic and also a hybrid IaC tool.

Your first task is to use Terraform and Ansible to deploy a distributed Jenkins CI/CD
pipeline and put it behind one of the company's DNS domains for testing.

It sounds easy enough but there's quite some planning which will go into this and
you're already on top of it.

Objectives

# Log in to the Terraform Controller Node EC2 Instance

ssh cloud_user@<IP-OF-TERRAFORM-CONTROLLER>

terraform version

# Clone the GitHub Repo for Terraform Code

git clone https://github.com/linuxacademy/content-deploying-to-aws-ansible-terraform.git

1. View the contents of backend.tf to view the backend information for storing state files.

# Configure the S3 bucket

1. Create the S3 bucket, providing a unique bucket name:

aws s3api create-bucket --bucket <UNIQUE_BUCKET_NAME>

2. Copy the unique bucket name after it is successfully created. Edit the backend.tf file
and replace "<name-of-already-created-bucket>" for the bucket variable with your unique
bucket name. Save and close file

# Create an SSH Key Pair

1. Create the key pair, pressing Enter three times after running the command to i
accept the defaults:

ssh-keygen -t rsa

# Deploy the Terraform Code

1. Initialize the Terraform directory you changed into to download the required provider:

terraform init
terraform fmt
terraform validate
terraform plan
terraform apply

After terraform apply has run successfully, you can use the AWS CLI on the Controller
node to list, describe created resources, and additionally also log in to the AWS Console
to verify and investigate created resources.

Finally, on the Terraform Controller node CLI, execute terraform destroy and enter yes
when prompted to delete all resources which were created and ensure that it runs through
successfully.

# Test Out Your Deployment

1. Test out the URL of your website returned in Terraform outputs.

In a new web browser tab, navigate to the URL provided in the results of the terraform
apply command run previously. Use the following Jenkins credentials:

Username: admin
Password password

2. Changing the workers count by modifying workers-count variable in the variables.tf
file and ensure that Terraform apply is successful.

Click Manage Jenkins from the menu on the left of the page.
Click Manage Nodes and Clouds from the System Configuration section.
From your terminal, edit the variables.tf file and increase the workers-count variable 
from 1 to 0.

vim variables.tf

Run terraform apply again to apply this configuration change. Enter yes when prompted.

Back in your web browser, refresh the page to verify the number of worker nodes is updated.