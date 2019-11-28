# Redshift

## Quick facts
- used for business intelligence
- only available in 1 AZ
- backups
  - enabled by default
  - data retention = 1 day (default) to 35 days
  - always attempts to maintain 3 copies of your data
    - original
    - replica on compute nodes
    - backup in S3
- can async replicate snapshots to S3 for disaster recovery