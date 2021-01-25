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

# OpenShift-4.5.6-UPI-Installation-on-Libvirt-KVM

!["This is Openshift"](/_images/Openshift.png)

## Prerequisites

Openshift 4.5.6 UPI instalation for who wants to try, learn or teach how to deploy Opneshift 4 on a server. Deployment made using [Redhat Opeshift Baremetal procedure](https://docs.openshift.com/container-platform/4.5/installing/installing_bare_metal/installing-bare-metal.html) as reference





Now let's check if your Server's OS it's up to date and install and enable libvirt/KVM to create the vm's 

```
sudo su -
yum update
yum install -y qemu-kvm qemu-img virt-manager libvirt libvirt-python libvirt-client virt-install virt-viewer bridge-utils libguestfs-tools wget vim
systemctl start libvirtd
systemctl enable libvirtd
```

To keep everithing in one place create a new directory named for example ocp4 and go in it

```
mkdir ocp4
cd ocp4
```

When lbvirt it's installed network called "default" will be created. Just make sure that you have it by running:

```
virsh net-list
```


Setup a dnsmasq server and a dhcp server


```
yum -y install dnsmasq
```

Update hosts

```
echo '
192.168.122.70 bootstrap.mylab.test
192.168.122.71 lb.mylab.test
192.168.122.72 master-1.mylab.test
192.168.122.73 master-2.mylab.test
192.168.122.74 master-3.mylab.test
192.168.122.75 worker-1.mylab.test
192.168.122.76 worker-2.mylab.test
192.168.122.77 worker-3.mylab.test
192.168.122.71 console-openshift-console.apps.mylab.test
192.168.122.71 oauth-openshift.apps.mylab.test
192.168.122.71 centos-myp.apps.mylab.test
192.168.122.71 api.mylab.test api-int.mylab.test
>> /etc/hosts
```

Create dnsmasq configuration

```
echo '
strict-order
except-interface=lo
bind-dynamic
interface=virbr0
srv-host=_etcd-server-ssl._tcp.mylab.test,etcd-0.mylab.test,2380,10,10
srv-host=_etcd-server-ssl._tcp.mylab.test,etcd-1.mylab.test,2380,10,10
srv-host=_etcd-server-ssl._tcp.mylab.test,etcd-2.mylab.test,2380,10,10
dhcp-range=192.168.122.2,192.168.122.254
dhcp-no-override
dhcp-authoritative
dhcp-lease-max=253

address=/apps.mylab.test/192.168.122.71
cname=master-1.mylab.test,etcd-0.mylab.test
cname=master-2.mylab.test,etcd-1.mylab.test
cname=master-3.mylab.test,etcd-2.mylab.test
cname=api.mylab.test,api-int.mylab.test

dhcp-host=52:54:00:aa:04:01,192.168.122.70,bootstrap.mylab.test
dhcp-host=52:54:00:aa:04:00,192.168.122.71,lb.mylab.test
dhcp-host=52:54:00:aa:04:02,192.168.122.72,master-1.mylab.test
dhcp-host=52:54:00:aa:04:03,192.168.122.73,master-2.mylab.test
dhcp-host=52:54:00:aa:04:04,192.168.122.74,master-3.mylab.test
dhcp-host=52:54:00:aa:04:05,192.168.122.75,worker-1.mylab.test
dhcp-host=52:54:00:aa:04:06,192.168.122.76,worker-2.mylab.test
dhcp-host=52:54:00:aa:04:07,192.168.122.77,worker-3.mylab.test
dhcp-host=52:54:00:74:fc:13,192.168.122.78,fromdhcp
> /etc/dnsmasq.conf
```

Restart dnsmasq

```
systemctl restart dnsmasq
systemctl enable dnsmasq
```

# Installation Procedure

Create a ssh key-ed25519 with following commands:

```
ssh-keygen -t d25519
```

Now we have to set up the variables. Everithing can be left the same just the pull secret you have to use your own.
You can copy paste the pull secret. The variable PULL_SEC should have your pull secret without any newlines

```
VIR_NET="default"
HOST_NET=$(ip -4 a s $(virsh net-info $VIR_NET | awk '/Bridge:/{print $2}') | awk '/inet /{print $2}')
HOST_IP=$(echo $HOST_NET | cut -d '/' -f1)
BASE_DOM="test"
WEB_PORT='1234'
CLUSTER_NAME="mylab"
SSH_KEY="/root/.ssh/id_ed25519.pub"
PULL_SEC='<paste-pull-secret>'
```


Create a folder "rhcos-install" Download the RHCOS kernel and initramfs images

```
mkdir rhcos-install
wget https://mirror.openshift.com/pub/openshift-v4/dependencies/rhcos/4.5/4.5.6/rhcos-4.5.6-x86_64-installer-kernel-x86_64 -O ./rhcos-install/vmlinuz
wget https://mirror.openshift.com/pub/openshift-v4/dependencies/rhcos/4.5/4.5.6/rhcos-4.5.6-x86_64-installer-initramfs.x86_64.img -O ./rhcos-install/initramfs.img
```

Generate treeinfo

```
cat <<EOF > rhcos-install/.treeinfo
[general]
arch = x86_64
family = Red Hat CoreOS
platforms = x86_64
version = 4.5.6
[images-x86_64]
initrd = initramfs.img
kernel = vmlinuz
EOF
```

Download the RHCOS bios image 

```
wget https://mirror.openshift.com/pub/openshift-v4/dependencies/rhcos/4.5/4.5.6/rhcos-4.5.6-x86_64-metal.x86_64.raw.gz
```

Create folder for Vms disks and download Centos image

```
mkdir -p /home/vms
wget https://cloud.centos.org/centos/7/images/CentOS-7-x86_64-GenericCloud.qcow2 -O /home/vms/${CLUSTER_NAME}-lb.qcow2
```

Create LB machine disk

```
virt-customize -a  /home/vms/${CLUSTER_NAME}-lb.qcow2 \
  --root-password password:redhat \
  --uninstall cloud-init \
  --ssh-inject root:file:$SSH_KEY --selinux-relabel \
```

Create the machine

```
virt-install --import --name lb.${CLUSTER_NAME}.test \
  --disk /home/vms/${CLUSTER_NAME}-lb.qcow2,size=80 --memory 2048 --cpu host --vcpus 4 \
  --network network=${VIR_NET},mac=52:54:00:aa:04:00 --noreboot --noautoconsole \
  --graphics vnc,listen=0.0.0.0 \
```

Configure load balancing (haproxy).

```
CLUSTER_NAME=mylab
BASE_DOM=test
ssh -l root lb.${CLUSTER_NAME}.${BASE_DOM} <<EOF
yum install -y haproxy
# Allow haproxy to listen on custom ports
semanage port -a -t http_port_t -p tcp 6443
semanage port -a -t http_port_t -p tcp 22623

echo '
global
  log 127.0.0.1 local2
  chroot /var/lib/haproxy
  pidfile /var/run/haproxy.pid
  maxconn 4000
  user haproxy
  group haproxy
  daemon
  stats socket /var/lib/haproxy/stats

defaults
  mode tcp
  log global
  option tcplog
  option dontlognull
  option redispatch
  retries 3
  timeout queue 1m
  timeout connect 10s
  timeout client 1m
  timeout server 1m
  timeout check 10s
  maxconn 3000
# 6443 points to control plan
frontend mylab-api *:6443
  default_backend master-api
backend master-api
  balance source
  server bootstrap bootstrap.mylab.test:6443 check
  server master-1 master-1.mylab.test:6443 check
  server master-2 master-2.mylab.test:6443 check
  server master-3 master-3.mylab.test:6443 check

# 22623 points to control plane
frontend mylab-mapi *:22623
  default_backend master-mapi
backend master-mapi
  balance source
  server bootstrap bootstrap.mylab.test:22623 check
  server master-1 master-1.mylab.test:22623 check
  server master-2 master-2.mylab.test:22623 check
  server master-3 master-3.mylab.test:22623 check

# 80 points to worker nodes
frontend mylab-http *:80
  default_backend ingress-http
backend ingress-http
  balance source
  server worker-1 worker-1.mylab.test:80 check
  server worker-2 worker-2.mylab.test:80 check
  server worker-3 worker-3.mylab.test:80 check

# 443 points to master nodes
frontend mylab-https *:443
  default_backend infra-https
backend infra-https
  balance source
  server master-1 master-1.mylab.test:443 check
  server master-2 master-2.mylab.test:443 check
  server master-3 master-3.mylab.test:443 check
  server worker-1 worker-1.mylab.test:443 check
  server worker-2 worker-2.mylab.test:443 check
  server worker-3 worker-3.mylab.test:443 check
EOF
```

Restart the dnsmasq

```
systemctl restart dnsmasq
```

Download OpenShift client and install binaries and extract

```
wget https://mirror.openshift.com/pub/openshift-v4/clients/ocp/4.5.6/openshift-install-linux-4.5.6.tar.gz
wget https://mirror.openshift.com/pub/openshift-v4/clients/ocp/4.5.6/openshift-client-linux-4.5.6.tar.gz
tar xf openshift-install-linux-4.5.6.tar.gz
tar xf openshift-client-linux-4.5.6.tar.gz
rm -f README.md
```

Create the installation directory for the Openshift installer:

```
mkdir install_dir
```

Generate the install-config.yaml:
Make sure you use your ssh key and pull secret

```
cat <<EOF > install_dir/install-config.yaml
apiVersion: v1
baseDomain: test
compute:
- hyperthreading: Disabled
  name: worker
  replicas: 0
controlPlane:
  hyperthreading: Disabled
  name: master
  replicas: 3
metadata:
  name: mylab
networking:
  clusterNetworks:
  - cidr: 10.128.0.0/14
    hostPrefix: 23
  networkType: OpenShiftSDN
  serviceNetwork:
  - 172.30.0.0/16
platform:
  none: {}
fips: false
pullSecret:<your secret>
sshKey:<your key>
EOF
```

Generate the ignition file consuming install-config.yaml

```
./openshift-install create ignition-configs --dir=./install_dir
```

Check that VMs can access the host on the web port and open a HTTP server to serve the ignition file

```
iptables -I INPUT -p tcp -m tcp --dport ${WEB_PORT} -s ${HOST_NET} -j ACCEPT
python -m SimpleHTTPServer ${WEB_PORT}
```

Create the VMs -> bootstrap, 3 workers and 3 masters

```
mac=1
virt-install --name bootstrap.${CLUSTER_NAME}.test \
 --disk /home/vms/${CLUSTER_NAME}-bootstrap.qcow2,size=50 --ram 14000 --cpu host --vcpus 4 \
 --os-type linux --os-variant centos7.0  \
 --graphics vnc,listen=0.0.0.0 \
 --network network=${VIR_NET},mac=52:54:00:aa:04:0${mac} --noautoconsole \
 --location rhcos-install/ \
 --extra-args "nomodeset rd.neednet=1 coreos.inst=yes coreos.inst.install_dev=vda coreos.inst.image_url=http://${HOST_IP}:${WEB_PORT}/rhcos-4.5.6-x86_64-metal.x86_64.raw.gz  coreos.inst.ignition_url=http://${HOST_IP}:${WEB_PORT}/install_dir/bootstrap.ign"
mac=$(( $mac + 1 ))
for i in {1..3}; do
 virt-install --name master-${i}.${CLUSTER_NAME}.test \
   --disk /home/vms/${CLUSTER_NAME}-master-${i}.qcow2,size=60 --ram 16384 --cpu host --vcpus 5 \
   --os-type linux --os-variant centos7.0 \
   --graphics vnc,listen=0.0.0.0 \
   --network network=${VIR_NET},mac=52:54:00:aa:04:0${mac} --noautoconsole \
   --location rhcos-install/ \
   --extra-args "nomodeset rd.neednet=1 coreos.inst=yes coreos.inst.install_dev=vda coreos.inst.image_url=http://${HOST_IP}:${WEB_PORT}/rhcos-4.5.6-x86_64-metal.x86_64.raw.gz coreos.inst.ignition_url=http://${HOST_IP}:${WEB_PORT}/install_dir/master.ign"
   mac=$(( $mac + 1 ))
done
for i in {1..3}; do
 virt-install --name worker-${i}.${CLUSTER_NAME}.test \
   --disk /home/vms//${CLUSTER_NAME}-worker-${i}.qcow2,size=60 --ram 16384 --cpu host --vcpus 5 \
   --os-type linux --os-variant centos7.0  \
   --graphics vnc,listen=0.0.0.0 \
   --network network=${VIR_NET},mac=52:54:00:aa:04:0${mac} --noautoconsole \
   --location rhcos-install/ \
   --extra-args "nomodeset rd.neednet=1 coreos.inst=yes coreos.inst.install_dev=vda coreos.inst.image_url=http://${HOST_IP}:${WEB_PORT}/rhcos-4.5.6-x86_64-metal.x86_64.raw.gz coreos.inst.ignition_url=http://${HOST_IP}:${WEB_PORT}/install_dir/worker.ign"
   mac=$(( $mac + 1 ))
done
```

After the initial setup the machines will shutdown. Start all of them back

```
for x in bootstrap master-1 master-2 master-3 worker-1 worker-2
do
  virsh start $x.${CLUSTER_NAME}
done
```

Ssh into the bootstrap node and watch the bootkube.service service

```
ssh core@$bootstrap.${CLUSTER_NAME}.${BASE_DOM} journalctl -b -f -u bootkube.service
```

Output should be 

```
Waiting for CEO to finish...
bootstrap.mylab.test bootkube.sh[2473]: I1030 22:03:50.185475       1 waitforceo.go:64] Cluster etcd operator bootstrapped successfully
bootstrap.mylab.test bootkube.sh[2473]: I1030 22:03:50.188735       1 waitforceo.go:58] cluster-etcd-operator bootstrap etcd
bootstrap.mylab.test bootkube.sh[2473]: bootkube.service complete
```

If something's wrong and you want to retry, destroy vm and delete disks with following command:

```
for x in bootstrap master-1 master-2 master-3 worker-1 worker-2 worker-3
do
  virsh destroy $x.mylab.test
  virsh undefine $x.mylab.test --remove-all-storage
done
```


```
export KUBECONFIG=install_dir/auth/kubeconfig
```


Remove bootstrap from load balancer

```
ssh lb.${CLUSTER_NAME}.${BASE_DOM} <<EOF
sed -i '/bootstrap\.${CLUSTER_NAME}\.${BASE_DOM}/d' /etc/haproxy/haproxy.cfg
systemctl restart haproxy
EOF
```

When all the cluster operators are fully available, it should look some thing like this:

```
oc get co
```

```
NAME                                       VERSION   AVAILABLE   PROGRESSING   DEGRADED   SINCE
authentication                             4.5.6     True        False         False      79m
cloud-credential                           4.5.6     True        False         False      95m
cluster-autoscaler                         4.5.6     True        False         False      83m
config-operator                            4.5.6     True        False         False      83m
console                                    4.5.6     True        False         False      73m
csi-snapshot-controller                    4.5.6     True        False         False      84m
dns                                        4.5.6     True        False         False      91m
etcd                                       4.5.6     True        False         False      91m
image-registry                             4.5.6     True        False         False      84m
ingress                                    4.5.6     True        False         False      84m
insights                                   4.5.6     True        False         False      84m
kube-apiserver                             4.5.6     True        False         False      90m
kube-controller-manager                    4.5.6     True        False         False      90m
kube-scheduler                             4.5.6     True        False         False      89m
kube-storage-version-migrator              4.5.6     True        False         False      85m
machine-api                                4.5.6     True        False         False      84m
machine-approver                           4.5.6     True        False         False      89m
machine-config                             4.5.6     True        False         False      90m
marketplace                                4.5.6     True        False         False      84m
monitoring                                 4.5.6     True        False         False      77m
network                                    4.5.6     True        False         False      92m
node-tuning                                4.5.6     True        False         False      92m
openshift-apiserver                        4.5.6     True        False         False      85m
openshift-controller-manager               4.5.6     True        False         False      85m
openshift-samples                          4.5.6     True        False         False      79m
operator-lifecycle-manager                 4.5.6     True        False         False      91m
operator-lifecycle-manager-catalog         4.5.6     True        False         False      91m
operator-lifecycle-manager-packageserver   4.5.6     True        False         False      85m
service-ca                                 4.5.6     True        False         False      92m
storage                                    4.5.6     True        False         False      84m
```

