# EC2 = Elastic Compute Cloud

## Quick facts
- provides resizable compute capacity in the cloud
- reduces time to obtain / boot new servers (vs. historically, physcial servers)
- quickly scale capacity ↕
- charges you only for the capacity you use
- failure-resilient
- termination protection turned ___OFF___ by default --> must turn it on yourself

## Plans
- __on demand__
  * fixed rate by hour/second
  * no commitment
- __reserved (1-3 year contract)__
  * capacity reservation at discount
  * good for:
    - apps with steady state or predictable usage
    - apps that require reserved capacity (e.g. something that absolutely can't go offline)
  * 3 types: standard, convertible, scheduled
    - __*standard RI*__ = up to 75% off
    - __*convertible RI*__ = up to 54% off; you can change the attributes of this RI so long as you end up creating RIs of equal or greater value
    - __*scheduled RI*__ = available to launch within the time window you reserve; good for predicable, recurring schedule
- __spot__
  * bidding $ for capacity
  * good for:
    - flex start/end times
    - tasks that are very compute-heavy, and so can only be done during off-hours when $$ is cheaper
    - e.g. parsing genetic code at 4 AM for research
  * _NOTE: if Amazon cuts you off mid-hour, you don't pay; if you cut yourself off, you pay for full hour_
- __dedicated hosts__
  * can be purchased on-demand, but if you purchase it as a reserved RI it can be up to 70% off
  * physical server for you only
  * good for:
    - if you can't use multi-tenant virtualization, e.g. for compliance with healthcare laws
    - if you have an existing server-bound license and want to continue to use that

## Instance types
| type | purpose                               | example |
|------|---------------------------------------|---------|
| F1   | field-programmable gate array         | • genomics research<br>• financial analytics<br>• real-time video processing<br>• big data |
| I3   | high speed ([IOPS][IOPS])             | • NoSql DBs<br>• data warehousing |
| G3   | graphics intensive                    | • video encoding<br>• 3D app streaming |
| H1   | high-disk thoroughput                 | • [MapReduce](https://en.wikipedia.org/wiki/MapReduce)-based workloads<br>• distributed file systems |
| T2   | low-cost/general purpose              | • web servers<br>• small DBs |
| D2   | dense storage                         | • file servers<br>• data warehousing<br>• [Apache Hadoop](https://en.wikipedia.org/wiki/Apache_Hadoop) |
| R4   | memory optimized ("RAM")              | • memory-intensive apps<br>• DBs |
| M5   | general purpose (main choice)         | • app servers |
| C5   | compute optimized                     | • CPU-intensive apps<br>• DBs |
| P3   | graphics/general purpose GPU ("pics") | • ML<br>• Bitcoin mining |
| X1   | memory optimized ("xtreme memory")    | • [SAP HANA](https://en.wikipedia.org/wiki/SAP_HANA)<br>• [Apache Spark](https://en.wikipedia.org/wiki/Apache_Spark) = general-purpose cluster-computing framework; implicit data parallelism and fault tolerance |

## Hardware disk types
- solid
  - general purpose SSD (solid state drive)
  - provisioned [IOPS][IOPS] SSD --> for high-performance
- magnetic
  - throughput-optimized HDD
  - cold HHD (cheapest)
  - magnetic

## AMI (Amazon Machine Image) types
- you can select your AMI based on ...
  - region
  - OS
  - architecture (32-bit / 64-bit)
  - launch permissions
  - storage for root device
    - instance store ("ephemeral storage")
    - EBS-backed volume

| root device type             | EBS (Elastic Block Store)               | instance store ("ephemeral storage") |
|------------------------------|-----------------------------------------|--------------------------|
| what is it?                  | an EBS volume created from EBS snapshot | instance store volume created from a template in S3 |
| can AMI be rebooted?         | yes, without losing data                | yes, without losing data |
| can AMI be stopped?          | yes, and the data will persist          | no                       |
| if underlying host fails ... | the data will persist                   | the data will be lost    |
| if AMI is terminated ...     | by default, the root volume will be deleted (unless you had manually told AWS to keep it) | the root volume will be deleted (no option to change this) |

## ??? Enctypted Root Device: volumes & snapshots
- snapshots of encrypted volumes are auto-encrypted (must config if you don't want that)
- volumes restored from encrypted snapshots are auto-encrypted
- you can share snapshots but only if they're unencrypted
- these snapshots can be shared with other AWS accounts or made public
- you can now encrypt root device volumes upon creation of EC2 instances
  - --> if not already encrypted, process is to ...
    - create shapshot of unencrypted root device volume
    - create copy of snapshot & select "encrypt" option
    - create an AMI from encrypted snapshot
    - use that AMI to launch new encrypted instances

## Security / Encryption
- __least privilege__ = always give users minimum access required
- __create groups__
  - assign users to groups --> users inherit group permissions
  - permissions assigned with __policy documents__
- __secret access key__
  - you only see it once; if you don't save it, you can delete it and regerenate a new one --> you'd need to rerun `aws configure`
  - don't use just one access key --> use one key per dev, so that you don't have to recreate keys each time someone leaves the company
- even better, use __roles__
  - preferred from a security perspective over secret access keys
  - controlled by policies
  - if you change a policy on a role, the change is immediate
  - you can attach / detach roles to running EC2 instances without having to stop / terminate them
- you can __encrypt__ the root device volume using OS-level encryption
  - you'd first take a snapshot of that volume, then create a copy of it with encryption
  - you can then make an AMI (Amazon achine image) of this snapshot and deploy the encrypted root device volume
  - you can encrypt additional attached volumes using the console, CLI, or API

### Security groups
- all inbound traffic is blocked by default
- all outbound traffic is allowed
- changes to security groups take effect immediately
- you can have any number of EC2 instances within a security group
- you can have multiple security groups attached to an EC2 instance
- security groups are __stateful__ (???)
- if you create an inbound rule allowing traffic in, that traffic is auto allowed back out again
- you can't block specific IP addresses --> use ACL's for that
- can specify "allow" rules but not "deny" rules

### Roles
- more secure than storing access key + secret access key on individual EC2 instances
- easier to manage
- can be assigned to an EC2 instance after creation, via both AWS console & AWS CLI
- are universal (???)

### Misc.
- bootstrap scripts
  - run when an EC2 instance first boots
  - automates software installs and updates
- instance metadata & user data
  - used to get info about an instance (e.g. public IP)
  - curl commands:

## EC2 Placement Groups
- 3 types
  - __clustered placement group__
    - low network latency / high network throughput
    - _CANNOT_ span multiple AZ's
  - ____
    - individual critical EC2 instances
  - ____
    - multiple EC2 instances
    - HDFS, HBase, Cassandra

| | clustered placement group | spread placement group | partitioned |
|-|---------------------------|------------------------|-------------|
| purpose |
| can span multiple AZ's? | no | yes | yes |
| homogeneous instances?  | recommended | N/A | N/A |

- the name you specify for a placement group must be unique within your AWS account
- only certain types of instances can be launched in a placement group
  - compute-optimized
  - GPU
  - memory-optimized
  - storage-optimized
- can't merge placement groups
- can't move existing instances into a placement group --> workaround is to create an AMI from the instance, then launch a new instance from that AMI into a placement group

-----

# EC2 Labs

## Lab: using IAM roles with EC2
- create EC2 role "AdministratorAccess"
- EC2 console --> instance settings --> apply role
- via command line: even without stored credentials, EC2 instance should have access to do `X` (?????) --> run `aws s3 ls`
- using roles
  - safer than storing access key on individual EC2 instance
    - in event of hacking of EC2 instance, hackers could still run AWS command line (which isn't great), but wouldn't have access to access key ID nor secret access key
  - easier to manage
  - can be assigned to EC2 instance after creation via either the console or the command line

## Lab: using bootstrap scripts
- way of customizing EC2 deployments
- while creating EC2 instances in step 3 ("configure instance details") --> under "Advanced Details", enter bash script directly, or attach file --> this will perform your script actions

## Lab: instance metadata
- ssh into an EC2 instance
- get user data: `curl http://169.254.169.254/latest/user-data` --> returns content in bootstrap script
- get metadata: `curl http://169.254.169.254/latest/meta-data` --> see list of options
  - e.g. choose to see ipv4 address
    - --> `curl http://169.254.169.254/latest/meta-data/local-ipv4`
    - --> `curl http://169.254.169.254/latest/meta-data/public-ipv4`

-----

###### Notes:
[IOPS]: https://en.wikipedia.org/wiki/IOPS
- [IOPS](https://en.wikipedia.org/wiki/IOPS) = IO operations per second; used to measure performance for compute resources
- [MapReduce](https://en.wikipedia.org/wiki/MapReduce) = model to process and generate big data sets with a parallel, distributed algorithm on a cluster
- [Apache Hadoop](https://en.wikipedia.org/wiki/Apache_Hadoop) = network of many computers to solve problems involving massive amounts of data and computation
- [SAP HANA](https://en.wikipedia.org/wiki/SAP_HANA) = in-memory, column-oriented, relational database management system
- [Apache Spark](https://en.wikipedia.org/wiki/Apache_Spark) = general-purpose cluster-computing framework; implicit data parallelism and fault tolerance

