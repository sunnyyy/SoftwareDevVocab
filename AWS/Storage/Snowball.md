# AWS Snowball

## Snowball
- for import/export to S3
- uses a physical, secure appliance to transfer data
- addresses common issues with large-scale data transfers like
    - high network costs
    - long transfer times
    - security concerns
- simple, fast, secure --> 1/5 cost of high-speed internet
- comes in 50 TB or 80 TB
- uses multiple layers of security
    - tamper-resistent enclosures
    - 256-bit encryption
    - industry-standard __Trusted Platform Model (TPM)__
- ensures security & full chain-of-custody for your data
- once data transfer processed & verified, AWS performs software erasure of Snowball

## Snowball Edge
- 100 TB data transfer device
- on-board storage & compute capabilities
- uses:
    - to move large amounts of data into/out of AWS
    - as temporary storage tier for large local datasets
    - to support local workloads in remote/offline locations
        - e.g. used within airplanes
- connects to your existing apps / infrastructure with standard storage interfaces
- streamlines data transfer process
- minimizes setup / integration
- can cluster together to form a local storage tier & process your data on-premises
- ensures your app runs even when it can't access the cloud

## Snowmobile
- up to 100 PB per Snowmobile
- is basically a large truck + 45-foot shipping container
- used to move massive volumes of data to cloud
    - e.g. video libraries
    - e.g. image repositories
    - e.g. complete data center migration

## When to use AWS Snowball?
| available internet connection | # days it'd take to transfer 100 TB at 80% network | switch to AWS Snowball if ... |
|-----------|----------|---------|
| ~ 45 Mbps | 269 days |  > 2 TB |
|  100 Mbps | 120 days |  > 5 TB |
| 1000 Mbps |  12 days | > 60 TB |
