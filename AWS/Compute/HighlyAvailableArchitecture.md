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

-----

# Labs

## Lab: building a fault-tolerant WordPress site, part 1: setup
- set up two S3 buckets (one for code, one for images)
  - AWS console : storage : S3 --> create bucket --> give name + region --> create
- create CloudFront distribution
  - AWS console : network : CloudFront
  - create new distribution
    + origin domain name = select images bucket from earlier
  - keep default values --> create
- ?
  - AWS console : ? : VPC
  - create a webDMZ security group (if one doesn't already exist)
    - inbound rules:
      + port 80 + `0.0.0.0/0` --> HTTP traffic
      + port 80 + `::/0` --> HTTP traffic
      + port 22 + `0.0.0.0/0` --> SSH traffic
  - create an RDS security group (if one doesn't already exist)
    - inbound rules:
      + type = MySQL / Aurora
      + protocol = TCP
      + port range = 3306
      + source = _group ID from webDMZ security group_
- ?
  - AWS console : databases : RDS
  - create a new RDS DB
    + type = MySQL
    + template = Dev/Test
    + "DB instance identifier" = name + user/pass
    + burstable class = T2.micro
    + SELECT "general purpose storage"
    + SELECT "enable storage autoscaling"
    + create a standby instance
    + default VPC
    + select dropdown:
      + advanced connectivity config
    + publicly acessible = NO
    + security group = new RDS-launch security group, created earlier in this lab
    + dropdown = additional config
      + "initial DB name" = name
      + monitoring:
        + enable detailed monitoring = NO
    + leave everything else as default
  - --> create --> wait
- create an IAM role to talk to S3
  - AWS console : identity : IAM --> create new role --> "S3forWP"
- provision an EC2 instance
  - AWS console : compute : EC2
  - create a new instance
    + Amazon Linux 2
    + T2.micro
    + select new IAM role created earlier, "S3forWP"
    + add script
      - _sidenote: `.htaccess` file allows us to rewrite URL, aka allows serving content out of CloudFront instead of S3 bucket_
    + configure security group = webDMZ
  - review + launch
- wait for all newly-provisioned services to come online

## Lab: building a fault-tolerant WordPress site, part 2: set up EC2
- check that all services from previous lab are online
- ?
- AWS console : compute : EC2 --> grab the public IP address from the instance created earlier
- ?
```bash
ssh ec2-user@<IP> -i <KeyPair>.pem
cd /var/www/html
ls                                        # lists ton of WP files ... from where ?
vim .htaccess                             # see contents from video, paste into this file
service httpd start                       # to start Apache server
service httpd status                      # check status of Apache server
```
- in browser, visit [http://<IP>/wp-admin/setup-config.php]
  - see WP installation screen
  - click "get started" to start WP installation
    + enter user / pass / dbName
    + dbHost = RDS endpoint URL
    + table prefix = "wp_"
  - possible errors:
    - if you get the error message "sorry, I can't write the wp-config.php file", you can create it manually with the auto-generated text
    - if it takes forever to load, it's likely something wrong with your security group
  - on the installation page, enter:
    + site title
    + user / pass / email
    + search engine visibility
- look at WP site configuration
  - create a post with pic --> publish --> view post
  - back in terminal ...
```bash
cd wp-content
ls
cd uploads
cd <year>
cd <month>
ls                                        # notice list of .jpg uploads in this folder
```
- we want to make sure that every time we have a new file uploaded, we also back up to S3 for redundancy
  - eventually, we also wanna force CloudFront to serve this content instead of via EC2, for faster load time
```bash
cd /var/www/html
aws s3 ls                                 # make sure S3 role works
aws s3 cp --recursive /var/www/html/wp-content/uploads s3://<bucket1Name>
```
- we also wanna add redundancy for if we lose the EC2 website, so we wanna copy the entire website
```bash
aws s3 cp --recursive /var/www/html s3://<bucket2Name>
aws s3 ls s3://<bucket2Name>              # test successful
```
- as a health check, we want a `healthy.html` file in `/var/www/html` that just says "healthy"
  - also see `.htaccess` file
    - this has a rewrite rule on the URL, to point to the CloudFront distribution
- ?
  - AWS console : network : CloudFront
  - click the ID
  - copy the domain name
  - replace the URL in the file with this domain name
- now that you've edited the `.htaccess` file, make sure S3 is sync'ed
```bash
aws s3 sync /var/www/html s3://bucket2Name
```
- for this to work, tell Apache to allow URL rewrites
```bash
cd /etc/httpd
ls
cd conf                                   # for config
ls
cp httpd.conf httpd-copy.conf             # create backup
vim httpd.conf
# scroll down until you find the line "AllowOverride None", and change that to "AllowOverride All" --> save
service httpd restart
restart httpd.service                     # force a restart to make sure changes are saved
```
- make sure S3 bucket policy allows images to be publicly-visible
  - AWS console : storage : S3
  - go into bucket settings --> uncheck all 4 checkboxes to unblock bucket objects from becoming public
  - go into bucket --> go to "permissions" tab --> click bucket policy button --> edit --> paste contents --> make sure ARN is changed to the ARN for your bucket --> ignore warning for "this bucket is public"
  - go to browser --> navigate to the WP post created earlier --> view uploaded images --> look at their URL --> check whether the URL is now CloudFront
- for high availability, create an application load balancer
  - AWS console : compute : EC2
  - scroll down left nav to "Load Balancers" section
  - create load balancer
    + application load balancer
    + name + http
    + keep defaults
    + select all 3 AZ's
    + use existing security group (webDMZ)
    + create new target group
      + name + http + port 80
    + health checks = `healthy.html` (from earlier in lab)
    + advanced health check settings
      + healthy threshhold = 2
      + unhealthy threshold = 3
      + timeout = 5 seconds
      + interval = 6 seconds
      + success code = 200
    + SELECT register targets
  - --> review --> create
- create domain name + point it behind the application load balancer
  - AWS console : network : Route53
  - go into "Hosted Zones" --> domain name --> create record set (e.g. hello.mywebsite.com) --> wait --> is now pointing at our ELB (?)
- ?
  - AWS console : compute : EC2
  - scroll down left nav to "Target Groups"
  - click target group created earlier
  - click "Targets" tab at the bottom
  - click "Registered Targets" --> edit --> click "add to registered" --> click "save" --> wait --> refresh --> see that target is now healthy
- go to your Route53 URL --> see that your WP website is now online