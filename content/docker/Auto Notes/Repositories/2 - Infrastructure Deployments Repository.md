# Overview 

The infrastructure deployments repository (IDR) takes information from the issue relay server (which was sent by the information requests repository) to perform the work of deploying the requested infrastructure. 


# CI/CD - .gitlab-ci.yml stages and jobs

![[Pasted image 20240829143030.png]]

## Containerize 

Generates supporting containers, contains the following jobs. ==The containerize stage rarely runs in day to day deployments - it should only run if the supporting containers go down.==

### Jobs

*container jobs are under infrastructure_deployments/docker/gitlab-ci-build-containers.yml*
*Each container job has a boolean variable associated with it that is referenced in its ruleset*
*Containers are referenced in the container registry repository*
#### quick_c2 cobaltstrike container
#### quick_c2 nighthawk container

#### quick_c2 bruteratel container

#### nexus_ng headscale container

#### nexus_ng apt-cacher-ng container

#### nexus_ng dnsmasq container

#### nexus_ng neo4j container

#### nexus_ng squid container

#### nexus_ng syncthing container

#### nexus_ng tailscale container

#### containerize pulumi
## init

Creates the repository for the deployment. Notifies the user by responding to the issue that the deployment has started. 
### Jobs

#### quick-c2 init

#### c2-cdns init

#### nexus-ng init

#### standard droplets init

####  deploy init comment

#### destruction init comment

## pulumi-standard-droplets

Builds standard droplets. Used by various build types. Also placed before the CDN deployment to ensure IPs and FQDNs are available as needed.

### Jobs

#### standard droplets deploy

Runs if the generic-standard-droplets-condition- this condition checks the following flags:
- DEPLOY_INFRASTRUCTURE == "true"
- CI_COMMIT_BRANCH != "main"
- CI_PIPELINE_SOURCE == "push"
- And at least one of the following conditions
	- DEPLOYMENT_TYPE == "standard_droplets"
	- DEPLOYMENT_TYPE == "nexus_ng"
	- DEPLOYMENT_TYPE == "quick_c2"

The script runs the checkout-sequence (switches to the relevant branch). 

The script creates and deploys the [pulumi](pulumi.md) stack:
- Specifies the login file
- Initializes the pulumi stack
- Selects the pulumi stack for editing
- Configures the pulumi stack using the files in the branch
- Starts the pulumi stack
- Runs a python3 script - generate_roster.py ==why are some of the environment variables defined twice?==
- Pushes the changes 
- sets a temp-ssh key
- Add the gitlab ssh keys to the droplet using salt.


#### standard droplets destroy

## pulumi-C2-cdns

deploys the C2 CDNs

### Jobs

#### c2-cdns deploy

#### c2_cdns destroy

## salt-states

Allows deployments to apply salt states that will bring deployments to the expected configuration state.

### Jobs

#### quick-c2 salt states

#### nexus-ng salt states

## exit

generate files and collect information for the exit-comment stage.

### Jobs

#### quick-c2 exit

#### c2_cdns exit

#### nexus-ng exit

#### standard droplets exit

#### remove tmp_ssh.pub from droplet

#### generic destruction

## exit-comment

sends a comment to the IRR issue containing important information the tester will need to access the deployment.

### Jobs

#### deploy success comment

## failure

notify the tester through the IRR issue if the deployment fails. 

### Jobs

#### infrastructure deploy failure

#### infrastructure destroy failure