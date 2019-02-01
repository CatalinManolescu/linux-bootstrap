# setup dev env

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

## kubernetes

Add GPG key

```bash
curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -
```

Add repository

```bash
echo "deb https://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee -a /etc/apt/sources.list.d/kubernetes.list
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

