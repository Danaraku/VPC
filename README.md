# STEP by STEP new GUIDLANCE.

## Step 1: Launch instance "APP1":

- In the EC2 dashboard, click on "Instances" in the left sidebar, then click on the "Launch Instance" button.
- Choose an Amazon Machine Image (AMI) (AWS).
- Choose an Instance Type (t2.micro).
- No need to associate with VPC or subnets. It will beby default.
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
echo "<html><head><title>  </title></head><body><h1> APP NUMBER 1 ===> IP OF THIS INSTANCE IS $(hostname -f)</h1></body></html>" > /var/www/html/index.html
#Set permissions for the HTML file
chmod 644 /var/www/html/index.html
#Set permissions for the HTML file
chmod 644 /var/www/html/index.html

- Select Keypair.
- Review and launch instance.

## Step 2: Create second instance APP2. Repeat Step 1 but in user data add below:

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
echo "<html><head><title>  </title></head><body><h1> APP NUMBER 2 ===> IP OF THIS INSTANCE IS $(hostname -f)</h1></body></html>" > /var/www/html/index.html
#Set permissions for the HTML file
chmod 644 /var/www/html/index.html
- Select Keypair.
- Review and launch instance.

## Step 3: Create Load Balancer

- Navigate to EC2 Service and Choose a Load Balancer.
- Click on the "Create Load Balancer" button

## Step 4: Configure Load Balancer Settings:
 
- Enter a name for your load balancer.
- Choose Type: Application Load Balancer (ALB). We're working with HTTP/HTTPS traffic, we might choose ALB.
- Select Internet-facing scheme.
- Select the appropriate VPC where your load balancer will reside.
- Select subnets (public).
- Choose security group (inbound rule allow HTTP and HTTPS)
- Configure listeners and target groups. Listeners define how traffic is routed to our target groups. We need to create target group. 
While creating LB we can create target Group to add it.

## Step 5: Create Target Group:

- Click on "create Target Group" in Listeners and routing" when creating Load Balancer.
- Choose taget type (EC2).
- Write name of TG.
- Protocl port 80
- Create target
- Then going back to Load Balancer - add Listner (HTTP:PORT 80)
- Select the instances (targets) that you want the load balancer to distribute traffic to.
- Review and Create:
Review all the settings you've configured for your load balancer.
- If everything looks good, click "Create" to create the load balancer

# Step 6: Go back to tab with creating AutoScaling group

- Choose AMI:
Select the Amazon Machine Image (AMI) 
- Configure Instance Details:
Configure instance details such as the instance type, subnet, and IAM role.
- Attach to exisiting Load Balancer
- Turn on Elastic Balancing Health check
- Configure Scaling Policies:
Define scaling policies to automatically adjust the number of instances based on demand.
- Click on Targettracking scaling Policies:
- Set up scaling policies that define when and how the auto scaling group should scale in or out. Need to scale out when CPU utilization exceeds 80%.
- Put 300 sec in Instance warmup.

## Step 7: Review the configuration settings for your Auto Scaling group.

If everything looks good, click "Create Auto Scaling group" to create the group.

## Step 8: Testing:
Test your setup by launching instances through the Auto Scaling group. The instances should automatically register with the load balancer, and you should be able to access your application through the load balancer's DNS name.

## Step 9: Create A record of your domain name for your load balancer.

- Go to Route53.
- Select "Hosted Zone" from left side.
- Click on DNS
- Find the section for adding new records and choose the type "A" record.
- Click "edit record"
- Click on "Alias"
- Enter the DNS name of your load balancer in the "Value" or "Points to" field. This DNS name is typically provided by your load balancer service.
- CLick Cname and click "edit record"
- Enter DNS link
- Set the TTL (Time to Live) value. This determines how long DNS resolvers should cache the record.
- Save or submit the changes.

DONE!!!