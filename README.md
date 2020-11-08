# DevOps-Ass. 
This is a DevOps ass. includes a presentation of the project and its implementation code.

1. We’ll need to have the following resources installed to be able to follow along with the next steps:

	o Terraform
	o kops
	o kubectl
	o AWS CLI

2. We will be using the following GitHub repository: https://github.com/nclouds/generalized/tree/rogue_one/terraform/terraform-kops. We’ll clone it to our machines with the following command:
  >> git clone https://github.com/nclouds/generalized.git --branch rogue_one --depth 1

Then we will use the cd command to move into the right directory:
  >> cd generalized/terraform/terraform-kops/

3. We need to create a couple of AWS resources, so let’s log in to the console and create the following:
o Amazon Simple Storage Service (Amazon S3) bucket: This will be used to store Terraform state files.
o Amazon DynamoDB table: This will be used to manage locks on the Terraform state files.
The Amazon S3 bucket and Amazon DynamoDB table need to be in the same AWS Region and can have any name you want. Be sure to keep them handy as we will be using those later. The only restriction is that the Amazon DynamoDB table must have a partition key named "LockID".

4. Now that we have those resources set up, we can continue. Make a backend.tf file using the template provided in the repository.
cp backend.tf.example backend.tf
In this file, you need to input the information about your Amazon S3 bucket and Amazon DynamoDB table.

	    terraform {
	      required_version = "~> 0.10"
	      backend "s3"{
		bucket                 = "<YOUR_BUCKET_NAME>"
		region                 = "<YOUR_BUCKET_REGION>"
		key                    = "backend.tfstate"
		workspace_key_prefix   = "terraform"
		dynamodb_table         = "<YOUR_DYNAMODB_TABLE_NAME>"
	      }
	     }

5. We will create an environment config file based on the template provided in the repository.
  >>	cp config/env.tfvars.example test.tfvars
  >>	export env=test

7. Fill in the missing values in "config/test.tfvars":
   https://github.com/AlaaSHafeez/DevOps-Ass./blob/main/config/test.tfvars
8. Run 
  >>  terraform init.

    
9. We are now ready to execute a Terraform plan that will let us know about all the resources that Terraform will create for us.
  >>	terraform plan -var-file=config/${env}.tfvars
	
10. Now that we have confirmed that the plan command works, we are ready for terraform apply. 
  >>   terraform apply -var-file=config/${env}.tfvars
  
  -----------------------------------------------------------------------------------------------------------------------------------------------------------
  
  NOW we will be using Ansible as our deployment tool:
  
1. Installing Python 3, Ansible, and the openshift module: 
  >>	sudo apt update && sudo apt install -y python3 && sudo apt install -y python3-pip && sudo pip3 install ansible && sudo pip3 install openshift

2. Pip installs binaries under a hidden directory in the user’s home folder. We need to add this directory to the $PATH variable: 
  >>	echo "export PATH=$PATH:~/.local/bin" >> ~/.bashrc && . ~/.bashrc

3. Installing the Ansible role necessary for deploying a Jenkins instance: 
  >>	ansible-galaxy install geerlingguy.jenkins

4. Install the Docker role: 
  >>	ansible-galaxy install geerlingguy.docker

5. Creating a "playbook.yaml" file,
  https://github.com/AlaaSHafeez/DevOps-Ass./blob/main/playbook.yaml
		

6. Run the playbook through the following command: 
  >>	ansible-playbook playbook.yaml.
  
 
7. Jenkins should be installed. And now we can create our Jenkins Pipeline using Groovy scripting jenkins file,
   https://github.com/AlaaSHafeez/DevOps-Ass./blob/main/Groovy_scripting_Jenkins_File
