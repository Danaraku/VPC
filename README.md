# How to Create a Virtual Private Cloud (VPC)

This step by step documentation to create a Virtual Private Cloud (VPC) using AWS.

## Step 1: Access the Cloud Provider's Console

Log in to your AWS using your credentials.

## Step 2: Navigate to VPC Dashboard

Navigate to the VPC dashboard or networking section of the cloud provider's console. This is where you'll manage your VPCs.

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
- Add a route to the internet gateway (IGW) for traffic destined outside the  VPC.

### Private Route Table:
- Create a private route table for the VPC.
- Associate the private subnet with this route table.
- Add routes for internal traffic and any necessary VPN or Direct Connect connections.

## Step 7: Create Internet Gateway (IGW)

Create an internet gateway and attach it to the VPC. This allows instances in the public subnet to communicate with the internet.

## Step 8: Review and Create

Review all the configurations you've made and ensure everything looks correct. Click on the "Create"  button to create your VPC.

## Conclusion

Congratulations! You've successfully created a Virtual Private Cloud (VPC) with public and private subnets, route tables, and internet connectivity.

