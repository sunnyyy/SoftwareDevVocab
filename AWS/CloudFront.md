# CloudFront

## Quick facts
- CDN = content-delivery network
  - system of distributed servers that deliver webpages, etc. to a user based on the user's geolocation
  - speeds up delivery of static content (e.g. HTML, CSS, Javascript)
- CloudFront = AWS's CDN service
- vocab
  - __edge location__ = collection of servers in a geographically-dispersed location
    - distinct from AWS region or AZ
  - __origin__ = origin of all files that CDN will distribute
    - can be S3, EC2, Elastic LB, or Route53
    - CloudWatch can also work with any non-AWS origin server
  - __distribution__ = a collection of edge locations; aka a specific CDN
    - __*web distribution*__ = used for websites, HTTP/HTTPS
    - __*RTMP*__ = Adobe real-time messaging protocol = used for media streaming / Flash multi-media content
- user makes request to their nearest edge location
  - ⟶ edge location calls main server
  - ⟶ edge lcoation gets the content & delivers it to the user while saving a cached version of that content
  - when the next user requests the content, the edge location can deliver it directly
- objects are cached during a TTL
- objects can be written directly to edge location
- objects can be manually cleared from cache in an edge location (at an extra $$)

## S3 transfer acceleration
- basically using a CDN for S3 files
- leverages CloudFront's globally distributed edge locations
- as data arrives at an edge location, data os routed to S3 over an optimized network path
- works for S3 uploads only (?)
- reduces latency