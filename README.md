# How to Create a Virtual Private Cloud (VPC)

This step by step documentation to create a Virtual Private Cloud (VPC) using AWS.

## Step 1: Access the Cloud Provider's Console

Log in to your AWS using your credentials.

## Step 2: Navigate to VPC Dashboard

Navigate to the VPC dashboard or networking section of the cloud provider's console.

## Step 3: Start the VPC Creation Process

Click on the option/button to create a new VPC. You may see options like "Create VPC" or "Create Virtual Network".

## Step 4: Configure VPC Settings

### Basic Configuration:
- Provide a name for your VPC (e.g., MyVPC)
- Specify the CIDR block for your VPC (e.g., 10.0.0.0/16)

## Step 5: Create Subnets

### Public Subnet:
- Create a public subnet within the VPC.
- Specify a name for the subnet (e.g., PublicSubnet)
- Assign a CIDR block to the subnet (e.g., 10.0.1.0/24)
- Ensure that "Auto-assign Public IP" is enabled for instances in this subnet.

### Private Subnet:
- Create a private subnet within the VPC.
- Specify a name for the subnet (e.g., PrivateSubnet)
- Assign a CIDR block to the subnet (e.g., 10.0.2.0/24)
- Disable "Auto-assign Public IP" for instances in this subnet.

## Step 6: Configure Route Tables

### Public Route Table:
- Create a public route table for the VPC.
- Associate the public subnet with this route table.
- Add a route to the internet gateway (IGW).

### Private Route Table:
- Create a private route table for the VPC.
- Associate the private subnet with this route table.

## Step 7: Create Internet Gateway (IGW)

Create an internet gateway and attach it to the VPC. This allows instances in the public subnet to communicate with the internet.

## Step 8: Review and Create

Review all the configurations you've made and ensure everything looks correct. Click on the "Create"  button to create your VPC.

## Virtual Private Cloud (VPC) is created.

## Step 9: ENDPOINT 

Go to the VPC dashboard in the AWS Management Console, and select "Endpoints" from the left sidebar. Click on "Create Endpoint", then select the service you want to create the endpoint for.

## Step 10:Configure Endpoint Settings:

 - Configure the endpoint settings: services: S3 amazon: com.amazonaws.us-east-2.s3 and click on type "gateway".
 - Configure the VPC and subnets in which the endpoint will be accessible. 
 - Configure type as "gateway".
 - Associate it with Piblic Route table.
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

## Step 11: Review and Create

Review the endpoint configuration, then click on "Create endpoint" to create the endpoint.

## Step 12: Update Route Tables

If you're creating an interface VPC endpoint (powered by AWS PrivateLink) and want to make the service accessible to resources within your VPC, you may need to update your VPC's route tables to route traffic to the endpoint.

## Step 13: Test the Endpoint:

Once the endpoint is created, you can test it by accessing the AWS service using the endpoint's DNS name from resources within your VPC. Ensure that the access works as expected.

## SYNC FILES FROM S3 TO EC2

## Step 14: Create S3 Bucket

- Click on the "Create bucket" button.
- Enter a unique name for your bucket. 
- Select the region for your bucket. 
- Create new folder "file-sync-service".
- Open folder and upload file.
-
## Step 15: Launch instance "amz-dev-file-sync-worker":

- In the EC2 dashboard, click on "Instances" in the left sidebar, then click on the "Launch Instance" button.
- Choose an Amazon Machine Image (AMI) (AWS).
- Choose an Instance Type (t2.micro).
- Associate with VPC and Public Subnet.
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

## Step 16: ADD IAMROLE:

- In the IAM dashboard, click on "Roles" in the left sidebar, then click on the "Create role" button.
- Select the type of trusted entity for the role. (AWS service).
- Select Use case (EC2).
- Attach permissions policies (S3READONLY).
- Review the role configuration to ensure it meets your requirements.
- If everything looks good, click "Create role" to create the IAM role.

## Step 17.Make changes in S3 bucket policy:

## Add IAM ROLE (S3READONLY):
- Click on Instance 
- Go to "Actions" and choose "Security" and then "Modify IAM role"
- Add created S3READONLY IAM ROLE

## Modify Permissions in S3 Bucket:
- Bucket policy as: block all public access.
- Add policy:
copy ARN of IAM role and insert in Principal
action: GetObject
resource: ARN of Autoscaling Group
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
## Step 18: Download files from S3 to EC2:
- Connect to EC2
- Run command: aws s3 cp s3://bucket-name/path/to/source/file /path/to/destination/file
(in our case: aws s3 cp s3://staging-danara/file-sync-service/Greyhound_-_dog.jpeg .)


## Step 19: Create AutoScaling Group:

- Click on "Auto Scaling groups" in the left sidebar, then click on the "Create Auto Scaling group" button.
# Choose "Create Launch template".
- Add name and description
- in App and OS IMAGES (AMI) click 'quick start and choose Amazon Linux.
- instance type: t2.mirco
- add keypair.
- Subnet: Public
- select security group or create security group.
-
# Create Load Balancer:
- Choose a Load Balancer Type: Application Load Balancer (ALB). We're working with HTTP/HTTPS traffic, we might choose ALB.
- Configure Load Balancer Settings:
- Enter a name for your load balancer.
- Select the appropriate VPC where your load balancer will reside.
- Configure listeners and target groups. Listeners define how traffic is routed to our target groups. We need to create target group.

# Create Target Group:
- click on "create Target Group" in Listeners and routing" when creating Load Balancer.
- Choose taget type (EC2).
- Write name of TG.
- Protocl port 80
- Choose VPC
- Add instance
- Create target

- Then going back to Load Balancer - add Listner POrt 80
- Select the instances (targets) that you want the load balancer to distribute traffic to.
- Review and Create:
Review all the settings you've configured for your load balancer.
- If everything looks good, click "Create" to create the load balancer

then going back to Autoscaling group
- attach Instance

- in Details: add desired capacity -1; minimum capacity:1; maximum capacity: 4

## Step 20: Configure Auto Scaling Group to Use Load Balancer:

- Go to the EC2 Auto Scaling service in the AWS Management Console.
- Select Auto Scaling group from the list.
- In the "Details" tab, under "Load balancing", click on "Edit".
- Select "Enable" for "ELB(s)" and choose load balancer from the dropdown list.
- Click "Save" to apply the changes.
- Configure Health Checks:
- Ensure that load balancer is configured with appropriate health checks to monitor the health of instances registered with it. Auto Scaling will use these health checks to determine whether instances should be considered healthy or not.
- Testing:
Test your setup by launching instances through the Auto Scaling group. The instances should automatically register with the load balancer, and you should be able to access your application through the load balancer's DNS name.

### DONE!