
# 1. User specifies input in [[Operator's Companion]]

Common configurations are:
- Nexus-NG (NNG)
- Standard Droplets 

## Nexus-ng deployment 

Yaml file specifies the following

deployment_type: nexus-ng 
implant_count: (NUMBER OF IMPLANTS)
headscale_options: //how do testers choose these? Is there a default?
	namespace: nexus
	tailnet_domain: nexus.tun
project: //project information 
	id: (PROJECT ID)
	lead_tester: (LEAD TESTER)
expiration_date: (WHEN TO KILL)
droplet_config: 
	name: (SPECIFY A DROPLET NAME)
	dns_record:
		apex_domain: (WHAT GOES HERE?)
		sub_domain: (WHAT GOES HERE?)
	 name: (DROPLET NAME)
gitlab_ssh_keys:
	(NAME OF ENTRY):
		(GITLAB KEY NAME): (SSH KEY)

==Theory - nexus-ng is a special version of standard droplets== 

## Standard Droplets 

deployment_type: standard_droplets
project:
	id: 
	lead_tester:
expiration_date:
droplet_configs:
	image: 
	name:
	region
	size:
tags:
gitlab_ssh_keys:
	(NAME OF ENTRY):
		(GITLAB KEY NAME): (SSH KEY)

Qs
- how are namespaces selected? Are they created just for the test?
	- If so, is previous setup required to specify them? (Current assumption: they are entirely arbitrary and setup on the fly)
- what is an apex_domain in a droplet config? Are these preconfigured? 
	- how is the sub-domain chosen? 


# 2. 