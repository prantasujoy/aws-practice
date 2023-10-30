# VPC Peerring (Same region and account)
## Description
creating connection between one public and one private vpc using vpc peering
## Services used
    1.Ec2
    2.Vpc
    3.Vpc peering
## Architecture

![Alt architecture](https://github.com/prantasujoy/aws-practice/blob/main/vpc_peering/vpc_peering.png?raw=true)

## Step1: Creating 2 VPCs:

    • Go to vpc and create a vpc
    • set name( lets say VPC-01),set subnet for the vpc(for example: 10.1.0.0/16)
    • Create a subnet inside the vpc (IPV4 CIDR block:10.1.0.0/24)
    • Create a internet gateway
    • Create a Route table,set destination to 0.0.0.0/0 and set target to the newly created internet gateway
    • Associate the route table to the subnet created before.This will make the subnet public
    • create another vpc (VPC-02-private) and subnet inside following same  process,no need to create internet gateway as we will keep this vpc private (vpc subnet:10.2.0.0/16,subnet IPV4 CIDR:10.2.0.0/24)
    • create a route table for this vpc and subnet as well, but leave it as it is.Peering connection will be added as target later for routing
## Step 2:Creating Peering Connection
    • In VPC console,click peering conection
    • set name (VPC-01-to-VPC-02),requester=>VPC-01 acceptor =>VPC-02-private
    • Account=>my account ( as two vpcs are within same account), region=>this region
    • Actions=>Accept peering connection
    • for both route tables : destination=> add subnet of the opposite vpc and set the peering connection as target
## Step 3:Creating Instances:
    • Create an instance inside VPC-01,name EC2-01
    • Auto assiggn public IP=>Enable
    • VPC=>VPC-01
    • allow SSH from anywhere in Security group
    • allow ICMP All -anywhere in security group
    • create a key pair and save it
    • launch instance (this will be a public instance)
    • another instance following same process (EC2-02-private),auto assign public IP=>disabled as it is private instance
    • for key pair may use same or different key pair
    • allow SSH from anywhere in Security group
    • allow ICMP All-anywhere in security group
    
## Step 4:Testing coonection:
    • connect to EC2-01 using ssh from local machine or use ec2 instance connect
    • ping to EC2-02-private using its private IP,if everything is ok, it will ping
    • to SSH into EC2-02,need to copy the key for Ec2-02 inside Ec2-01, and then ssh 
    • ssh -i key_pair_name username@ec2-02-private-ip

      




