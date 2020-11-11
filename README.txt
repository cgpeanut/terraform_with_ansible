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

git clone https://github.com/cgpeanut/terraform_with_ansible.git

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

# Additional Resources: <<< --- >>>

1. You'll be spinning up a Jenkins Master node (EC2) in us-east-1 and a Jenkins Worker (EC2)
   in us-west-2. Both these nodes will need to communicate so you'll need to set up appropriate
   VPC peering, route table entries, and security group rules to allow that.

   The Jenkins Master node's webserver listens on port 8080/tcp, and the Jenkins Worker will
   need to be able communicate with the Jenkins Master on all ports over TCP.

   You've been provided the following details for the network setup:

   * us-east-1 VPC CIDR: 10.0.0.0/16
   * us-west-2 VPC CIDR: 192.168.0.0/16
   * us-east-1 VPC will have 2 subnets: 10.0.1.0/24, 10.0.2.0/24
   * us-west-2 VPC will have 1 subnet: 192.168.1.0/24
   * Both VPC's will have internet gateways attached so that instances can have internet access

2. Ansible Playbooks, Jinja templates for Ansible, and a plaintext file containing the username
   and password for your Jenkins Application have been provided to you at the following URLs:

   a. Jenkins Master Ansible Playbook
   b. Jenkins Worker Ansible Playbook
   c. Ansible Jinja template for Jenkins Worker Setup
   d. Ansible Jinja template for Jenkins Worker credential setup
   e. Plaintext Jenkins Auth file

3. You've been advised to create a directory named ansible_templates inside your project folder and
maintain all Ansible related Playbooks, Jinja templates, and inventory configuration in this
directory.

4. You've further been advised to create another directory inside the ansible_templates directory
called inventory_aws and store the Ansible dynamic inventory fetching config file there. The URL
to the file is: Ansible Inventory Config for AWS

5. You can fetch the publicly-hosted domain name provided by your company by issuing the following
command:

aws route53 list-hosted-zones | jq -r .HostedZones[].Name | egrep "cmcloud*"

6. You will need to create an Application Load Balancer with 2 listeners:

a. Port 443 (HTTPS)
b. Port 80 (HTTP)

7. Create and attach an ACM certificate with the port 443 listener of your ALB. You can assign any
permissible subdomain string to your URL for the Jenkins Application. For example, if the public
domain of your company is cmcloudlab1111.info, you can give your Jenkins DNS the URL
jenkins.cmcloudlab111.info when generating an ACM certificate for it.

8. Ensure that the port 80 listener permanently redirects all traffic to port 443.

9. Your ALB will only route traffic to the Jenkins Master. The Ansible playbooks will handle setup
of the Jenkins Application throughout so no need to worry about the application deployment.

10. You'll be invoking Ansible playbooks onto their respective nodes via provisioners in their
Terraform code resources. You can use the following general command to wait until an EC2 instance
is in ready state to issue commands/run playbooks against it:

aws --profile default ec2 wait instance-status-ok --region <EC2-Region> --instance-ids <INSTANCE-ID>

11. For better integrity, you intend to save the Terraform state file generated in an S3 bucket.
You'll need to configure a backend in Terraform and you'll also need to create the bucket before
you run a terraform apply. Create it using:

aws s3api create-bucket --bucket <your-unique-bucket-name>

12. For Ansible, you've been provided with a preconfigured ansible.cfg file which needs to be
placed in the same folder where your Terraform template files will be.
You can download the preconfigured ansible.cfg from here. Please note that this ansible.cfg file
has the path to EC2 Dynamic Inventory configuration file hardcoded into it.

# Learning Objectives:

# Log in to the Terraform Controller Node EC2 Instance
- 1. log in to the node using SSH. 
- 2. After logging in, Execute terraform version

# Clone the GitHub Repo for Terraform Code

git clone https://github.com/cgpeanut/terraform_with_ansible.git