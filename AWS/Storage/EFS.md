# EFS = Elastic File System

## Quick facts
- file storage service shared across EC2 instances
  - kind of like EBS volumes, except EC2 instances can't share EBS volumes
- supports the Network File system v4 (NSF v4) protocol
- elastic; you only pay for the storage you use
- no pre-provisioning required
- can scale up to petabytes
- can support thousands of concurrent NFS connections
- data is stored across multiple AZ's within a region
- read after write consistency
- does have different storage tiers (e.g. infrequently accessed), and can set up lifecycle management for files

-----

# EFS Labs

## Lab: set up EFS
- provision an EFS cluster
  - AWS console : storage : EFS --> create EFS
  - step 1: config system access --> use default VPC & AZ's in default security group
  - step 2: optional settings --> choose most default options --> enable encryption at rest + create new KMS key
  - step 3: review & create
- create EC2 instances that use this EFS cluster
  - AWS console : compute : EC2 --> launch 2 new EC2 instances --> choose Amazon AMI + instance type T2 micro
  - --> add bootstrap script that installs Apache and EFS utils:
```bash
#!/bin/bash
yum update -y
yum install httpd -y
service httpd start
chkconfig httpd on
yum install amazon-efs-utils -y
```
- EC2 dashboard --> scroll down left nav to "security groups" --> see default security group --> edit inbound rules
  - --> add "NFS" type traffic / TCP protocol / port range 2049 / source "WebDMZ" security group
  - --> check that NFS firewall was added properly
  - --> ___this allows NFS protocol into default security group via web servers___
- go back to EFS --> check that its online now
- go back to EC2 --> get the public IP addresses of both EC2 instances
  - --> SSH into both instances using separate terminals, then switch to root --> `ssh ec2-user@<IP> -i <KeyPair.pem>` + `sudo su`
  - --> `cd /var/www/html` to check that Apache was installed --> go back up to `www` dir
- go back to EFS --> click on our newly-created EFS --> click on instructions for "mount EC2 from local VPC"
  - --> use the instructions for mounting EFS with encryption (TLS) --> `sudo mount -t efs -o tls <nameOfEFS> /var/www/html`
  - --> paste that into the terminal for both EC2 instances
  - --> if this takes too long, it's likely an issue with the security group
- from EC2 instance #1, create `index.html` in `html` dir
- from EC2 instance #2, `cat index.html` to see that the file is visible --> edit it
- from EC2 instance #1, see that the file has changed
- clearly, both EC2 instances were reading from the same file
- wrap up: terminate EC2 instances + delete EFS