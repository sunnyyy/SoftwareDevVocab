# EBS = Elastic Block Storage

## Quick facts
- create storage + attach to EC2 instances
- once attached, you can: creating file sys, run database, etc.
- by availability zone
- auto-replicated for safety
- EBS root volumes
  - on an EBS-backed instance, the default action is for the root EBS volume to be ___deleted___ when the instance is terminated
  - EBS root volumes of your default AMI's cannot be encrypted (???)
- you can use a 3P tool like Bitlocker to encrypt the root volume or do it when creating AMI's in the console or with the API
- additional volumes can be encrypted

## Types
- SSD = solid state drive
  - __general-purpose SSD (GP2)__ ⟶ most workloads
    - 10000 IOPS ± 3000 IOPS
  - __provisioned IOPS SSD (IO1)__ ⟶ intensive I/O ⟶ mission-critical apps
    - e.g. NoSQL, large relational databases, etc.
    - 10000+ IOPS (up to 20000 IOPS per volume)
- magnetic / HDD = hard disk drive
  * __throughput-optimized HDD (ST1)__ ⟶ big data, data warehouse, log processing
    - not boot volume ("boot volume" is like C drive on a Windows; "non-boot volume" is like a D drive in Windows)
  * __cold HDD (SC1)__ ⟶ infrequent access⟶ file server
    - lowest-cost
    - not boot volume
  * __magnetic (standard)__ ⟶ legacy! ⟶ infrequent access
    - can be boot volume

## EBS snapshots
- always in the same AZ as the EC2 instance
- volumes
  - exist on EBS
  - like a virtual hard disk
- snapshots
  - exist on S3
  - like point-in-time copies of volumes
  - are incremental ⟶ only changed blocks are moved to S3
  - first snapshot takes time
  - you can create a snapshot while an instance is running, but better to stop it for a consistent snapshot
- you can create AMI's from both volumes and snapshots
- you can change EBS volume sizes on the fly, including size + storage type

## Migrating EBS
- to move an EC2 volume from one AZ to another ...
  1. take a snapshot
  2. create an AMI from the snapshot
  3. then use new AMI to launch the EC2 instance in a new AZ
- to move an EC2 volume from one region to another ...
  1. take a snapshot
  2. create an AMI from the snapshot
  3. copy the AMI from one region to another
  4. then use copied AMI to launch new EC2 instance in a new region

## Encryption
- snapshots of encrypted volumes are auto encrypted
- volumes restored from encrypted snapshots are auto encrypted
- only ___UNencrypted___ snapshots can be shared
  - can be shared with other AWS accounts
  - can be made public
- to encrypt an unencrypted root device volume ...
  1. create a snapshot
  2. create a copy of the snapshot & select "encrypt"
  3. create an AMI from the encrypted snapshot
  4. use the AMI to launch new encrypted instances