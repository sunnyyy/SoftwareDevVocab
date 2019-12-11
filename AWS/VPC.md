# VPC = Virtual Private Cloud

## Quick facts
- provision a logically-isolated section of AWS cloud where you can launch AWS resources in a virtual network
- consists of ...
  - internet gateways (or virtual private gateways)
  - route tables
  - network ACL's
  - subnets
  - security groups

## Details
- you have complete control over your virtual networking environtment, including:
  - selection of your own IP address range
  - creation of subnets
  - configuration of route tables, network gateways, etc.
- you can leverage multiple layers of security to control access to EC2 instances in each subnet
  - security groups = stateful
  - network ACLs = stateless
- example use case:
  - you create a public-facing subnet for your webservers with access to the internet
  - you place your backend systems (databases, application servers, etc.) in a private-facing subnet with NO internet
- you can create a hardware VPN connection between your corp data center and your VPC --> leverage AWS cloud as an extension of your corp data center
- what can you do with VPC's?
  - launch instances into your chosen subnet
  - assign custom IP address ranges in each subnet
  - configure route tables between subnets
  - create internet gateway & attach it to your VPC
  - better security control over AWS resources
  - instance security groups
  - use a network ACL for each subnet
- default VPC vs. custom VPC
  - default = user-friendly, can immediately deploy instances
  - all subnets in default VPC have rout out to internet
  - each EC2 instance has both a public and a private IP
- __bastion host__ = machine in a public subnet that you use to connect to a private subnet
- __route table__ = specifies how packets are forwarded between subnets within your VPC, the internet, and your VPN connection

## Subnets ???
- internet assigned #s authority (???)
- 3 reserved IP's for private IP ranges
  - 10.0.0.0 <--> 10.255.255.255 -- (10/8 prefix)
    - largest possible block
    - widely used for corporate networks
  - 172.16.0.0 <--> 172.31.255.255 -- (172.16/12 prefix)
  - 192.168.0.0 <--> 192.168.255.255 -- (192.168/16 prefix)
- use [CIDR.xyz](http://cidr.xyz/) tool to (???)
- Amazon VPC must be between /16 and /28 (???)

## Network Access Control Lists (NACL)
- rules
  - each rule has a number; suggested assigning rules in increments of 100
  - rules are executed in ascending order; first 100, then 200, etc.
  - if you want to use both `deny` & `allow` rules, make sure your `denies` are assigned to a lower number than your `allows`
  - inbound rule are separate from outbound rules
- you can block IP addresses with NACLs, but not security groups
- a subnet must be associated with a NACL, and can only be associated with one NACL at once
- one NACL may be used for multiple subnets
- default NACL
  - when you create a VPC, a NACL is auto-created
  - by default, allows all inbound / outbound traffic
  - subnets will be auto-associated with that default NACL
- custom NACL
  - by default, each newly-created custom NACL denies all traffic until you add rules
  - if you move a subnet to a different NACL, it will no longer be associated with its previous NACL
- NACLs are __stateless__, aka responses to allowed inbound traffic are subject to rules for outbound traffic (& vice versa)

## VPC peering
- you can connect one VPC with another via a direct network route using private IP addresses --> instances behave as if on same private network
- you can peer VPCs with VPCs in same AWS account _OR_ a different AWS account
- you can peer across regions
- you can only peer in a __"star configuration"__, aka peer relationships are NOT transitive
  - aka let's say you had a VPC peering config like `A` <--> `B` <--> `C`
  - `A` would not be connected to `C`, unless you specifically set up a connection between `A` and `C`

## Custom VPCs & ELBs

## VPC flow logs

## VPC endpoints

## Lambda + VPCs
- _aka enabling Lambdas to access resources on a VPC_
- some uses cases require Lambda to access resources inside a private VPC
  - e.g. read/write to RDS in VPC
  - e.g. shut down EC2 instance in a VPC, in response to a security alert
- to enable this, you must allow function to connect to a private subnet
- Lambda requires the following VPC configs ...
  - private subnet ID
  - security group ID (+ IAM access)
- ... to set up __Elastic Network Interfaces__ (ENI) using an available IP address from your private subnet CIDR range
- ... security group then allows Lambda to access resources in VPC
- you can add VPC info to Lambda function config ...
  1. using `vpc-config` parameter: // STRING COMMAND HERE //
  2. via console (see lab)

-----

# VPC Labs

## Lab: build a custom VPC

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

## Lab: Network Access Control Lists (NACL)
- AWS console : ? : VPC --> ACL's --> (from earlier VPC setup) see 2 default ACL's, one with 2 subnets + one with 3 subnets
  - click on them to see inbound rules
    - rule 100 for IPv4
    - rule 202 for IPv6
- create a new NACL --> add name + select VPC --> upon creation, both inbound & outbound rules are set to `deny`
- `ssh` into web server
```bash
service httpd status        # to see status of Apache installation
yum install httpd -y        # to install Apache
chkconfig httpd on          # creates simlink
service httpd start         # starts Apache
cd /var/www/html; ls        # see that it's empty
vim index.html              # create a simple HTML file and save
```
  - --> go online --> notice that the webpage is visible because that instance is currently associated with the default subnet, which currently allows all traffic
- move subnet over to new NACL
  - click new ACL --> click new subnet associations --> add the default `10.0.0.0` subnet --> see the change
  - go back to the old default ACL --> notice that it's no longer part of subnet `10.0.0.0`, because it's been moved over
- go back to browser, refresh page --> will time out becaues we didn't assign the new NACL to allow HTTP/S traffic
- go to new NACL --> click "inbound rules" tab --> add new rules
  - rule = 100 // custom TPC rule // port range = 80 (for HTTP traffic) // source = `0.0.0.0/0` // allow/deny = allow
  - rule = 200 // ditto, except port range = 443 (for HTTPS traffic)
  - rule = 300 // ditto, except port range = 22 (for SSH traffic)
- click "outbound rules" tab --> add new rules
  - rule = 100 // custom TPC rule // port range = 80 // source = `0.0.0.0/0` // allow/deny = allow
  - rule = 200 // ditto, except port range = 443
  - rule = 300 // ditto, except protocol = TCP(6) + port range 1024-65535 --> for [__ephemeral ports__](https://en.wikipedia.org/wiki/Ephemeral_port)
- notice that the webpage is now visible again in the browser

## Lab: Lambda + VPC
- add VPC info to Lambda function's config via console
  - AWS console : compute : Lambda
  - --> create function --> author from scratch --> give name + defaults --> create
  - --> create test event
  - scroll all the way down in Lambda function to "network" section
    + select your VPC
    + select subnet(s) (suggested minimum of 2 subnets for high availability)
    + select security group(s)
  - --> save --> see error "your role does not have VPC permissions"
  - in Lambda function, scroll down to "execution role" section
    - Lambda requires permission to create ENI's, which can be done via ...
      - editing existing IAM role
      - creating new IAM role to include the permission
  - see in Lambda designer that we now have 3 new permissions for Amazon EC2
    - create ENI
    - delete ENI
    - describe ENI
- redo earlier steps --> this should now work
- wrap up steps --> delete Lambda function + IAM role