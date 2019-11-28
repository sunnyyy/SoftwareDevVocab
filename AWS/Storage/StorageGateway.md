# AWS Storage Gateway

## Quick facts
- connects on-premise software appliances with cloud-based storage
- provides seamless / secure integration between an org's on-premises IT env & AWS's storage infrastructure
- enables you to securely store data to AWS cloud
- is available for download as a VM image that you install on a host in your datacenter
    - supports VMware ESX
    - supports Microsoft Hyper-V
- after installation, use AWS console to choose your options
- 3 diff types:
    - File Gateway (NFS & SMB)
    - Volume Gateway (iSCSI)
        - stored volumes
        - cached volumes
    - Tape Gateway (VTI)

## File Gateway
- for flat files
- files are stored as objects in your S3 buckets
- files accessed via a Network File System (NFS) mount point
- ownership / permissions / timestamps stored in S3 on user-metadata of object associated with the file
- once objects transferred to S3, they can be managed as native S3 objects
- bucket policies apply directly to objects
    - versioning
    - lifecycle management
    - cross-region replication

## Volume Gateway
- presents your apps with disk volumes using iSCSI block protocol
- data written to these volumes can be async backed up as point-in-time snapshots of your volumes & stored as EBS snapshots
    - __snapshot__ = incremental backups that capture only changed blocks
- snapshot storage is also compressed to $$
- 2 types
    - Stored Volumes
        - entire dataset is stored onsite, with async backups to AWS
        - provide low-latency access to your on-premises apps but also durable, off-site backups
        - 1 GB - 16 TB in size
    - Cached Volumes
        - entire dataset stored on S3, with most frequently accessed data cached onsite
        - minimize need to scale on-premises storage infrastructure while still providing your apps low-latency access to frequently accessed data
        - 1 GB - 32 TB

## Tape Gateway
- Virtual Tape Library interface lets you leverage your existing tape-based backup app infrastructure to store data on virtual tape cartridges that you create on your tape gateway
- each tape gateway is pre-config'ed with a media charger + tape drives, which are available to your existing client backup apps as iSCSI devices
- you add tape cartridges as you need to archive data
