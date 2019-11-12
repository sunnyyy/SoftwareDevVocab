# Elastic Load Balancer

## Quick facts
- helps equally balance load across many servers
- types of load balancers
  * __Application Load Balancer__ ⟶ clever decisions
  * __Network Load Balancer__ ⟶ very fast speed
  * __Classic Load Balancer__ ⟶ outdated, but still on AWS exam
- to see the user's public IP address (IPv4), look for the __`X-Forwarded-For header`__
- instances monitored by ELB's are reported as `InService` / `OutofService`
- ELB FAQ: https://aws.amazon.com/elasticloadbalancing/faqs/

## Application Load Balancer
- best suited for HTTP / HTTPS traffic
- operate at Layer 7 (application level) of OSI model
- application-aware; intelligent
  - e.g. if you switch your language to fr_FR or $ to USD, it could route you to FR or US servers
- can create advanced request routing, sending specified requests to specific web servers

## Network Load 
- best-suited for load balancing __[TCP traffic](https://en.wikipedia.org/wiki/Transmission_Control_Protocol)__
- extreme performance!
- operates at Layer 4 (connection level) of OSI model
- can handle millions of requests per second, while maintaining super-low latencies
- costly!

## Classic Load Balancer
- legacy ELB
- you can use it to load balance HTTP / HTTPS apps + use Layer 7-specific features such as X-Forwarded + sticky sessions
- you can also it for Layer 4 load balancing for apps that rely purely on the TCP protocol
- errors
  - __504 error__ = time out
    - troubleshoot: is it the Web Server layer or the DB layer?
    - identify where the app is failing + scale it up or out if possible

## Advanced: Load Balancers

### Sticky sessions
- _classic load balancers_ route each request independently to the registered EC2 instance with the smallest load
- __sticky sessions__ let you bind a user's session to a specific EC2 instance --> ensures all requests from a user during that session is sent to the same EC2 instance
- useful if you're storing info locally to that instance
- can also enable sticky sessions for an _application load balancer_ as well, but traffic will be sent at the ___target group level___, not at the level of individual EC2 instances
- __exam question:__
  - Q: if you're using a classic load balancer and you see that all traffic is going to one EC2 instance but not the others, how do you fix this?
  - A: disable sticky sessions

### Cross-zone load balancing
- example scenario where you have NO cross-zone load balancing:
  - you have 2 AZ's, and Route53 splits your traffic 50-50 between AZ `us-east-1a` (with 4 instances) and AZ `us-east-1b` with 1 instance, and both AZ's each have a load balancer
  - each instance in `us-east-1a` would get `50% * 1/4 = 12.5%` traffic
  - the instance in `us-east-1b` would get 50% traffic
- example scenario where you have cross-zone load balancing:
  - each of the 2 load balancers could send traffic to the instances in the other AZ
  - each instance gets 20% traffic
- __exam question:__
  - setup: user --> Route53 --> 100% traffic sent to load balancer in `us-east-1a` --> sending that traffic to 4 instances in that same AZ
  - Q: you notice that you have no traffic to `us-east-1b`... how to fix this?
  - A: enable cross-zone load balancers

### Path patterns
- __"path-based routing"__ = you can create a listener with rules to forward requests based on the URL path
- if you're running microservices, you can route traffic to multiple backend services with path-based routing
  - e.g. route general requests to one target group + requests to render images to another target group
- __exam question:__
  - setup: user --> Route53 --> 100% of requests to _application_ load balancer in `us-east-1a`
  - Q: you want requests to `url.com` to go to `us-east-1a` but requests to `url.com/img/` to go to `us-east-1b` ... how to accomplish this?
  - A: enable path-based routing

-----

# LAB: Load Balancers & Health Checks
- NOTE: load balancers cost $$$!
- step 1: create EC2 instances
  - AWS console --> EC2 --> launch 2 different instances in 2 different AZ's
  - instance #1, under step 3:
    - use default VPC
    - under "advanced details", use a bootstrap script to install updates & run server with `index.html` file that says "server 1"
    - use specific AZ / use specific subnet `eu-west-1a`
    - --> review & launch
  - instance #2: repeat, but with slight tweak to bootstrap script & use subnet `eu-west-1b`
  - check that both IP addresses work
- step 2: create _classic_ load balancer
  - still in EC2 section --> scroll down left nav to "Load Balancers"
  - create classic load balancer
    - under step 1, use HTTP / port 80
    - under step 2, use existing security group
    - under step 4, configure helath check
      - for this lab, use small numbers for response timeout / interval / etc. for quicker testing
  - ___NOTE: we do not get IP address of ELB, only its DSN address___
  - visit DNS address of ELB --> hard refresh --> should sometimes get instance #1, sometimes instance #2
- step 3: test the classic load balancer
  - stop instance #1 --> ELB should find instance #1 unhealthy & direct to instance #2
- step 4: create target groups
  - load balancer will route requests to targets in a __target group__ (e.g. a group of instances for EU customers vs. a group for US customers)
  - still in EC2 section --> scroll down left nav to "Target Groups"
  - --> create target group
  - --> select target type = instances
    - NOTE: would also work with IP addresses, if all you had was the IP address (of a non-AWS server)
  - --> choose HTTP / port 80
  - --> for health check settings, choose path = `index.html`
  - --> for this lab, use smaller intervals in the config to see faster results
  - target group created, now add targets --> target tab --> edit --> click checkbox for both instances
- step 5: configure _application_ load balancer
  - under step 1, keep user defaults + use all 3 AZ's
  - under step 2, skip warning for HTTPS
  - under step 3, use default security group
  - under step 4, select the new target group we just created --> create
  - go back to target groups --> edit --> add targets to registered
  - wait a bit. --> target status should now be healthy
  - go back to "Load Balancers" --> go to listener HTTP : 80 --> edit rules --> can create a list of rules in an `if/then/else` tree

-----

###### Notes:
- [OSI model](https://en.wikipedia.org/wiki/OSI_model) = Open Systems Interconnection model
  1. Physical Layer
  2. Data Link Layer
  3. Network Layer
  4. Transport Layer
  5. Session Layer
  6. Presentation Layer
  7. Application Layer