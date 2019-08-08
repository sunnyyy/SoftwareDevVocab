# CodePipeline

## Quick facts
- fully-managed CI & C.Delivery service
- can orchestrate the build, test, & deployment of your app every time there's a change to your code
- automate e2e software release process based on user-defined workflow

## Details
- allows you to model your release process as a workflow / pipeline, made up of diff tasks
  * e.g. code update triggers build, which when done triggers test, which when passed triggers a deployment
  * e.g. dev ⟶ pushes source code ⟶ alerts CloudWatch ⟶ goes through CodePipeline ⟶ deploys via CodeDeploy ⟶ goes out to public
  * you define what happens & where
- can be modeled in CodePipeline GUI or CLI
- integrates with CodeCommit, CodeBuild, CodeDeploy, Lambda, EBS, CloudFormation, ECS, Github, Jenkins
- when you configure CodePipeline, every code changed pushed to your repo automatically enters the workflow & triggers the set of actions defined for each stage of the pipeline
- the pipeline automatically stops if one stage fails (e.g. if a unit test fails); catches bugs before code is deployed