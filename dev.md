# set up dev env

**Resources:**

- ARM architecture <https://en.wikipedia.org/wiki/ARM_architecture#Cores>

## virtual box

Resource <https://www.virtualbox.org/wiki/Linux_Downloads>

Add GPG key

```bash
curl -fsSL https://www.virtualbox.org/download/oracle_vbox_2016.asc | sudo apt-key add -
```

Add repository

```bash
echo "deb [arch=amd64] https://download.virtualbox.org/virtualbox/debian $(lsb_release -cs) contrib" | sudo tee /etc/apt/sources.list.d/virtual-box.list
```

## docker

### add docker repository

#### add Debian docker repository

Resource <https://docs.docker.com/install/linux/docker-ce/debian/>

Add GPG key

```bash
curl -fsSL https://download.docker.com/linux/debian/gpg | sudo apt-key add -
```

Add repository (replace `arch=amd64` with `arch=arm64` for ARM 64 bit architecture)

```bash
echo "deb [arch=amd64] https://download.docker.com/linux/debian $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list
```

```bash
sudo apt update
sudo apt install -y virtualbox-6.0
```

#### add Ubuntu docker repository

Resource <https://docs.docker.com/install/linux/docker-ce/ubuntu/>

Add GPG key

```bash
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
```

Add repository (replace `arch=amd64` with `arch=arm64` for ARM 64 bit architecture)

```bash
echo "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list
```

### install docker-ce and containerd

```bash
sudo apt update
sudo apt install -y docker-ce docker-ce-cli containerd.io
```

### install docker-compose

Using `pip` 

```bash
pip install docker-compose
```

Or get latest from GitHub

1. create upgrade script

    ```bash
    sudo tee /usr/local/bin/docker-compose-upgrade <<EOF
    #!/usr/bin/env bash

    DOCKER_COMPOSE_VERSION=\`curl --silent "https://api.github.com/repos/docker/compose/releases" |  jq -r '[.[] | select( .prerelease == false ) | {tag_name, prerelease, tarball_url}][0].tag_name'\`

    echo "upgrading docker-compose to version '\$DOCKER_COMPOSE_VERSION'"

    sudo curl -L https://github.com/docker/compose/releases/download/\${DOCKER_COMPOSE_VERSION}/docker-compose-\`uname -s\`-\`uname -m\` -o /usr/local/bin/docker-compose
    sudo chmod +x /usr/local/bin/docker-compose
    EOF
    ```

2. install docker-compose

    ```bash
    sudo chmod +x /usr/local/bin/docker-compose-upgrade
    docker-compose-upgrade
    ```

### change default docker data location

```bash
# stop docker service
sudo systemctl stop docker

# create daemon configuration file https://docs.docker.com/engine/reference/commandline/dockerd//#daemon-configuration-file
sudo mkdir -p /etc/docker
# replace with your desired path
sudo tee /etc/docker/daemon.json <<EOF
{
    "data-root": "/mnt/docker-data",
    "storage-driver": "overlay2",
    "log-opts": {
      "max-size": "10m",
      "max-file": "3"
    }
}
EOF
```

### set up connection from WSL to docker toolbox

Set mount location of windows partitions to root path (eg. /c /d instead of /mnt/c /mnt/d)

```bash
sudo tee /etc/wsl.conf <<EOF
[automount]
root = /
enabled = true
options = "metadata,umask=22,fmask=11"
EOF
```

Edit `.bashrc` and add

```bash
alias docker-machine="docker-machine.exe"
# add docker env vars and convert paths
eval $(docker-machine env default --shell bash | sed 's?\\?/?g;s?C:/?/c/?g')
export COMPOSE_CONVERT_WINDOWS_PATHS=1
```

Share certificates from Docker Toolbox on Windows with Docker client on WSL

```bash
win_user=$(cmd.exe /c "echo %USERNAME%")
mkdir -p ~/.docker
ln -s /c/Users/${win_user}/.docker/machine/certs/ca.pem ~/.docker/ca.pem
ln -s /c/Users/${win_user}/.docker/machine/certs/ca-key.pem ~/.docker/ca-key.pem
ln -s /c/Users/${win_user}/.docker/machine/certs/cert.pem ~/.docker/cert.pem
ln -s /c/Users/${win_user}/.docker/machine/certs/key.pem ~/.docker/key.pem
```
## java

