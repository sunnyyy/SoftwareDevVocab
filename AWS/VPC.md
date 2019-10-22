# VPC = Virtual Private Cloud

## Quick facts
- virtual data center in the cloud
- lets you provision a logically-isolated section of AWS cloud where you can launch AWS resources in a virtual network
- you have complete control over your virtual networking environtment, including:
  - selection of your own IP address range
  - creation of subnets
  - configuration of route tables, network gateways, etc.
- ex: you can create a public-facing subnet for your webservers with access to the internet, and place your backend systems (databases, application servers, etc.) in a private-facing subnet with NO internet
- you can leverage multiple layers of security (e.g. security groups, network ACLs) to control access to EC2 instances in each subnet
- you can create a hardware VPN connection between your corp data center and your VPC --> leverage AWS cloud as an extension of your corp data center
- __bastion host__ = machine in a public subnet that you use to connect to a private subnet
- internet assigned #s authority (???)
- 3 reserved IP's for private IP ranges
  - 10.0.0.0 <--> 10.255.255.255 -- (10/8 prefix)
    - largest possible block
    - widely used for corporate networks
  - 172.16.0.0 <--> 172.31.255.255 -- (172.16/12 prefix)
  - 192.168.0.0 <--> 192.168.255.255 -- (192.168/16 prefix)
- use [CIDR.xyz](http://cidr.xyz/) tool to (???)
- Amazon VPC must be between /16 and /28 (???)
- recap:
- what can VPC's do?
  - launch instances into your chosen subnet
  - assign custom IP address ranges in each subnet
  - configure route tables between subnets
  - create internet gateway & attach it to your VPC
  - better security control over AWS resources
  - instance security groups
  - subnet network ACLs
- default VPC vs. custom VPC
  - default = user-friendly, can immediately deploy instances
  - all subnets in default VPC have rout out to internet
  - each EC2 instance has both a public and a private IP
- VPC peering
  - you can connect one VPC with another via a direct network route using private IP addresses
  - instances behave as if on same private network
  - you can peer VPCs with VPCs in same AWS account _OR_ different AWS account
  - you can peer across regions
  - you can only peer in a __"star configuration"__, e.g. 1 central VPC peered with 4 others, where peer relationships are NOT transitive
- __route table__ = specifies how packets are forwarded between subnets within your VPC, the internet, and your VPN connection

-----

# VPC Labs

## Build custom VPC

### Part 1: Create default VPC
- find "VPC" in AWS console under "Network & Content Delivery"
- lefnav: "Your VPCs" --> "create VPC" --> enter: name + IPv4 CIDR block + tenancy ("default", or "dedicated" for extra $$$)
- NOTE: default VPC creation ...
  - auto-creates:
    - a route table
    - a network ACL
    - a security group
  - does not auto-create:
    - subnets
    - internet gateway

### Part 2: Create subnet
- give name --> select your VPC --> select AZ --> give IPv4 CIDR block + optional IPv6 block
- by default, "auto-assign public" is turned off --> turn one of them public to have a public/private setup
  - select checkbox for subnet
  - --> top menu: "modify auto-assigned IP"
  - --> check "public"
- NOTES from this section of the lab:
  - your "us-east-2a" may not be the same as someone else's, as Amazon randomizes them
  - VPC & subnet sizing --> why would a 256-address block become 251? because Amazon always reserves 5 IP addresses within your subnets
  - it is not possible to have more than 1 internet gateway per VPC

### Part 3: Set up route tables.
- gateway out ?
- config route table ?
- the 2 subnets we just created by default are "not explicitly associated with any route table, and are therefore associated with the main route table"
- create a new public route table
  - add name & VPC
  - --> edit routes --> add IPv4 route "0.0.0.0/0" + intenet gateway (auto-populates with internet gateway)
  - add IPv6 (?) route "::/0" + internet gateway
  - now any subnet associated with this route table will automatically become public, for both IPv4 and IPv6
- now 2 route tables, 1 private 1 public (?)

### Part 4: Provisition EC2 instance.
- Under "Compute" in AWS console, click EC2 --> Amazon AMI --> T2 micro --> under step 3, "config instance details"
  - Network select VPC
  - subnet sleect public subnet
  - auto-assign public IP
- --> config a security group (to enable SSH)
- provision a different EC2 instance, but this time with the private subnet from earlier

### ?
- at this point, we have 2 EC2 instances, 1 public IP + 1 private IP
- create a new database security group for your private EC2 instance
  - give name + enter custom VPC
  - --> add rule for allow all ICMP traffic + use CIDR address range for public IP ___OR___ use a security group
  - rule: add `HTTP` / same address range / protocol TCP / port range 80
  - rule: add `HTTPS` / same address range / protocol TCP / port range 443
  - rule: add `MySQL/Aurora` / same address range / protocol TCP / port range 3306
  - rule: add `SSH` / same address range / protocol TCP / port range 22
- now go to EC2 instance, add new security group to database server & remove its default security group
- you can now ping the public server
- to SSH ...
  - (normally you'd use a bastion host, but for this example we won't. obviously it's a bad idea to save private keys onto public servers)
  - copy private key for (???) into public server
  - copy private IP
  - `ssh ec2-user@<privateIP> -i <private key file>` --> now ssh'ed from public web server into private DB server
- ex: `su` to root --> run `yum update -y` --> can't update bc this private server has no internet access
- NAT instances vs NAT gateways (???)
