# HA (Highly-Available) Architecture

## Quick facts
- everything fails, so design for failure
- if you fail health checks, you want the ability to fail over to another region or AZ --> use multiple AZ's & region whenever possible
- Netflix article on chaos engineering: https://medium.com/netflix-techblog/the-netflix-simian-army-16e57fbab116
  - Netflix has built an entire suite of "Simian Army" tools
  - purposely inject failure into prod env to check health of env
- __exam question:__
  - Q: your website requires a minimum of 6 instances & must tolerate failure of 1 AZ ... what's the ideal architecture to make it highly available but also most cost-effective?
  	- 2 AZ's, with 2 instances per AZ
  	- __3 AZ's, with 3 instances per AZ__ --> this is because if 1 AZ fails, you'll still have a total of 6 instances online
  	- 1 AZ with 6 instances
  	- 3 AZ's with 2 instances per AZ
- tips:
  - know the difference between multi-AZ & read replicas for RDS
  - know different S3 storage classes
    - HA tiers: standard, infrequently accessed
    - not HA tiers: reduced redundancy, single AZ
  - know when to __scale out__ (with ASG's, to add # instances) vs. __scale up__ (with better instances that have more RAM/CPU)
  - consider cost
