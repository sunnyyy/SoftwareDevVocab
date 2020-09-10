# Intro

## Quick summary
- old exam = 2018
- new exam = Nov. 2019

## CIA model
- CIA = confidentiality, integrity, availability
- confidentiality ~ privacy
  - e.g. want DNA results to be mostly private, but maybe shared with family
  - using enctryption at rest + in transit
  - AWS tools: IAM, MFA
- integrity = consistency / accuracy of data + limit access from outsiders
  - ex: file permissions, version control, user access control
  - ex: checksum = check data hash after download, to ensure data accuracy
  - AWS tools:
    - AWS Certificate Manager (ACM)
    - IAM
    - S3 bucket policies
- availability = redundancy, HA clusters + multiple AZ's / regions
  - AWS tools:
    - auto-scaling groups
    - multi-AZ
- common exam q: which of these options matches "confidentiality" requirement --> choice of IAM, b), c), d)

## AAA model
- authentication, authoriztion, accounting
  - authentication = login = IAM user
  - authorization = your permissions upon login = IAM policies + S3 bucket policies
  - accounting = logging = CloudTrail

## non-repudiation (concept)
- cannot deny that you did something
- AWS tools:
  - CloudTrail
  - CloudWatch
  - IAM
  - MFA

## Security of AWS
- see whitepaper: intro to AWS security practices
- physical & environmental security
  - fire detection / suppression
  - power (2 feeds)
  - climate / temperature (avoid overheating)
  - management (who's coming in / out)
  - storage device decomissioning --> "zeroing" the disc + smashing
- business continuity management (operations center)
  - availability
  - incident response
  - company-wide executive review of any incidents
  - communication (via service health dashboards + email)
    - issues are often in us-east-1
- network security
  - secure network architecture
  - secure access points --> service in AWS is available as a public API, so must be secured as part of design
  - transmission protection --> SSL, encryption during transit
  - Amazon corporate segregation --> Amazon.com is on a separate network
  - fault-tolerant design --> multi- AZ's inside multi-regions
  - network monitoring & prevention --> DDoS mitigation via AWS shield (automatic, or for $3000/mo extra, advanced)
- AWS access (employees)
  - account review & audit every 90 days
  - background checks
  - credentials policy (very complex passwords, rotated every 90 days)
- secure design principles
  - change management
    - software = formal design reviews, threat modeling, risk assessment, static code analysis, reoccurring penetration testing, logged / tested / approved before deployment
    - infrastructure = routing & emergency config changes, all logged / tested / approved / documented, regular updates, communicated via email & via service health dashboard
- why should we trust AWS?
  - security audits by many orgs:
  - you still need to do security review of your own software