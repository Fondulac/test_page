# Description 

The infrastructure requests repository takes YAML inputs generated from the [[Operator's Companion]] specifying inputs. 

Appears that this repo works by creating a local copy (clone) of the repository on the issue relay server using the arguments provided by the user created issue YAML file from the operator's companion. 

Issue (YAML) -> .gitlab.ci.yml


# Files


# Analysis of .gitlab.ci.yml

## Anchors

.tailscale-variables
.http_proxies
.tailscale-service
.init-tailscale
.tailscale-send-chunk

## Functions (jobs)

==stat grabber==
compile stat grabber
send stat grabber to minio
run stat grabber

==expenum==
compile expenum
send expenum to minio
run expenum

==deployments relay==
compile deployments relay
send deployments relay to minio

==issue relay server==
compile irs
send irs to minio

==commentator==
compile comentator
send commentator to minio

==digitalocean-diver==
compile digitalocean-diver
send digitalocean-diver to minio
run digitalocean-diver

==containerize infrastructure requests utils==
containerize infrastructure requests utils

==automated infrastructure deployment==
pre push to deploy infrastructure
push to deploy infrastructure 
push to destroy infrastructure 

# Stat Grabber

## Compile Stat Grabber

==When to run==
Runs when a push is made to the master branch and changes are made to yml or go file sin the cicd-utils directory OR if the build_stat_grabber environment variable is true.

==What it does==
moves into the cicd-utils/stat-grabber directory
compiles the stat grabber

```yaml
compile stat grabber:

image: golang:1.20.6-alpine

stage: build

rules:

#Run this rule if the following is true

# a PUSH was made to the MASTER branch to any of the following files listed below

# OR

# if BUILD_STAT_GRABBER is true

- if: $CI_COMMIT_BRANCH == "master" && $CI_PIPELINE_SOURCE == "push"

changes:

- cicd-utils/stat-grabber/*.go

- cicd-utils/*.go

- cicd-utils/*.yml

- if: $BUILD_STAT_GRABBER == "true" #predefined variable on image?

artifacts:

paths:

- stat-grabber

script:

- cd cicd-utils/stat-grabber

- GOOS=linux GOARCH=amd64 go build -o ../../stat-grabber
```

## send stat grabber to minio

==When to run==
Runs if the build stat grabber environment variable is TRUE or if a push is made to .go or .yml files in the cicd-utils directory in the main branch.

Dependent on the compile stat grabber job

==What it does==
defines alias minio to minio_url, minio_access_key, minio_secret_key variables. 
copies stat-grabber to minio/infrastructure-requests/bin/stat-grabber


```yaml
send stat grabber to minio:

<<: *tailscale-send-chunk

image: git.nopsled.me:5050/justin.angel/automation-registry/common/minio:latest

stage: send

rules:

- if: $BUILD_STAT_GRABBER == "true"

- if: $CI_COMMIT_BRANCH == "master" && $CI_PIPELINE_SOURCE == "push"

changes:

- cicd-utils/stat-grabber/*.go

- cicd-utils/*.go

- cicd-utils/*.yml

dependencies:

- compile stat grabber

script:

- mc alias set minio $MINIO_URL "$MINIO_ACCESS_KEY" "$MINIO_SECRET_KEY"

- mc cp stat-grabber minio/infrastructure-requests/bin/stat-grabber
```

# un stat grabber

==When to run==
when RUN_STAT_GRABBER is true

==What it does==
using the infrastructure-stats (report.html) files, it produces an output file reporting stats (on what?)

```yaml
run stat grabber:

image: git.nopsled.me:5050/justin.angel/automation-registry/infrastructure-requests/utils:latest

stage: run

rules:

- if: $RUN_STAT_GRABBER == "true"

artifacts:

name: infrastructure-stats

paths:

- report.html

script:

- OUTPUT_FILE=report.html CI_JOB_TOKEN=$STAT_GRABBER_TOKEN stat-grabber
```