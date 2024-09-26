
# Overview

Pulumni provides infrastructure as code in *any programming language*. 

# As used in CI/CD

Every Pulumni program is deployed to a stack - an isolated independently configurable instance of a Pulumni program. Stacks are commonly used to denote different phases of development or feature branches. 

Create a stack using the following commands. 

```bash
pulumi stack init staging
```

## Infrastructure Deployments

```bash
pulumi login file://$PWD
pulumi stack init --cwd $PWD/pulumi_projects/standard_droplets $CI_COMMIT_BRANCH
pulumi stack select --cwd $PWD/pulumi_projects/standard_droplets $CI_COMMIT_BRANCH
|
CONFIG_FILE_PATH=$PWD/standard-droplets
```