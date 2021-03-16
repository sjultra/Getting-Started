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

#### Get an old computer or raspberry pi and install pihole with portainer: 
    
    https://homenetworkguy.com/how-to/install-pihole-on-raspberry-pi-with-docker-and-portainer/

 ## Install on Ubuntu

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

        or

        sudo systemctl status docker


### If you want to unninstall Docker, use the following commands

```
sudo apt-get purge docker-ce docker-ce-cli containerd.io
sudo rm -rf /var/lib/docker
```

#### If trying to run pihole in docker on ubuntu server and getting the following error:
    “Failuredriver failed programming external connectivity on endpoint Pihole (c9b821f72080bbc4cf5a736b170e50cfcccbf7c3e312384356edcec8e740d63b): Error starting userland proxy: listen tcp 0.0.0.0:53: bind: address already in use” it means that the port 53 is already in use
    
    To remove this error type this command in the ubuntu terminal:


    sudo systemctl disable systemd-resolved.service
    
    sudo systemctl stop systemd-resolved.service

