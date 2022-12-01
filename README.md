# Deploying the full three-tier application 
##  Infrastructure Setup 

    1 VPC
    2 Public subnets
    2 Private subnets
    2 Autoscaling groups
    5 Security Groups
    2 Load Balancers, (one private, one public)
    2 Private EC2 instances (representing our application tier)
    2 Public EC2 instances (representing our presentation tier)
    1 Nat Gateways (so private instances can connect to the internet)
    1 Elastic IP addresses,
    2 rds instance

#  Our Application 
Our application consists of 3 tiers 
1. presentation tier (this represents normally the customer facing part of the application, so what the customer interacts with)
2. application tier (this is where we have our business logics)

3. To keep it simple, our presentation tier simply forward requests to the business tier, that in turn run sql queries on the rds instance.

#  Terraform Resources 

    1. vpc  module-> creates the vpc
    2. subnet module -> public subnets,private subnet, internet gateway, eip, nat gateway, route table , creates the compute instances
    3. security module -> To allow traffic between the load balancers, public, and private instances, we added a security group for each component
    4. elb module  -> creates the presentation load balancer ,application load balancer, the listener, and the target group
    
    5. master module -> declares the providers to use (only the aws provider for now) , here we apply init,plan,apply,destroy, declare variable
    6.asg module -> declares the auto scaling group
    7. rds module -> We added a module for rds. Which will basically provision an rds instance that the application EC2 instances will query for data.
    This module will create 3 resources:

    1. A db subnet group
    2. A security group
    3. An rds instance

#  Creating the infrastructure 

- Our current infrastructure will consist of a vpc resource named main that is declared with a cidr block of "10.0.0.0/16".
- We will have 2 public subnets in different availability zones (to achieve a highly available architecture).
- We need need to create a security group that allows HTTP traffic in and out of instances
- Since our VPC will need to connect to the internet, we will need to create an Internet Gateway and attache it to our freshly created VPC as follows
- We will also create a route table and attach our public subnets to it, so we will have a route from these subnets to the internet
-
# Creating the Application Load Balancer 
- For this part we can refer to the elb.tf file. This file will create an application load balancer named "Alb
- The rest of the file, create, a load balancer rule, a target group on port 80, and a target group attachement, that attaches the instance we will create to the load balancer target group"

#  Creating the EC2 instances 

- The last part here is the EC2 instances that will be provisioned when terraform runs the master module. This will create 2 instances in the public subnets and private subnets , and will have the security group that allows http traffic attached to them.

##  Deploying the infrastructure + application 

- Please make sure to have terraform installed, and have AWS Credentials configured locally.
- Navigate to the module folder and run:

1. terraform init
    This will initialise the backend, and install the aws plugin and prepare terraform.
2. terraform plan
    The terraform plan command evaluates a Terraform configuration to determine the desired state of all the resources it declares, then compares that desired state to the real infrastructure objects being managed with the current working directory and workspace.
3. terraform apply 
    The terraform apply command executes the actions proposed in a terraform plan .It is used to deploy your infrastructure.   
4. terraform destroy
    The terraform destroy command terminates resources managed by our Terraform project. This command is the inverse of terraform apply in that it terminates all the resources specified in your Terraform state. It does not destroy resources running elsewhere that are not managed by the current Terraform project.
