# Developer Theory: Basics

## What's CI/CD?
- CI = __continuous integration__
  - goal: small code changes which are frequently committed into repo after successful testing
- CD = continuous delivery / deployment
  - __continuous delivery__ = automating build, test, deployment + manual release process
  - __continuous deployment__ = full-automation of entire release process
- CI + CD = best practices for software development & deployment
  - enables frequent software changes to be applied while maintaining system & service stability

## Example workflow
- CI
  - many devs work on diff features / bug fixes in same app, using same git repo; frequently push to this shared repo
  - build-management system
    - code change pushed to repo triggers an automated build
    - want to ensure code changes don't break build or introduce new bugs in app
  - test framework
    - run automated tests on newly-biuld app
    - identifies any bugs, preventing issues from being introduced to master code
- CD
  - if __continuous delivery__, manual release process
    - merged changes are auto-built, tested, prepared for release into Staging environment(s), then Prod environment
    - manual decision process to initiate deployment of new code
  - if __continuous deployment__, auto-release process
    - takes idea of automation one step further
    - auto-deploys new code following successful testing; eliminates any manual steps
    - new code auto-released as soon as it passes through release process stages
    - small changes released early / frequently

## AWS tools for CI/CD
- CodePipeline
  - CodeCommit = source control
  - CodeBuild = build-management system ⟶ compile source code, run tests, package code
  - CodeDeploy = deploy packaged app

## Resources
- white paper: [LINK]