# CodeDeploy

## Quick facts
- automated deployment service
- deploy your app's code automatically to:
  * EC2 instances
  * in-premise systems
  * Lambda functions
- quickly release new features without downtime during app deployments
- avoid risks associated with manual processes
- auto-scales with your infrastructure
- integrates with various CI / CD tools (e.g. Jenkins, Github, Atlassian, AWS CodePipeline) & config managment tools (e.g. Ansible, Puppet, Chef)
- can be used for either __continuous delivery__ or __continuous deployment__

## 2 deployement approaches

### In-place deployments (aka "rolling deployments")
- the app is stopped in each host while lastest revision installs
- that instance is out of service during this time; you'll be at reduced capacity
  * if the instances are behind a load balancer, you can figure it to stop sending requests to the instances being upgraded
- this type of deployment can only be used for EC2 & on-premise systems, _NOT_ Lambda
- if you need to roll back, the previous version of the app must be re-deployed

### Blue-green dployments
- new instances are provisioned & latest revision is installed on the __*new*__ instances
- __blue__ = active deployment
- __green__ = new release
- new instances are registered with an Elastic Load Balancer
  * traffic is then routed to new instances
  * original instances are eventually terminated
- this type of deployment can used for EC2, on-premise systems, and Lambda
- pros:
  * new instances can be created ahead of time
  * code is released to prod by simply switching traffic to new servers
  * so long as you haven't already terminated the original servers, switching back to the original env is faster & more reliable -- you just reroute traffic back to the original servers

## Vocab
- __deployment group__ = set of EC2 instances / Lambda functions to which new revision of software will be deployed
- __deployment__ = process of applying a new revision
- __deployment configuration__ = a set of rules + success / failure conditions used during a deployment
- __AppSpec file__ = defines the deployment actions you want CodeDeploy to execute
- __revision__ = everything needed to deploy the new version
  * includes AppSpec file, app file, executables, config files
- __Application__ = unique ID for the app you wanna deploy, to ensure we reference the correct combination of revision + deployment config + deployment group

## The AppSpec file
- defines params needed for deployment + validation tests
- file structure varies depending on if for Lambda or for EC2 / on-premises

### AppSpec file for Lambda
- may be written in YAML or JSON
- contains the fields:
  * __version__ -- reserved for future use; currently only allowed value is 0.0
  * __resources__ -- name & properties of the Lambda function you're about to deploy
  * __hooks__ -- specifies Lambda functions to run at set points in the deployment lifecycle to validate the new deployment (e.g. validation tests) before allowing traffic to be sent to newly-deployed instances
- supported hooks:
  * __BeforeAllowTraffic__ = specifies Lambda to run before traffic is routed -- e.g. test new function has been deployed correctly
  * __AfterAllowTraffic__ = specifies __*Lambdas*__ to run after -- e.g. test validate that the function is accepting traffic correctly / behaving as expected

#### [EXAMPLE]

### AppSpec file for EC2 & On-Premises
- YAML only -- appspec.yml
- 4 fields
  * __version__ = reserved for future use; currently 0.0
  * __OS__ = Linux or Windows
  * __files__ = location of any app files that need to be copied & where to (source + destination)
  * __hooks__ = lifecycle event hooks; specify __*scripts*__ to run at set points in deployment lifecycle
    - e.g. unzip app files before deployment
    - e.g. run functional tests on new app
    - e.g. de-register & re-register instances with a load balancer
- _MUST_ be placed in the root of the directory of your revision
  * e.g. `ls mywebapp` --> `appspec.yml     /scripts     /config     /src`
- supported hooks:
  * __BeforeBlockTrafic__ = run tasks on instances before they're deregistered from ELB
  * __BlockTraffic__ = deregister instances from ELB
  * __AfterBlockTraffic__ = run tasks on instances after they're deregistered
  * __ApplicationStop__ = gracefully stop the app in prep for deploying our revision
  * __DownloadBundle__ = the CodeDeploy agent copies the app revision to a temp location
  * __BeforeInstall__ = any pre-installation scripts -- e.g. back up current version; decrypt files
  * __Install__ = the CodeDeploy agent copies the app revision files from the temp location to the correct location
  * __AfterInstall__ = any post-install scripts -- e.g. config tasks, change file permissions
  * __ApplicationStart__ = restarts aby services stopped during ApplicationStop
  * __ValidateServices__ = any validation tests
  * __BeforeAllowTraffic__ = any tasks on new instances before they're registered with a load balancer
  * __AllowTraffic__ = register instances with a load balancer
  * __AfterAllowTraffic__

#### [EXAMPLE]