

# AWS Site to SIte VPN connection

## Description
A site to site VPN connection between aws VPC and on premise (simulated in aws vpc in a different region or account)

## Services used
  1.virtual private gateway 
  2.Customer gateway 
  3.Site to site VPN 
  4.Openswan VPN 
   

## Architecture

![Alt architecture](https://github.com/prantasujoy/aws-practice/blob/main/site_to_site_vpn/s2sVpn.png?raw=true)

## step 1:Setting up on Premise
    • To simulate on premise machine use an Ec2 instance in a Vpc in a different region
    • Launch a VPC (name=>on-prem-vpc),create a subnet inside
    • Create an Internet gateway
    • Create a route table,for destination 0.0.0.0/0 set the target to internet gateway
    • Associate the route table to the subnet ,making it a public subnet
    • Launch an Ec2 instance inside the subnet (name=>on-prem)
    • Create a key pair while creating instance or use existing one
    • Disable source/distination check of the ec2
    • Auto assign public IP Enabled
    • Security group:Tcp 22,ICMP for All
## step 2: AWS VPC configuration:
    • Create a VPC (name=>aws-side-vpc) as before,and create a subnet (private)
    • Launch Ec2 instance inside the private subnet
    • In security group=>allow TCP 22 and ICMP all
## Step 3:Creating customer Gaterway:
      • From Vpc console,go to customer Gateway
    • set a name (AWS-VPC-CGW)
    • IP:Public IP of on premise Ec2
## Step 4:Creating virtual Gateway:
    • Create a virtual Gateway,name=>AWS-VPC-VGW
    • Attach it to the aws-side-vpc
## Step 5:Creating VPN Connection:
    • Create a vpn connection (name=>aws-onprem-vpn)
    • Target=>previously created Virtual private Gateway
    • Select the customer Gateway ID
    • Local IPV4 CIDR =>CIDR of on-prem-vpc
    • Remote IPV4 CIDR =>CIDR of aws-side-vpc
    • Enable route propagation for aws-side-vpc route table,selecting the virtual gateway
    • after creating,download configuration for openswan
## Step 6:Configurating Openswan on on-prem Ec2
      run commands:
        ◦ sudo su
        ◦ yum install openswan -y
          
        ◦ in /etc/sysctl.conf add:
          
          net.ipv4.ip_forward = 1
          net.ipv4.conf.all.accept_redirects = 0
          net.ipv4.conf.all.send_redirects = 0
          
        ◦ in /etc/ipsec.d/aws.conf add : 
          congiguration for the Tunnel 1, which is available in downloaded config file for the vpn
          
        ◦ in  /etc/ipsec.d/aws.secrets add:
            customer_public_ip aws_vgw_public_ip: PSK "shared secret" (alwo available in the config file)
        ◦ systemctl start ipsec
        ◦ systemctl status ipsec
          
 Ping from the on-prem Ec2 to the private Ec2








