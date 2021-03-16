   <!-- Copyright 2020 SJULTRA, inc.

   Licensed under the Apache License, Version 2.0 (the "License");
   you may not use this file except in compliance with the License.
   You may obtain a copy of the License at

       http://www.apache.org/licenses/LICENSE-2.0

   Unless required by applicable law or agreed to in writing, software
   distributed under the License is distributed on an "AS IS" BASIS,
   WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
   See the License for the specific language governing permissions and
   limitations under the License. -->
   
[go back](../Getting-Started)

# Straight-forward Docker engine install on Ubuntu, CentOS/RHEL and Debian

## 1. Install on Centos/RHEL

### Prerequisites

If you have older Docker version, uninstall them.

```
sudo yum remove docker \
                  docker-client \
                  docker-client-latest \
                  docker-common \
                  docker-latest \
                  docker-latest-logrotate \
                  docker-logrotate \
                  docker-engine
```

It’s OK if yum reports that none of these packages are installed.


### SET UP THE REPOSITORY

Update the yum package and add the repo

```
sudo yum install -y yum-utils

sudo yum-config-manager \
    --add-repo \
    https://download.docker.com/linux/centos/docker-ce.repo
```

### Install Docker 

	sudo yum install docker-ce docker-ce-cli containerd.io

###  Start Docker

	sudo systemctl start docker

Verity that Docker Engine is installed correctly

	sudo docker run hello-world


### If you want to uninstall Docker, use the following commands

```
sudo yum remove docker-ce docker-ce-cli containerd.io
sudo rm -rf /var/lib/docker
```
<br>

## 2. Install on Ubuntu

### Prerequisites

To get started with Docker Engine on Ubuntu, make sure you have one of following OS

- Ubuntu Focal 20.04 (LTS)
- Ubuntu Bionic 18.04 (LTS)
- Ubuntu Xenial 16.04 (LTS)

Uninstall older version of docker

	sudo apt-get remove docker docker-engine docker.io containerd runc


It’s OK if apt-get reports that none of these packages are installed.


### SET UP THE REPOSITORY

1. Update the apt package index and install packages to allow apt to use a repository over HTTPS:

```
sudo apt-get update

sudo apt-get install \
apt-transport-https \
ca-certificates \
curl \
gnupg-agent \
software-properties-common
```

2. Add Docker’s official GPG key:

        curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -

3. Setup the stable repository

```
sudo add-apt-repository \
"deb [arch=amd64] https://download.docker.com/linux/ubuntu \
$(lsb_release -cs) \
stable"
```

### Install Docker 

```
sudo apt-get update
sudo apt-get install docker-ce docker-ce-cli containerd.io
```

Verity that Docker Engine is installed correctly

        sudo docker run hello-world


### If you want to unninstall Docker, use the following commands

```
sudo apt-get purge docker-ce docker-ce-cli containerd.io
sudo rm -rf /var/lib/docker
```

<br>

## 3. Install on Debian

### Prerequisites

To get started with Docker Engine on Ubuntu, make sure you have one of following OS

- Debian Buster 10 (stable)
- Debian Stretch 9 / Raspbian Stretch

Uninstall older version of docker

	sudo apt-get remove docker docker-engine docker.io containerd runc

It’s OK if apt-get reports that none of these packages are installed.


### SET UP THE REPOSITORY

1. Update the apt package index and install packages to allow apt to use a repository over HTTPS:

```
sudo apt-get update

sudo apt-get install \
apt-transport-https \
ca-certificates \
curl \
gnupg-agent \
software-properties-common
```

2. Add Docker’s official GPG key:

	curl -fsSL https://download.docker.com/linux/debian/gpg | sudo apt-key add -

3. Setup the stable repository

```
sudo add-apt-repository \
   "deb [arch=amd64] https://download.docker.com/linux/debian \
   $(lsb_release -cs) \
   stable"
```

### Install Docker 

```
sudo apt-get update
sudo apt-get install docker-ce docker-ce-cli containerd.io
```

Verity that Docker Engine is installed correctly

        sudo docker run hello-world


### If you want to unninstall Docker, use the following commands

```
sudo rm -rf /var/lib/docker
sudo rm -rf /var/lib/containerd
```