### install adoptopenjdk on Debian

Add GPG key

```bash
wget -qO - https://adoptopenjdk.jfrog.io/adoptopenjdk/api/gpg/key/public | sudo apt-key add -
```

Add repository

```bash
echo "deb [arch=amd64] https://adoptopenjdk.jfrog.io/adoptopenjdk/deb $(lsb_release -cs) main" | sudo tee /etc/apt/sources.list.d/adoptopenjdk.list
```

Install

```bash
sudo apt update && sudo apt install adoptopenjdk-11-hotspot
```

## kubernetes

### install kind

Install latest from github

1. create upgrade script

    ```bash
    sudo tee /usr/local/bin/kind-upgrade <<EOF
    #!/usr/bin/env bash
    
    PLATFORM=linux-amd64
    KIND_VERSION=\`curl --silent "https://api.github.com/repos/kubernetes-sigs/kind/releases" |  jq -r '[.[] | select( .prerelease == false ) | {tag_name, prerelease, tarball_url}][0].tag_name'\`

    echo "upgrading kind to version '\$KIND_VERSION'"

    sudo curl -L https://github.com/kubernetes-sigs/kind/releases/download/\${KIND_VERSION}/kind-\${PLATFORM} -o /usr/local/bin/kind
    sudo chmod +x /usr/local/bin/kind
    EOF
    sudo chmod +x /usr/local/bin/kind-upgrade
    ```

2. install kind

    ```bash
    kind-upgrade
    ```

### install kubectl from debian package repository

Add GPG key

```bash
curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -
```

Add repository

```bash
echo "deb https://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee /etc/apt/sources.list.d/kubernetes.list
```

Install kubectl

```bash
sudo apt update
sudo apt install -y kubectl
```

### install kubectl from github

```bash
curl -LO https://storage.googleapis.com/kubernetes-release/release/$(curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt)/bin/linux/amd64/kubectl \
  && chmod +x ./kubectl && sudo mv ./kubectl /usr/local/bin/kubectl
```

## Visual Studio Code

Add GPG key

```bash
curl -s https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor | sudo apt-key add -
```

Add repository

```bash
echo "deb [arch=amd64] https://packages.microsoft.com/repos/vscode stable main" | sudo tee /etc/apt/sources.list.d/vscode.list
```

### install vscode from repository

```bash
sudo apt update
sudo apt install -y code
```

## azure cli

Add GPG key

```bash
sudo apt-key --keyring /etc/apt/trusted.gpg.d/Microsoft.gpg adv \
     --keyserver packages.microsoft.com \
     --recv-keys BC528686B50D79E339D3721CEB3E94ADBE1229CF
```

Add repository

```bash
echo "deb [arch=amd64] https://packages.microsoft.com/repos/azure-cli/ $(lsb_release -cs) main" | sudo tee /etc/apt/sources.list.d/azure-cli.list
```

### install azure cli from repository

```bash
sudo apt-get update
sudo apt-get install azure-cli
```

## terraform

### install latest terraform cli

1. create upgrade script
    
    ```bash
    sudo tee /usr/local/bin/terraform-upgrade <<EOF
    #!/usr/bin/env bash

    TERRAFORM_VERSION=\`curl --silent "https://api.github.com/repos/hashicorp/terraform/releases" |  jq -r '[.[] | select( .prerelease == false ) | {tag_name, prerelease, tarball_url}][0].tag_name'\`
    TERRAFORM_VERSION=\${TERRAFORM_VERSION//v}
    echo "upgrading terraform to version '\$TERRAFORM_VERSION'"
    
    curl -L https://releases.hashicorp.com/terraform/\${TERRAFORM_VERSION}/terraform_\${TERRAFORM_VERSION}_linux_amd64.zip -o /tmp/terraform_linux_amd64.zip
    
    sudo unzip -u /tmp/terraform_linux_amd64.zip -d /usr/local/bin && rm -f /tmp/terraform_linux_amd64.zip
    sudo chmod +x /usr/local/bin/terraform
    EOF
    sudo chmod +x /usr/local/bin/terraform-upgrade
    ```

2. install terraform

    ```bash
    terraform-upgrade
    ```
