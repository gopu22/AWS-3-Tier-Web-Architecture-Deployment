# AWS 3 Tier Web Architecture Application Deployment
This project is a hands-on walk through of a three-tier web architecture in AWS. We will be manually creating the necessary network, security, app, and database components and configurations in order to run this architecture in an available and scalable manner.
## Architecture Overview
![3TierArch](https://github.com/gopu22/AWS-3-Tier-Web-Architecture-Deployment/assets/69630416/3c0f9195-31e6-46fa-99d0-d015fb4fe151)
In this architecture, a public-facing Application Load Balancer forwards client traffic to our web tier EC2 instances. The web tier is running Nginx webservers that are configured to serve a React.js website and redirects our API calls to the application tier’s internal facing load balancer. The internal facing load balancer then forwards that traffic to the application tier, which is written in Node.js. The application tier manipulates data in an Aurora MySQL multi-AZ database and returns it to our web tier. Load balancing, health checks and autoscaling groups are created at each layer to maintain the availability of this architecture.

## Goal
Goal of this project is to deploy scalable, highly available and secured React.js application on 3-tier architecture and provide application access to the end users from public internet.

## Getting Started
* login to the aws management console (aws.amazon.com)
* clone this repository into your local machine
  ```shell
  git clone https://github.com/gopu22/AWS-3-Tier-Web-Architecture-Deployment.git
  ```
### Part 1: Setup
* Navigate to the S3 service in the AWS console and create a new S3 bucket.
* Navigate to the IAM dashboard in the AWS console and create an EC2 role.
    * AmazonSSMManagedInstanceCore
    * AmazonS3ReadOnlyAccess
* Attach the above policies to the role.

### Part 2: Networking and Security
* Navigate to the VPC dashboard in the AWS console and navigate to Your VPCs and create a VPC.<br>
  NOTE: Choose a CIDR range that will allow you to create at least 6 subnets.
* Next, create 6-subnets under the created VPC
    * We will need six subnets across two availability zones. That means that three subnets will be in one availability zone, and three subnets will be in another zone. Each subnet in one availability zone will correspond to one layer of our three tier architecture. Create each of the 6 subnets by specifying the VPC we created in part 1 and then choose a name, availability zone, and appropriate CIDR range for each of the subnets.
    * NOTE: It may be helpful to have a naming convention that will help you remember what each subnet is for. For example in one AZ you might have the following: Public-Web-Subnet-AZ-1, Private-App-Subnet-AZ-1, Private-DB-Subnet-AZ-1.
* create a internet gateway and attach it to the VPC.
* create a NAT gateway as shown in architecture.
    * Repeat step 1 and 2 for the other subnet.
* Create route table and edit the routes according to the requirements.
* Create security group and allow port 80 for public and to myip only, Repeat this process for 5times to get totally 6 security groups.

### Part 3: Database Deployment
* Navigate to the RDS dashboard in the AWS console and click on Subnet groups on the left hand side. Click Create DB subnet group.
* Navigate to Databases on the left hand side of the RDS dashboard and click Create database.
    * Select specific engine and requirements according to the architecture.

### Part 4: App Tier Instance Deployment
* Navigate to the EC2 service dashboard and click on Instances on the left hand side. Then, click Launch Instances.
* Connect to Instance
* When you first connect to your instance like this, you will be logged in as ssm-user which is the default user. Switch to ec2-user by executing the following command in the browser terminal:
  ```shell
  sudo -su ec2-user
  ```
* Navigate to the S3 service and create bucket, and make changes in the code and upload the cloned code to the S3 bucket.
* Now, configure the database.

### Part 5: Internal Load Balancing and Auto Scaling
* Navigate to Instances on the left hand side of the EC2 dashboard. Select the app tier instance we created and under Actions select Image and templates. Click Create Image.
* Create the target group.
* Create the internet load balancers.
* Create the launch templates.
* Now, with the help of pre-requisites create auto-scaling groups.

### Part 6: Web Tier Instance Deployment
* Update the config file with LB-DNS name and upload the file to the s3 bucket and download them into the instance terminal.
* Launch another instance and repeat the commands process.

### Part 7: External Load Balancer and Auto Scaling
* Create the AMI from the 2nd launched instance.
* Create the target group, load balancers, launch template and launch the auto-scaling groups.
<br>
You should now have your external load balancer and autoscaling group configured correctly. You should see the autoscaling group spinning up 2 new web tier instances. If you wanted to test if this is working correctly, you can delete one of your new instances manually and wait to see if a new instance is booted up to replace it. To test if your entire architecture is working, navigate to your external facing loadbalancer, and plug in the DNS name into your browser.<br>

![FinalLBDNS](https://github.com/gopu22/AWS-3-Tier-Web-Architecture-Deployment/assets/69630416/07ff36aa-c2db-4c6f-809e-0e7df5a84345)

<br>NOTE: Again, your original web tier instance is excluded from the ASG so you will see 3 instances in the EC2 dashboard. You can delete your original instance that you used to generate the web tier AMI but it's recommended to keep it around for troubleshooting purposes.
<br>
<br>
<br>
Congrats!, you've Completed the 3 Tier Web Architecture Application Deployment Successfully!.
