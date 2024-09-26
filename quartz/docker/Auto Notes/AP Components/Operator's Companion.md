[Repo](https://git.nopsled.me/justin.angel/operators-companion)

# files

dev-notes-vault (d)
docker (d)
poc (d)
	wapp (d)
		public (d)
			data (d)
				.gitkeep
			yaml_forms(d)
				.gitkeep
			bhis.png
			bootstrap.min.css
			favicon.ico
			favicon.png
			index.html
			logo192.png
			logo512.png
			react-bootstrap.min.js
			react-dom.production.min.js
			react.production.min.js
			robots.txt
		src (d)
		README.md
		chromium.js
		package-lock.json
		package.json
	requirements.txt
	yaml_forms.py
	yaml_forms_manifest.yml
.gitignore
.gitlab-ci.yml
README.md



# .gitlab-ci.yml

```YAML
stages:            #five stages! git, build, deploy, yaml, containerize
  - get data
  - build
  - deploy
  - yaml-schema
  - containerize-yaml-schema

# ==============
# COMMON ANCHORS
# ==============

#Anchors are kind of like classes, they can be reused across a 
#variety of stages or passed to scripts. 

#Variables required for tailscale
.tailscale-variables: &tailscale-variables #where are these stored?
  HEADSCALE_URL: $HEADSCALE_URL
  TAILSCALE_HOSTNAME: $TAILSCALE_HOSTNAME
  TAILSCALE_HTTP_PORT: $TAILSCALE_HTTP_PORT
  TAILSCALE_KEY: $TAILSCALE_KEY
  TAILSCALE_SOCKS_PORT: $TAILSCALE_SOCKS_PORT

#proxies for tailscale
.http_proxies: &tailscale-proxies
  http_proxy: http://tailscale:8080 #why are there two?
  HTTP_PROXY: http://tailscale:8080

#connect to the tailscale service in the common repo
.tailscale-service: &tailscale-service
  name: git.nopsled.me:5050/justin.angel/automation-registry/common/cicd-tailscale-service #common automation service for Angel projects, specific for tailscale
  alias: tailscale #alias 

#start the tailscale service
.init-tailscale: &init-tailscale | #why is "|" here?
  curl --proxy "http://tailscale:$TAILSCALE_HTTP_PORT" --insecure --fail \
    --connect-timeout 20 --retry-connrefused --retry 4 \
    https://100.64.0.1:8200 1>/dev/null

#??
.tailscale-send-chunk: &tailscale-send-chunk
  services:
    - *tailscale-service #pull in the tailscale service
  variables: #define variables using the anchors
    <<: *tailscale-variables 
    <<: *tailscale-proxies
  before_script: #run init-tailscale before running service
    - *init-tailscale

.docker-auth-before: &docker-auth-before |
  mkdir -p ~/.docker
  echo "$DOCKER_AUTH_CONFIG" > ~/.docker/config.json

.master-push-rule: &master-push-rule
  if: $CI_COMMIT_BRANCH == "master" && $CI_PIPELINE_SOURCE == "push"

.rebuild-always-rule: &rebuild-always-rule
  if: $REBUILD == "true"
  when: always

.tmp-artifacts: &tmp-artifacts
  untracked: true
  expire_in: 3 hours

# =========
# CICD JOBS
# =========

create yaml schema container:
  # Container that can be used to run the yaml-schema-dumper
    # In situations where the dumper-script requires other libraries,
    # distinct containers should be implemented with the dependencies.
  stage: containerize-yaml-schema
  image: docker
  rules:
    - if: $BUILD_YAML_SCHEMA_CONTAINER == "true"
    - <<: *master-push-rule
      changes:
      - docker/schema_dumper*
  before_script:
    - *docker-auth-before
  script:
    - docker build -f docker/schema_dumper.Dockerfile -t git.nopsled.me:5050/justin.angel/automation-registry/opcomp/yaml-schema-dumper:latest .
    - docker push git.nopsled.me:5050/justin.angel/automation-registry/opcomp/yaml-schema-dumper:latest

pull digitalocean diver data:
  # Retrieve the latest DigitalOcean diver data from Minio.
  stage: get data
  image: git.nopsled.me:5050/justin.angel/automation-registry/common/minio
  rules:
    - *rebuild-always-rule
  services:
    - *tailscale-service
  variables:
    <<: *tailscale-variables
    <<: *tailscale-proxies
  artifacts:
    <<: *tmp-artifacts
    paths:
      - poc/wapp/public/data/digitalocean_diver_dump.yml
  before_script:
    - *docker-auth-before
    - *init-tailscale
  script:
    - mc alias set minio $MINIO_URL "$MINIO_ACCESS_KEY" "$MINIO_SECRET_KEY"
    - mc cp minio/infrastructure-requests/digitalocean-diver-dump.json poc/wapp/public/data/digitalocean_diver_dump.yml

get ssh keys:
    stage: get data
    image:
      name: git.nopsled.me:5050/justin.angel/automation-registry/common/gitstrument
      entrypoint: [""]
    rules:
      - *rebuild-always-rule
    artifacts:
      <<: *tmp-artifacts
      paths:
          - poc/wapp/public/data/ssh_keys.yml
    script:
        - GITLAB_URL="$CI_SERVER_URL" GITLAB_TOKEN="$RO_PAT" gitstrument users ssh-keys yaml-index > poc/wapp/public/data/ssh_keys.yml

get yaml forms:
    stage: get data
    image: python:3.9
    rules:
      - *rebuild-always-rule
    artifacts:
      <<: *tmp-artifacts
      paths:
        - poc/wapp/public/data/yaml_forms_manifest.yml
        - poc/wapp/public/yaml_forms
    script:
        - cd poc
        - pip3 install -r requirements.txt
        - python3 yaml_forms.py
        - rm -rf wapp/public/yaml_forms
        - mv yaml_forms_output wapp/public/yaml_forms
        - mv yaml_forms_manifest.yml wapp/public/data/yaml_forms_manifest.yml

build wapp:
    stage: build
    image:  node:20.15.1-bullseye-slim
    rules:
      - *rebuild-always-rule
    dependencies:
      - get ssh keys
      - get yaml forms
      - pull digitalocean diver data
    artifacts:
      <<: *tmp-artifacts
      paths:
        - poc/wapp/build
    script:
      - cd poc/wapp
      - npm install
      - npm run build

send webroot:
    stage: deploy
    image: debian:bullseye
    rules:
      - *rebuild-always-rule
    dependencies:
      - build wapp
    script:
      - apt update && apt install openssh-client rsync -y
      - mkdir ~/.ssh
      - cat "$VPS_SSH_KEY" > ~/.ssh/id_rsa
      - chmod -R 700 ~/.ssh
      - ssh-keyscan -t rsa opcomp.autonode.net > ~/.ssh/known_hosts
      - ssh root@opcomp.autonode.net "rm -rf /root/operators-companion/docker/webroot/*"
      - rsync -rv poc/wapp/build/ root@opcomp.autonode.net:/root/operators-companion/docker/webroot

	
```
