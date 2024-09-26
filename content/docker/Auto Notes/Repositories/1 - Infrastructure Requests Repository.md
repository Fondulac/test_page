
# Overview 

The infrastructure requests repository (IRR) provides the interface from which testers send requests to the infrastructure deployments repository. To initialize the deployment, testers create a new issue within the IRR with the YAML generated from the operator's console copy and pasted into it. A webhook will be triggered when the issue is created, sending the information to a relay server that kicks off the deployment. 

# Stages

## build

Jobs in the build stage focus on compiling or recompiling different tools. 
Such as:
- stat grabber
- expenum
- deployments relay
- issue relay server (IRS)
- commentator
- digitalocean-diver

### compile stat grabber

Runs if edits are pushed to the .go file in the stat-grabber directory, go files in the cicid utils directory, or the yml file in the cicd utils directory. The BUILD_STAT_GRABBER flag must also be true. 

If the previously described conditions are true, the script cds into the stat-grabber directory of the repository and compiles the go files in the stat-grabber directory.

The stat grabber uses minio - which is a data collection tool primarily used for AI and machine learning models. 

https://min.io/
### compile expenum 

Runs if edits are pushed to .go files in the cicd-utils/expiration-enum directory, .go or .yml files in the cicd-utils directory, or if the BIULD_EXPENUM variable is true. 

If the previously described conditions are true, the script section will cd in the cicd-utils/expriation-enum directory and compile the go files in the expenum folder. 

what is expenum? 

### compile deployments relay

Runs if changes are made to the .go files in the cicd-utils/deployments-relay or cicd-utils directory, or the .yml files in the cicd-utils directory. Also runs if the BUILD_DEPLOYMENTS_RELAY is true. 

The script cds into the cicd-utils/deployments-relay directory and compiles the go files in the deployments-relay directory. 

what is the deployments relay? 

### compile irs

Runs if changes are made to the .go files in the cicd-utils or cicd-utils/issue-relay-server directories, or the .yml files in the cicd-utils directory. Also runs if the BUILD_ISSUE_RELAY_SERVER is set to TRUE. 

If the previously described conditions are true, the script section uses the mc command to set alias "minio" to the command "MINIO_URL "MINIO_ACCCESS_KEY" "MINIO_SECRET_KEY." Following this command, it uses the mc command to copy the issue relay server directory into the directory minio/infrastrcuture-requests/bin/issue-relay-server. 

Requires compile irs job to be run beforehand. 

IRS stands for *issue relay server*

### compile commentator 

Runs if changes are made to the .go and .yml files in the cicd-utils/commentator and cicd-utils directories, or if the BUILD_COMMENTATOR is TRUE. 

If the previously described conditions are met, the script section cds into the cicd-utils/commentator directory and compiles to go files in the commentator folder. 

what is commentator? 

### compile digitalocean-diver

Runs if changes are made in the go files located in cicd-utils/digitalocean-diver or if changes are made to go or yml files located in the cicd-utils directory. Also runs if the BUILD_DIGITALCOEAN_DIVER is set to TRUE. 

If the previously described conditions are met, the script section cds into the cicd-utils/digialocean-diver directory and compiles the go files located in the digitalocean-diver folder. 

## send

In each job in the send stage, minio checks if changes are made to the relevant tool's go files or if changes are made to the yml and go files in the cicd-utils directory. It then creates and alias that consists of MINIO_URL "MINIO_ACCESS_KEY" "MINIO_SECRET_KEY" and copies the tool directory into the minio/infrastructure-requests/bin/(tool name) directory. 

### send stat grabber to minio

loads the tailscale-send-chunk block 

Runs if the BUILD_STAT_GRABBER is ture and the CI_COMMIT_BRANCH is master and CI_PIPELINE_SOURCE is push. Conditions also check for changes to go files in the cicd-utils/stat-grabber directory and changes in go and yml files in the cicd-utils directory. 

Requires the compile stat grabber job to have completed. 

The script sets alias minio to MINIO_URL "MINIO_ACCESS_KEY" "MINIO_SECRET_KEY" and copies the stat-grabber file into the minio/infrastructure-requests/bin/stat-grabber directory. 

### send expenum to minio

Runs if BUILD_EXPENUM is TRUE and CI_COMMIT_BRANCH is master and CI_PIPELINE_SOURCE is push and changes are detected in go files in the cicd-utils/expriation-enum/ or cicd-utils directories or yml files in the cicd-utils directory.

requires the compile expenum job. 

If the previously described conditions are met, the script section will set the alias minio to MINIO_URL "MINIO_ACCESS_KEY" "MINIO_SECRET_KEY" and copy the expenum directory into the minio/infrastructure-requests/bin/expenum directory. Finally, ls -r is run on the minio/infrastructure-requests directory. 

### send deployments relay to minio

### send irs to minio

### send commentator to minio

### send digitalocean-driver to minio

## pre-containerize 

No jobs in this stage?

## containerize

### containerize infrastructure requests utils 

Runs if CONTAINERIZE_UTILS is true or if any of the build tool variables is true. 
Changes in the following directories also trigger the pipeline: 
- cicd-utils/Dockerfile
- go files in any of the following
	- cicd-utils/expiration-enum
	- cicd-utils/deployments-relay
	- cicd-utils/digitalocean-diver
	- cicd-utils/issue-relay-server
	- cicd-utils/stat-grabber
	- cicd-utils
- yml files in cicd-utils

Before the script section, a .docker directory is created containing a file possessing the contents of the DOCKER_AUTH_CONFIG variable. 

the script builds a docker container with the MINIO requirements and a tailscale connection. 

## run

If the tools RUN variable is set to true, run the tool. 

### run stat grabber

### run expenum

### run digitalocean-driver

## pre-deploy-infrastructure

### pre push to deploy infrastructure

sets the variables 
NOTE_ID = DEP_FAIL_EXPIRED
JOB_TOKEN = DEPLOYMENTS_RELAY_TOKEN
runs the commentator command

## deploy-infrastructure

### push to deploy infrastructure 

This job makes a push to the infrastructure deployments repository, defining a series of variables to allow the creation of infrastructure. This is where infrastructure requests directly connects to infrastructure deployments. 
## destroy-infrastructure

### push to destroy infrastructure

This job makes a push to the infrastructure deployments repository, defining a series of variables to allow the deletion of infrastructure. 

# Q&A

What does the include link reference - gitlab/includes/build-opcomp.yml