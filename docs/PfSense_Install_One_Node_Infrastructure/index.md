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

# Install and config pfSense for one-node infrastructure and access it from anywhere with DDNS on Cloudflare

![alt text](/_images/pfSense.png "pfSense")


## One-node infrastructure

Having a single, beefy server can be a problem sometimes. We needed a fleet of virtual servers for various applications and tests and the posibility to access each server separatly from anywhere. In order to do this we need a router for all of them, but because this is a home setup and we only have a single physical machine for thit, the only viable solution is to have a virtual router and that will be pfSense


## The solution

We came up with a solution that uses various technologies from the virtualization world. A few
key points must be met:
- Server
- Capable hypervisor
- pfSense in VM
 
## The server

Our machine is a Dell PowerEdge R420 with the following relevant specifications:

- 2x Intel Xeon E5-2420 1.9 Ghz, 8 cores
- 8x 16GB ECC RAM
- 2x 500GB SSD
- 2x Gigabit NICs

## The hypervisor

There are various hypervisors capable of doing what we needed, like Proxmox, ovirt, KVM, Hyper-V, ESXi etc. We opted for KVM on Ubuntu because it's stable and it's free.

## Networking

Our ISP requires PPPoE authentication, and at each reconnect, our public IP changes. We needed a router for the VMs and for the office, but we first need to take care of the network virtualization part. We created networks, called VM Network and WAN.

- VM Network - this is the equivalent of LAN. All VMs will be connected to this network.
- WAN - our ISP


## Prerequisites before installing pfSense

In order to have good performance with the virtual router the VM should have enough resources. We chose next specs

- 4 CPUs
- 8 GB RAM
- 10 GB storage

##### First make sure you have root access
	sudo su -

Now go to [pfsense](https://www.pfsense.org/download/) website and download the image from there. Select the Architecture: AMD64(64-bit) and Installer: CD image (ISO) Installer
 or simply download it with next command

	wget https://nyifiles.pfsense.org/mirror/downloads/pfSense-CE-2.4.5-RELEASE-amd64.iso.gz


![1](/_images/1.png)
<br/>
Unzip the archive you just download either way

	gunzip pfSense-CE-2.4.5-RELEASE-amd64.iso.gz
	
<br/>

#### Install KVM and bridge-utils to set up the VMs and network


	apt install -y qemu-kvm libvirt-clients libvirt-daemon-system bridge-utils virt-manager
<br/>
Set up the network creating the bridges. Like we said earlier we need 2 networks so we have to create 2 network bridges on the server. In the bridge virbr0 we will add the interface connected to the ISP line and the other bridge, virbr1 will be used for future VM's created in our one-node infrastructure.

	brctl addbr virbr0 && brctl addbr virbr1
<br/>

Add the inteface conected to ISP line to virbr0. In our case the interface name was eno1

	brctl addif eno1 virbr0
	
<br/>

To have your things a little in order let's create a folder for your VM disk's called vmdisks
	
	mkdir vmdisks
<br/>
If you are not directly at the console of the server but connected through ssh connection make sure you use -X argument when connecting so you can directly see VM console. If this does not work for you then you will have to connect with a VNC viewer to VM's console

	ssh -X user@host
<br/>

Create the VM named pfsense with next comand

	sudo virt-install --name pfsense --ram 8048 --disk path=./vmdisks/pfsense.qcow2,size=10 --vcpus 4 --os-type linux --os-variant generic --network bridge=virbr0 --network bridge=virbr1 --graphics vnc --console pty,target_type=serial --cdrom 'pfSense-CE-2.4.5-RELEASE-amd64.iso'

<br/>
The VM it's created and a welcome screen should appear. You can hit Enter

![2](/_images/2.png)

Accept the End User License Agreement.

![3](/_images/3.png)

Select install

![4](/_images/4.png)

Select keyboard layout

![5](/_images/5.png)

Select the Auto(UFS) option

![6](/_images/6.png)

Select the No

![7](/_images/7.png)

Press Enter to reboot the VM

![8](/_images/8.png)


After rebooting, it will ask to configure VLANs. If your env does not have any vlans insert no, if it has, insert yes
The system will list all available network interfaces. Chose the parent interface name of the vlan
Enter the vlan tag
Next the system will ask you to choose 1 interface as the external interface [WAN] and 1 for [LAN].  In our example we have em0 for WAN and em1 for LAN

![9](/_images/9.png)

Instalation complete


## pfSense Dashboard Login

Log in using the following URL https://10.10.1.1 and default credentials
 - Username: admin  
 - Password: pfsense
 
 
![25](/_images/25.png)


## pfSense Setup Wizard

On step 1 simply click Next. On second step set a hostname and a domain if you have one and the Primary and Secondary DNS Server 8.8.4.4 or 8.8.8.8 and 1.1.1.1

![12](/_images/12.png)

Step 3 perform the Timezone and NTP server configuration.

![13](/_images/13.png)

Step 4 select the configuration of the WAN interface. If there is a router conected to your ISP, leave on DHCP if not select the option that meets your needs.

![14](/_images/14.png)

Step 5 configure the LAN interface

![15](/_images/15.png)


Step 6 set up the admin password

![16](/_images/16.png)


On step 7 check and on 8 reload the pfSense configuration and you are done

![16.5](/_images/16.5.png)

## Cloudflare DDNS
Supposing you already have a domain and an account set up on Cloudflare log in and go to My Profile

![18](/_images/18.png)

Select API Tokens and click on View on Global API key

![19](/_images/19.png)

You should get something like this. Take care not to share the API key
Cop

![20](/_images/20.png)

## Set up pfSense DDNS
Go to your pfSense dashboard and from the Service menu, select Dynamic DNS

![24](/_images/24.png)

Click on add and then on Service Type select Cloudflare and then insert your hostname and your domain name

Lower insert your username and on password you have to introduce the API key you just copied.

![22](/_images/22.png)
