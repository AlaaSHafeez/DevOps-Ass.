# DevOps-Ass. 
This is a DevOps ass. includes a presentation of the project and its implementation code.

1. We’ll need to have the following resources installed to be able to follow along with the next steps:

o	Terraform
o	kops
o	kubectl
o	AWS CLI

2.	We will be using the following GitHub repository: https://github.com/nclouds/generalized/tree/rogue_one/terraform/terraform-kops. We’ll clone it to our machines with the following command:
  >> git clone https://github.com/nclouds/generalized.git --branch rogue_one --depth 1

Then we will use the cd command to move into the right directory:
  >> cd generalized/terraform/terraform-kops/

3.	We need to create a couple of AWS resources, so let’s log in to the console and create the following:
o	Amazon Simple Storage Service (Amazon S3) bucket: This will be used to store Terraform state files.
o	Amazon DynamoDB table: This will be used to manage locks on the Terraform state files.
The Amazon S3 bucket and Amazon DynamoDB table need to be in the same AWS Region and can have any name you want. Be sure to keep them handy as we will be using those later. The only restriction is that the Amazon DynamoDB table must have a partition key named "LockID".

4.	Now that we have those resources set up, we can continue. Make a backend.tf file using the template provided in the repository.
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

5.	We will create an environment config file based on the template provided in the repository.
  >>	cp config/env.tfvars.example <env_name>.tfvars
  >>  export env=<env_name>

7.	Fill in the missing values in config/test.tfvars. After doing so it should look something like this:
      environment   = "test"
      cluster_name  = "nclouds"
      region        = "us-west-2"

      ### VPC MODULE
      vpc= {
          cidr          = "10.2.0.0/16",
          dns_hostnames = true,
          dns_support   = true,
          tenancy       = "default",
        }
      public_subnets  = ["10.2.0.0/24","10.2.1.0/24","10.2.5.0/24"]
      private_subnets = ["10.2.2.0/24","10.2.3.0/24","10.2.4.0/24"]

      ### KUBERNETES MODULE
      kops_state_bucket = "<YOUR_BUCKET_NAME>/kops"
      worker_node_type = "t3.medium"
      min_worker_nodes = "1"
      max_worker_nodes = "2"
      master_node_type = "t3.medium"


8.	Run 
  >>  terraform init.

    
9.	We are now ready to execute a Terraform plan that will let us know about all the resources that Terraform will create for us.
  >>	terraform plan -var-file=config/${env}.tfvars
	
10.	Now that we have confirmed that the plan command works, we are ready for terraform apply. 
  >>   terraform apply -var-file=config/${env}.tfvars
  
  -----------------------------------------------------------------------------------------------------------------------------------------------------------
  
  NOW we will be using Ansible as our deployment tool:
  

    
 
