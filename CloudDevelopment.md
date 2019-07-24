# Cloud Development: Very Basics

## Brief history of Cloud
- __Physical servers ("bare metal")__
  * provider leases a machine to consumer, with SLA to guarantee availability
- __Virtualization__
  * 1+ servers on one machine; scales up quickly, efficient
- __Infrastructure as a Service (IaaS)__
  * virtual servers for your use via API calls
  * you no longer need to maintain physical servers; saves money, time
  * remaining concerns: server going offline, hacking, etc.
  * e.g. EC2
- __Platform as a Service (PaaS)__
  * "give me your code, and we'll spin up the machine for you, and we'll even push your code into it and run it for you"
  * clients can build stuff using a platform, which handles scaling & redundancy
  * less worry about architecture
  * e.g. Salesforce, Heroku, Microsoft Azure, AWS Elastic Beanstalk
- __Software as a Service (SaaS)__
  * software whose function you can incorporate into your own software
  * you interact with it via a UI or API
  * e.g. Google Maps is an app _and_ an API
- __Containers__
  * "ephemeral"; (usually) exists only when it's running something
  * e.g. Docker, Kubernetes, Coral
- __Microservices__
  * lightweight services, but more of them
  * easier to maintain
- __Functions as a Service (FaaS)__
  * manage/run individual functions on demand
  * generic function for orchestration, data processing, backend compute, etc.
  * scale up quickly; can run in parallel
  * e.g. Lambda
- __Backend as a Service (BaaS)__
  * powerful storage & data orchestration services
  * like SaaS, but you only use it for 1-2 things
- __Serverless__
  * term for newer microservices like FaaS & BaaS
  * puts the responsibility for the server entirely on the provider
    - power, cooling, network
    - provisioning hardware (virtual or real)
    - provisioning/patching the OS & app environment
    - scaling (in part)
    - fault tolerance (in part)
  * principles of serverless
  	- execute code on demand
  	- single-purpose stateless functions
  	- push-based event-driven pipelines
  	- thicker, more powerful front-ends
  	- embrace 3P services

## What is Cloud Computing?
- 5 characteristics
  * on-demand self-service (control or API)
  * broad network access (available over internet)
  * resource pooling
  * rapid elasticity
  * measured service (billed by use)
- 4 service models
  * SaaS
  * PaaS
  * IaaS
  * Serverless
- 4 deployment models
  * private cloud
  * community cloud
  * public cloud
  * hybrid cloud

## Vendors
- AWS
- Google Cloud Platform
- Heroku
- IBM
- Microsoft Azure
- Oracle
- Rackspace
- Red Hat
- Salesforce
- Softlayer

## Other definitions
- Front-end
  * code running on user's device ("client")
  * browsers -- libraries around JS; often uses JS compilers
  * mobile devices, smart devices, voice UI
- Back-end
  * code running on a server (visual or physical)
  * compute (IaaS, PaaS, SaaS, FaaS)
  * databases, stoage
  * serverless
- SLA = Service-Level Agreement
  * contract between a service provider and its internal or external customers
  * documents what services the provider will furnish
  * defines the service standards the provider is obligated to meet
- SOA = Service Oriented Architecture
  * instead of a big monolithic app, smaller services work together, each on a separate machine
  * easier to scale

## Serverless patterns
- Command pattern
- Messaging pattern
- Legacy API wrapper
- Fan-out pattern
- Pipes and filters