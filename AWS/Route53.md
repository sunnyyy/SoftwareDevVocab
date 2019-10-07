# Route 53

## Vocab
- __DNS = domain name servers__
  - on port 53 --> where Route53 get its name
  - like a phonebook that maps domain names (human-friendly web URLs) to IP addresses
- __IP address = internet protocol__
  - e.g. `http://82.124.53.1`
- IPv4 vs. IPv6
  - __IPv4__ = 32-bit field --> 4 billion addresses available --> we're running out
  - __IPv6__ = 64-bit field --> 340 unodecillion addresses
- __top-level domains__ = .com, .edu, .gov
- __second-level domains__ = the ".co" in .co.uk, the ".gov" in .gov.uk
- __domain registrars__
  - authority that assigns DNs & registers them with InterNIC / ICANN
  - each DN becomes registered in a central "WhoIS" database
- __SOA = state of authority record__
  - stores name of server that supplies data for the URL (?)

## Quick facts
- Route 53 = Amazon's DNS service
- allows you to map your domain names to:
  - EC2 instances
  - load balancers
  - S3 buckets

## Routing policies
If you want to spread out backend endpoints (EC2 instances, S3 buckets, etc.) across many regions, but keep 1 URL, you can use __records__ to route your DSN to multiple IP addresses.

### 1. Simple routing
- only 1 record with multiple IP addresses
- Route53 returns all values to user in random order

### 2. Weighted routing --> randomly (?)
- split traffic based on assigned weights
  - e.g. 10% traffic to us-east-1, 90% to us-west-2
- create multiple record sets, each with a weight
  - if you have 2 record sets with weights 20 & 30, you'll get a 40/60 split
  - if you have 3 record sets with weights 20, 30, 50, you'll get 20%, 30%, 50%
- checkbox for "associate with health check", per record set
  - you can set up SNS notifications to tell you if a record set's resource goes down (e.g. EC2 instance terminated)
  - the other EC2 instances would receive that traffic
  - bad resource is removed from Route53 until it passes

### 3. Latency-based routing
- allows you to route traffic based on the lowest network latency (aka fastest response time) for your end user
- usually linked to geographic proximity, but say if the underwater cable between server in Ireland and user in UK was overloaded, but trans-Atlantic underwater cable was fine, you could get lower latency from server in Ohio

### 4. Failover routing
- used when you wanna create an active / passive setup
- e.g. primary site in eu-west-2 + secondary site in ap-southeast-2
- Route53 maintains the health of your primary site with a health check
  - if primary fails, will route your traffic to secondary

### 5. Geolocation routing
- lets you choose where traffic will come based on users' geographic location
- e.g. traffic from Europe to be routed to a fleet of EC2 instances config'ed for Europe
- __not__ same thing as latency-based routing (which actually checks latency, instead of assuming latency from proximity)

### 6. Geoproximity routing
- only available in traffic-flow mode
- kinda complicated, not covered in most AWS exams
- lets Route53 route traffic to your resources based on geographic location of users & resources
- can optimally choose to route more/less traffic to a given resource by specifying a value ("bias")
- a bias expands/shrinks the size of the geographic region from which traffic is routed to a resource

### 7. Multivalue routing
- same as "simple" policy but lets you check health of each resource
- only returns values for healthy resources

-----

## Lab
- register a domain name
- create a "record set"