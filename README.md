# Step by step guidance
## Step 1: Create ENDPOINT 
- Go to the VPC dashboard in the AWS Management Console, and select "Endpoints" from the left sidebar. 
- Click on "Create Endpoint".
## Step 2: Configure Endpoint Settings:
 - Configure the endpoint settings: services: S3 amazon: com.amazonaws.us-east-2.s3 and click on type "gateway".
 - Configure the VPC and subnets in which the endpoint will be accessible. 
 - Configure type as "gateway".
 - Associate it with Public Route table.
 - You can also specify security groups and policies for controlling access to the endpoint.
 - Policy as:
 {
    "Version": "2008-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Principal": "*",
            "Action": "*",
            "Resource": "*"
        }
    ]
}
## Step 3: Review and Create
Review the endpoint configuration, then click on "Create endpoint" to create the endpoint.
## Step 4: Update Route Tables
If we need to update our VPC's route tables to route traffic to the endpoint.
## Step 5: Test the Endpoint:
Once the endpoint is created, we can test it by accessing the AWS service using the endpoint's DNS name from resources within our VPC. Ensure that the access works as expected.
## SYNC FILES FROM S3 TO EC2
## Step 6: Create S3 Bucket
- Click on the "Create bucket" button.
- Enter a unique name for your bucket. 
- Select the region for your bucket. 
- Create new folder "file-sync-service".
- Open the folder and upload file.
## Step 7: Launch instance "amz-dev-file-sync-worker":
- In the EC2 dashboard, click on "Instances" in the left sidebar, then click on the "Launch Instance" button.
- Choose an Amazon Machine Image (AMI) (AWS).
- Choose an Instance Type (t2.micro).
- Associate instance with VPC and Public Subnet.
- Add port 80 in securtiy group.
- Add script in  advance details - user data:
#!/bin/bash
#Update the system packages
yum update -y
#Install Apache HTTP server
yum install -y httpd
#Start the Apache service
systemctl start httpd
#Enable Apache to start on boot
systemctl enable httpd
#Create a basic HTML file to be served
echo "<html><head><title>  </title></head><body><h1> FILE SYNCHRONIZING SERVICE at $(hostname -f)</h1></body></html>" > /var/www/html/index.html
#Set permissions for the HTML file
chmod 644 /var/www/html/index.html
- Select Keypair.
- Review and launch instance.
    
## Step 8: ADD IAMROLE:
- In the IAM dashboard, click on "Roles" in the left sidebar, then click on the "Create role" button.
- Select the type of trusted entity for the role. (AWS service).
- Select Use case (EC2).
- Attach permissions policies (S3READONLY).
- Review the role configuration to ensure it meets your requirements.
- If everything looks good, click "Create role" to create the IAM role.
## Step 9: ADD IAM ROLE (S3READONLY):
- Click on Instance 
- Go to "Actions" and choose "Security" and then "Modify IAM role"
- Add created S3READONLY IAM ROLE

## Make changes in S3 bucket policy:
## Step 10: Modify Permissions in S3 Bucket:
- Bucket policy as: block all public access.
- Add policy:
copy ARN of IAM role and insert in Principal
action: GetObject
resource: bucketname/*
{
    "Version": "2012-10-17",
    "Id": "Policy1711214493008",
    "Statement": [
        {
            "Sid": "Stmt1711214488706",
            "Effect": "Allow",
            "Principal": {
                "AWS": "arn:aws:iam::730335401584:role/S3READONLY"
            },
            "Action": "s3:GetObject",
            "Resource": "arn:aws:s3:::staging-danara/*"
        }
    ]
}
## Step 11: Download files from S3 to EC2:
- Connect to EC2
- Run command: aws s3 cp s3://bucket-name/path/to/source/file /path/to/destination/file
(in our case: aws s3 cp s3://staging-danara/file-sync-service/Greyhound_-_dog.jpeg .)
## Create AutoScaling Group for SyncingApps.
## Step 12: Create Load Balancer:
- Navigate to EC2 Service and Choose a Load Balancer.
- Click on the "Create Load Balancer" button
## Step 13 Configure Load Balancer Settings:
 
- Enter a name for your load balancer.
- Choose Type: Application Load Balancer (ALB). We're working with HTTP/HTTPS traffic, we might choose ALB.
- Select Internet-facing scheme.
- Select the appropriate VPC where your load balancer will reside.
- Select subnets (public).
- Choose security group.
- Configure listeners and target groups. Listeners define how traffic is routed to our target groups. We need to create target group. 
While creating LB we can create target Group to add it.
## Step 14: Create Target Group:
- Click on "create Target Group" in Listeners and routing" when creating Load Balancer.
- Choose taget type (EC2).
- Write name of TG.
- Protocl port 80
- Choose VPC
- Create target
- Then going back to Load Balancer - add Listner (HTTP:PORT 80)
- Select the instances (targets) that you want the load balancer to distribute traffic to.
- Review and Create:
Review all the settings you've configured for your load balancer.
- If everything looks good, click "Create" to create the load balancer
## Step 15: Creating Autoscaling Group
- Navigate to EC2.
- Go to the Auto Scaling Groups section: In the EC2 dashboard, locate the "Auto Scaling Groups" option in the left sidebar, and click on it.
## Step 16: Choose "Create Launch template".
- Add name and description
- In App and OS IMAGES (AMI) click 'quick start and choose Amazon Linux.
- Instance type: t2.mirco.
- Add keypair.
- Don't include subnet.
- Select security group or create security group.
- Add script to user data in Advanced details:
#!/bin/bash
#Update the system packages
yum update -y
#Install Apache HTTP server
yum install -y httpd
#Start the Apache service
systemctl start httpd
#Enable Apache to start on boot
systemctl enable httpd
#Create a basic HTML file to be served
echo "<html><head><title>  </title></head><body><h1> FILE SYNCHRONIZING SERVICE at $(hostname -f)</h1></body></html>" > /var/www/html/index.html
#Set permissions for the HTML file
chmod 644 /var/www/html/index.html
- Click on Create launch template
## Step 17: Go bakc to tab with creating AutoScaling group
- Choose AMI:
Select the Amazon Machine Image (AMI) 
- Configure Instance Details:
Configure instance details such as the instance type, subnet, and IAM role.
- Attach to exisiting Load Balancer
- Turn on Elastic Balancing Health check
- Configure Scaling Policies:
Define scaling policies to automatically adjust the number of instances based on demand. (min-1; desired -1; max - 4)
- Click on Targettracking scaling policy
- Put 300 sec in Instance warmup.
## Step 18: Review the configuration settings for your Auto Scaling group.
If everything looks good, click "Create Auto Scaling group" to create the group.
## Step 19: Testing:
Test your setup by launching instances through the Auto Scaling group. The instances should automatically register with the load balancer, and you should be able to access your application through the load balancer's DNS name.
### DONE!