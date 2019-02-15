# set up dev env

**Resources:**

- ARM architecture <https://en.wikipedia.org/wiki/ARM_architecture#Cores>

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
echo "deb [arch=amd64] https://download.docker.com/linux/debian $(lsb_release -cs) stable" | sudo tee -a /etc/apt/sources.list.d/docker.list
```

#### add Ubuntu docker repository

Resource <https://docs.docker.com/install/linux/docker-ce/ubuntu/>

Add GPG key

```bash
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
```

Add repository (replace `arch=amd64` with `arch=arm64` for ARM 64 bit architecture)

```bash
echo "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee -a /etc/apt/sources.list.d/docker.list
```

### install docker-ce and containerd

```bash
sudo apt update
sudo apt install -y docker-ce docker-ce-cli containerd.io
```

### install docker-compose

```bash
pip install docker-compose
```

### set up connection from WSL to docker toolbox

Set mount location of windows partitions to root path (eg. /c /d instead of /mnt/c /mnt/d)

```bash
sudo cat > /etc/wsl.conf <<^D
[automount]
root = /
options = "metadata"
^D
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
winUser=$(cmd.exe /c "echo %USERNAME%")
mkdir -p ~/.docker
ln -s /c/Users/${winUser}/.docker/machine/certs/ca.pem ~/.docker/ca.pem
ln -s /c/Users/${winUser}/.docker/machine/certs/ca-key.pem ~/.docker/ca-key.pem
ln -s /c/Users/${winUser}/.docker/machine/certs/cert.pem ~/.docker/cert.pem
ln -s /c/Users/${winUser}/.docker/machine/certs/key.pem ~/.docker/key.pem
```

## kubernetes

Add GPG key

```bash
curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -
```

Add repository

```bash
echo "deb https://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee /etc/apt/sources.list.d/kubernetes.list
```

### install kubectl from repository

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