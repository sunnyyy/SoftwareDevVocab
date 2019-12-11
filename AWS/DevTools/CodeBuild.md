# CodeBuild

## Quick facts
- `buildspec.yml` defines build commands + settings required for CodeBuild to run build
- you can override settings in `buildspec.yml` with your own commands in the console
- if your build fails, check the build logs (recent 10k lines) in CodeBuild console, or the full logs in CloudWatch

## Details
- an example setup for a Docker + CodeBuild project could look like ...
  - CodeCommit contains ...
    - repo, which contains ...
      - `Dockerfile`
      - `buildspec.yml`
  - CodeBuild, contains
    - Docker image
      - --> which gets pushed to ECR (ElasticContainerRegistry)
- important commands:
```bash
docker build -t myImageRepo
docker tag myImageRepo:latest <URI>
docker push <URI>
```

-----

# CodeBuild Labs

## Lab: get CodeBuild to build Docker image (following lab from `ECS.md`)
- look at `buildspec` file
  - contains all commands we want CodeBuild to run
  - most important: pre-build, build, post-build
  - NOTE: change to region & repo URI
  - `git add buildspec` to repo & push
- set up CodeBuild project
  - AWS console : Developer Tools : CodeBuild
  - create new project:
    + new name
    + name of CodeCommit repo
    + environment image = managed image
    + OS = Ubuntu
    + runtime = standard
    + image = standard: 2.0
    + image version = "always use latest image"
    + SELECT privilege flag
    + service role = new service role
    + build specification = use `buildspec` file
  - click create --> click orange "build project" button --> scroll down --> click orange "start build" button
- debugging an error
  - tail logs
  - notice error in prebuild step: `AccessDeniedException` when calling `GetAuthorizationToken`
  - fix:
    - --> go back to IAM consol
    - --> find service role (based on name of service created earlier, in previous lab)
    - --> see its policy
    - --> notice that the "registry" permission is missing
    - --> attach `AmazonEC2ContainerRegistryPowerUser` role
  - go back to CodeBuild, restart build --> see it succeed
- verify change
  - go back to the ECR repo
  - see 2 images under our repo
    - newest build now tagged "latest"
    - previous (manual) build now un-tagged