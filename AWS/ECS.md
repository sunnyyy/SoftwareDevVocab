# Docker + Elastic Container Service (ECS)

## Quick facts
- __Docker__ = open source tool, allows you to create apps based on Linux or Windows containers
- __container__ = lightweight, standalone executable software package, which includes everything the software needs to run (e.g. code, runtime env, libs, env settings)
- AWS __Elastic Container Service__ = a clustered platform that allows you to run Docker images in the cloud
- AWS __CodeBuild__ = build service; compiles code, runs tests, produces artifacts, etc. that are ready to deploy

-----

# Docker Labs

## Lab: running Docker manually (?)
- [install Docker](https://docs.docker.com/install/) --> get community edition & follow instructions
- run `docker --version` or `docker run hello-world` to confirm that Docker was installed properly
- see `dockerfile` from lab ...
- store `dockerfile` source code in CodeCommit
  - must have git installed
  - must have CodeCommit policy for IAM user
- follow commands on `GitCommands.txt` (including cloning repo locally)
- `mv dockerfile` into local repo --> cd into repo --> add to git, commit, push
- create an ECS cluster
  - AWS console : ? : ECS --> create cluster
  - use EC2 Linux + networking template (will auto-create cluster, VPC, subnets, ASG) + cluster name + provisioning model = on-demand + size of instance = T2.micro --> # instances = 1 (in prod, this should always be > 1) --> it runs a CloudForm stack for you as you wait
- after that finishes, click on your cluster --> click "ECS instances" tab --> click its underlying EC2 instance --> this'll take us to the EC2 dashboard, where you can see its status, etc.
- create the repo for the docker images
  - AWS console : ? : ECR (??) --> create repo --> after creation, click radio button next to repo, then "view push commands" button --> see commands needed to push your docker images to your repo
  - login command to authenticate your Docker client
    - notice an error because the newly-created IAM user didn't have ECR authorization --> attach new policy for "registry" (AmazonEC2ContainerRegistryPowerUser)
  - build your Docker image
  - tag your image
  - push the image to the newly-created repo
- launch new docker img
  - AWS console : ? : Amazon Container Services --> scroll down left nav to ECS --> task definitions
  - --> create new task definition
    - type = EC2 + new name + keep most defaults + task memory = 512 MB + CPU = 512 MB (since this is only a tiny website)
    - --> add new container
      - new name + img = registry URL (obtained via ECR --> repositories --> click on repo --> get repo URI) + keep default memory + new port mappings 80:80:TCP + leave out health check + keep env settings blank
    - --> add --> create
- view
  - click "actions" dropdown --> "create new service" --> will auto-find the newly created task definition & cluster
  - add new service name + keep # tasks = 1 + keep placement templates = "AZ balanced spread" --> next --> LB type = none --> next --> ASG = do not adjust (default) --> create service
  - click "myservice" --> under tasks tab, see that the task definition is running --> under events tab, see that "service ((name)) has reached a steady state"
  - go to AWS console : compute : ECS --> clusters --> click on your cluster --> click EC2 instances tab --> click on the EC2 instance --> grab public IP address --> enter in browser --> see it online

