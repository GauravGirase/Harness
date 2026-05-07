# Harness
## Installation
- Docker install
```bash
sudo apt-get update
sudo apt-get install ca-certificates curl -y
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc

# Add the repository to Apt sources:
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "${UBUNTU_CODENAME:-$VERSION_CODENAME}") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update

sudo apt-get install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

# Add user to docker group (log out / in or newgrp to apply)
sudo usermod -aG docker  ubuntu
newgrp docker
```
### What are connectors?
connectors to connect Harness to your tools, such as Kubernetes clusters, code and artifact repositories, and cloud platforms.
## Signup with Harness https://harness.io
### Install delegates (Docker)
```bash
docker run -d --cpus=1 --memory=2g --net=host \
  -v /var/run/docker.sock:/var/run/docker.sock \
  -v /tmp:/tmp \
  -e DELEGATE_NAME=docker-delegate-ci \
  -e NEXT_GEN="true" \
  -e DELEGATE_TYPE="DOCKER" \
  -e ACCOUNT_ID=H5ffoP7pS4WUIHJTwRhGag \
  -e DELEGATE_TOKEN=<TOKEM> \
  -e DELEGATE_TAGS="linux-amd64" \
  -e MANAGER_HOST_AND_PORT=https://app.harness.io us-docker.pkg.dev/gar-prod-setup/harness-public/harness/delegate:26.04.89003
```
### Install Set up a Linux local runner
- Install the Harness Docker Runner
https://github.com/harness/harness-docker-runner/releases
```bash
wget https://github.com/harness/harness-docker-runner/releases/download/v0.1.22/harness-docker-runner-linux-amd64
sudo chmod +x harness-docker-runner-linux-amd64
sudo -E ./harness-docker-runner-linux-amd64 server
```
### pipeline.yaml
```bash
pipeline:
  name: Demo-1
  identifier: Demo1
  projectIdentifier: default_project
  orgIdentifier: default
  description: Demo pipeline for harness poc
  tags: {}
  properties:
    ci:
      codebase:
        connectorRef: org.Githubconnector
        repoName: Harness
        build: <+input>
  stages:
    - stage:
        name: CI
        identifier: CI
        description: ""
        type: CI
        spec:
          cloneCodebase: true
          caching:
            enabled: true
            override: true
          buildIntelligence:
            enabled: true
          platform:
            os: Linux
            arch: Amd64
          runtime:
            type: Docker
            spec: {}
          execution:
            steps:
              - step:
                  type: GitClone
                  name: GitClone_1
                  identifier: GitClone_1
                  spec:
                    connectorRef: org.Githubconnector
                    repoName: Harness
                    build:
                      type: branch
                      spec:
                        branch: main
```


