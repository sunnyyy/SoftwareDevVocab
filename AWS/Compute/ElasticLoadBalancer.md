# Elastic Load Balancer

## Quick facts
- helps equally balance load across many servers
- [OSI model](https://en.wikipedia.org/wiki/OSI_model) = Open Systems Interconnection model
  1. Physical Layer
  2. Data Link Layer
  3. Network Layer
  4. Transport Layer
  5. Session Layer
  6. Presentation Layer
  7. Application Layer
- types of load balancers
  * __Application Load Balancer__ ⟶ clever decisions
  * __Network Load Balancer__ ⟶ very fast speed
  * __Classic Load Balancer__ ⟶ outdated, but still on AWS exam

## Application Load Balancer
- best suited for HTTP / HTTPS traffic
- operate at Layer 7 (application level) of OSI model
- application-aware; intelligent
- can create advanced request routing, sending specified requests to specific web servers

## Network Load 
- best-suited for load balancing TCP traffic
- extreme performance!
- operates at Layer 4 (connection level) of OSI model
- can handle millions of requests per second, while maintaining super-low latencies
- costly

## Classic Load Balancer
- legacy ELB
- you can use it to load balance HTTP / HTTPS apps + use Layer 7-specific features such as X-Forwarded + sticky sessions
- you can also use strict Layer 4 load balancing for apps that rely purely on the TCP protocol
- errors
  * if your app stops responding, the Classic Load Balancer responds with a 504 error
  * your app could be having issues at either the Web Server layer or the DB layer
  * identify where the app is failing + scale it up or out if possible

## X-Forwarded-For header
- user (IP address 124.12.3.231) ⟶ load balancer (private IP 10.0.0.23) ⟶ EC2 will see IP address 10.0.0.23
- to see the user's public IP address (IPv4), look for the __`X-Forwarded-For header`__

