# ASG = Auto-Scaling Groups

# Labs

## Lab: Launch configs + ASGs
- AWS Console : Compute : EC2 --> scroll down left nav, under "load balancing" --> provision an ASG
- create a launch config
  - step 1: choose AMI = Amazon Linux 2
  - step 2: choose type = T2 micro
  - step 3: config details
    - name = "MyASG-LC" + IAM role = "S3AdminAccess" + use bootstrap script
    - select "only assign public IP address to instances launched in the default VPC & subnet"
  - step 5: use existing security group
  - step 6: preview --> create --> nothing has been deployed yet
  - on confirmation page (?), click button for "create ASG using this launch config" --> this will deploy things
- create an ASG
  - step 1 (?)
    - add group name
    - launch config will be pre-populated to the one just created
    - start with 3 instances
    - use existing VPC
    - click on subnets (auto-populated) --> these will be randomly-created subnets in spread-out AZs
  - step 2
    - select "use scaling policies to adjust capacity of this group"
    - --> "scale between 3 & 6 instances"
    - --> metric type = "average CPU utilization"
    - --> target value = "80%" --> aka at 80% CPU, scale out
- after creation, go to ASGs --> go to "activity history" tab --> see EC2 instances launch in progress
- once all EC2 instances are online, terminate 2/3 instances (to simulate failover)
  - (alternately, you can stress out the CPU)
  - --> wait & see if the ASG scales to handle / recover from this
  - --> see new EC2 instances coming back online
  - --> total 3 instances back online, one in each of the 3 original AZs
