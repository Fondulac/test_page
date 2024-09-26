# Researching Angel Project

![[Pasted image 20240808145636.png]]

## Project Responsibilities

- Develop and maintain Gitlab CI/CD pipelines
- Implement services to support pipelines (HCP Vault, Headscale)
- Create docker images to support automation efforts
- Use Pulumi to automate cloud infrastructure 
- Manage states of cloud infrastructure using salt
	- Including expanding existing automation to deploy and configure additional C2 frameworks. 
- manage custom OS image management for cloud and custom internal services 

## Infrastructure Automation 
*Referred to as Fully Automated Deployments (FAD*

Infrastructure automation creates and configures various cloud resources while providing the user updates on completion details. 

### Key Deployment Types

- [[Nexus-ng]] - Overt remote access system for penetration tests 
- [Quick-C2] - Deploys and configures Cobalt Strike and BruteRatel 

### How it works 

- CI/CD pipelines utilize a variety of technologies to deploy and configure systems and components upon an operator's request. 

## problems to consider

- Deployment of cloud infrastructure requires setting up VPNs, CDNs, DNS records etc. 
- Automated configuration of VPS, C2 services, and file transfer services
- Collection and delivery of testing support artifacts to VPS (obfuscated tooling)

## key technologies used

- Gitlab CI/CD
- Docker for CI/CD images
- Pulumi
- Salt
- Go/Python 

## Supporting Components 

1. Operators generate input document for infrastructure requests through the [[operators companion]]
2. Operators submit the input for infrastructure requests in the [[infrastructure requests]] repository as an issue, triggering a webhook request to the [[Issue Relay Server]]
3. The issue relay server accepts the request, parses the output, and kicks off a deployment pipeline in the [[infrastructure deployments]] repository 
4. Upon completion, a comment is auto added to the GitLab issue created in step 2, alerting the user of any deployment details. 

