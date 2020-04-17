[go back](../README.md)

#### Get an old computer or raspberry pi and install pihole with portainer: 
    
    https://homenetworkguy.com/how-to/install-pihole-on-raspberry-pi-with-docker-and-portainer/
    
#### To install docker on ubuntu:

    1. Add the GPG key for the official Docker repository to your system:
    
    curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -

    2. Add the Docker repository to APT sources:
    
    sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
            
    3. Update the package database
    
    sudo apt-get update

    4. Make sure you are about to install from the Docker repo

    apt-cache policy docker-ce

    5. Install Docker:

    sudo apt-get install -y docker-ce

    6. Check that it’s running:

    sudo systemctl status docker

#### If trying to run pihole in docker on ubuntu server and getting the following error:
    “Failuredriver failed programming external connectivity on endpoint Pihole (c9b821f72080bbc4cf5a736b170e50cfcccbf7c3e312384356edcec8e740d63b): Error starting userland proxy: listen tcp 0.0.0.0:53: bind: address already in use” it means that the port 53 is already in use
    
    To remove this error type this command in the ubuntu terminal:


    sudo systemctl disable systemd-resolved.service
    
    sudo systemctl stop systemd-resolved.service

