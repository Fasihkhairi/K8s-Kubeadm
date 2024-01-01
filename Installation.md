# Kubeadm Installation Guide

This guide outlines the steps needed to set up a Kubernetes cluster using kubeadm.

## Pre-requisites

* Ubuntu OS (Xenial or later)
* sudo privileges
* Internet access
* t2.medium instance type or higher

---

## Both Master & Worker Node

Run the following commands on both the master and worker nodes to prepare them for kubeadm.

```bash
# using 'sudo su' is not a good practice.
sudo apt update
sudo apt-get install -y apt-transport-https ca-certificates curl
sudo apt install docker.io -y

sudo systemctl enable --now docker # enable and start in single command.

# Adding GPG keys.
curl -fsSL "https://packages.cloud.google.com/apt/doc/apt-key.gpg" | sudo gpg --dearmor -o /etc/apt/trusted.gpg.d/kubernetes-archive-keyring.gpg

# Add the repository to the sourcelist.
echo 'deb https://packages.cloud.google.com/apt kubernetes-xenial main' | sudo tee /etc/apt/sources.list.d/kubernetes.list

sudo apt update 
sudo apt install kubeadm=1.20.0-00 kubectl=1.20.0-00 kubelet=1.20.0-00 -y
```

**Sample Command run on master node**

<kbd>![image](https://github.com/paragpallavsingh/kubernetes-kickstarter/assets/40052830/a4e7a4af-31fa-40cf-bb9e-64ba18999cb5)</kbd>

<kbd>![image](https://github.com/paragpallavsingh/kubernetes-kickstarter/assets/40052830/acf157b8-5c7b-44e7-91ef-b5437053be60)</kbd>

<kbd>![image](https://github.com/paragpallavsingh/kubernetes-kickstarter/assets/40052830/8f960aae-3706-43cd-bac8-1903fbe8196d)</kbd>

---

## Master Node

1. Initialize the Kubernetes master node.

    ```bash
    sudo kubeadm init
    ```
    <kbd>![image](https://github.com/paragpallavsingh/kubernetes-kickstarter/assets/40052830/4fed3d68-eb41-423d-b83f-35c3cc11476e)</kbd>

    After succesfully running, your Kubernetes control plane will be initialized successfully.

   <kbd>![image](https://github.com/paragpallavsingh/kubernetes-kickstarter/assets/40052830/760276f4-9146-4bc1-aa92-48cc1c0b13f4)</kbd>


3. Set up local kubeconfig (both for root user and normal user):

    ```bash
    mkdir -p $HOME/.kube
    sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
    sudo chown $(id -u):$(id -g) $HOME/.kube/config
    ```

    <kbd>![image](https://github.com/paragpallavsingh/kubernetes-kickstarter/assets/40052830/f647adc1-0976-490e-b9c9-f6f96908d6fe)</kbd>


4. Apply Weave network:

    ```bash
    kubectl apply -f https://github.com/weaveworks/weave/releases/download/v2.8.1/weave-daemonset-k8s.yaml
    ```

    <kbd>![image](https://github.com/paragpallavsingh/kubernetes-kickstarter/assets/40052830/ec7b4684-7719-4d09-81d8-eee27b98972a)</kbd>


5. Generate a token for worker nodes to join:

    ```bash
    sudo kubeadm token create --print-join-command
    ```

    <kbd>![image](https://github.com/paragpallavsingh/kubernetes-kickstarter/assets/40052830/0370839b-bbac-415c-9d5a-9ab52cd3108b)</kbd>

6. Expose port 6443 in the Security group for the Worker to connect to Master Node

<kbd>![image](https://github.com/paragpallavsingh/kubernetes-kickstarter/assets/40052830/b3f5df01-acb0-419f-aa70-6d51819f4ec0)</kbd>


---

## Worker Node

1. Run the following commands on the worker node.

    ```bash
    sudo kubeadm reset pre-flight checks
    ```
    <kbd>![image](https://github.com/paragpallavsingh/kubernetes-kickstarter/assets/40052830/3d29912b-f1a3-4e0b-a6ee-6c9cc5db49fb)</kbd>

2. Paste the join command you got from the master node and append `--v=5` at the end.
*Make sure either you are working as sudo user or use `sudo` before the command*

   <kbd>![image](https://github.com/paragpallavsingh/kubernetes-kickstarter/assets/40052830/c41e3213-7474-43f9-9a7b-a75694be582a)</kbd>

   After succesful join->
   <kbd>![image](https://github.com/paragpallavsingh/kubernetes-kickstarter/assets/40052830/c530b65a-4afd-4b1d-9748-421c216d64cd)</kbd>

---

## Verify Cluster Connection

On Master Node:

```bash
kubectl get nodes
```
<kbd>![image](https://github.com/paragpallavsingh/kubernetes-kickstarter/assets/40052830/4ed4dcac-502a-4cc1-a63e-c9cbb0199428)</kbd>

---

## Optional: Labeling Nodes

If you want to label worker nodes, you can use the following command:

```bash
kubectl label node <node-name> node-role.kubernetes.io/worker=worker
```

---

# My Terminal (Master)

khairi@khairi-Lenovo-YOGA-520-14IKB:~/Desktop$ cd
khairi@khairi-Lenovo-YOGA-520-14IKB:~$ cd 
khairi@khairi-Lenovo-YOGA-520-14IKB:~$ cd Downloads
khairi@khairi-Lenovo-YOGA-520-14IKB:~/Downloads$ ssh -i "two-tier-app-k8s.pem" ubuntu@ec2-3-27-110-9.ap-southeast-2.compute.amazonaws.com
The authenticity of host 'ec2-3-27-110-9.ap-southeast-2.compute.amazonaws.com (3.27.110.9)' can't be established.
ED25519 key fingerprint is SHA256:2Xp3GJ8o/SafYn7Jlb3YPvQ0kb4nGe2a6XD07luQklM.
This key is not known by any other names
Are you sure you want to continue connecting (yes/no/[fingerprint])? 
Host key verification failed.
khairi@khairi-Lenovo-YOGA-520-14IKB:~/Downloads$ sudo ssh -i "two-tier-app-k8s.pem" ubuntu@ec2-3-27-110-9.ap-southeast-2.compute.amazonaws.com
[sudo] password for khairi: 
The authenticity of host 'ec2-3-27-110-9.ap-southeast-2.compute.amazonaws.com (3.27.110.9)' can't be established.
ED25519 key fingerprint is SHA256:2Xp3GJ8o/SafYn7Jlb3YPvQ0kb4nGe2a6XD07luQklM.
This key is not known by any other names
Are you sure you want to continue connecting (yes/no/[fingerprint])? y
Please type 'yes', 'no' or the fingerprint: yes
Warning: Permanently added 'ec2-3-27-110-9.ap-southeast-2.compute.amazonaws.com' (ED25519) to the list of known hosts.
Welcome to Ubuntu 22.04.3 LTS (GNU/Linux 6.2.0-1017-aws x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  System information as of Mon Jan  1 01:13:37 UTC 2024

  System load:  0.119140625       Processes:             111
  Usage of /:   20.6% of 7.57GB   Users logged in:       0
  Memory usage: 5%                IPv4 address for eth0: 172.31.4.107
  Swap usage:   0%

Expanded Security Maintenance for Applications is not enabled.

0 updates can be applied immediately.

Enable ESM Apps to receive additional future security updates.
See https://ubuntu.com/esm or run: sudo pro status


The list of available updates is more than a week old.
To check for new updates run: sudo apt update


The programs included with the Ubuntu system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Ubuntu comes with ABSOLUTELY NO WARRANTY, to the extent permitted by
applicable law.

To run a command as administrator (user "root"), use "sudo <command>".
See "man sudo_root" for details.

ubuntu@ip-172-31-4-107:~$ # using 'sudo su' is not a good practice.
sudo apt update
sudo apt-get install -y apt-transport-https ca-certificates curl
sudo apt install docker.io -y

sudo systemctl enable --now docker # enable and start in single command.

# Adding GPG keys.
curl -fsSL "https://packages.cloud.google.com/apt/doc/apt-key.gpg" | sudo gpg --dearmor -o /etc/apt/trusted.gpg.d/kubernetes-archive-keyring.gpg

# Add the repository to the sourcelist.
echo 'deb https://packages.cloud.google.com/apt kubernetes-xenial main' | sudo tee /etc/apt/sources.list.d/kubernetes.list

sudo apt update 
sudo apt install kubeadm=1.20.0-00 kubectl=1.20.0-00 kubelet=1.20.0-00 -y
Hit:1 http://ap-southeast-2.ec2.archive.ubuntu.com/ubuntu jammy InRelease
Get:2 http://ap-southeast-2.ec2.archive.ubuntu.com/ubuntu jammy-updates InRelease [119 kB]
Get:3 http://ap-southeast-2.ec2.archive.ubuntu.com/ubuntu jammy-backports InRelease [109 kB]
Get:4 http://ap-southeast-2.ec2.archive.ubuntu.com/ubuntu jammy/universe amd64 Packages [14.1 MB]
Get:5 http://ap-southeast-2.ec2.archive.ubuntu.com/ubuntu jammy/universe Translation-en [5652 kB]
Get:6 http://security.ubuntu.com/ubuntu jammy-security InRelease [110 kB]           
Get:7 http://ap-southeast-2.ec2.archive.ubuntu.com/ubuntu jammy/universe amd64 c-n-f Metadata [286 kB]
Get:8 http://ap-southeast-2.ec2.archive.ubuntu.com/ubuntu jammy/multiverse amd64 Packages [217 kB]
Get:9 http://ap-southeast-2.ec2.archive.ubuntu.com/ubuntu jammy/multiverse Translation-en [112 kB]
Get:10 http://ap-southeast-2.ec2.archive.ubuntu.com/ubuntu jammy/multiverse amd64 c-n-f Metadata [8372 B]
Get:11 http://ap-southeast-2.ec2.archive.ubuntu.com/ubuntu jammy-updates/main amd64 Packages [1263 kB]
Get:12 http://ap-southeast-2.ec2.archive.ubuntu.com/ubuntu jammy-updates/main Translation-en [260 kB]
Get:13 http://ap-southeast-2.ec2.archive.ubuntu.com/ubuntu jammy-updates/restricted amd64 Packages [1250 kB]
Get:14 http://ap-southeast-2.ec2.archive.ubuntu.com/ubuntu jammy-updates/restricted Translation-en [203 kB]
Get:15 http://ap-southeast-2.ec2.archive.ubuntu.com/ubuntu jammy-updates/universe amd64 Packages [1020 kB]
Get:16 http://ap-southeast-2.ec2.archive.ubuntu.com/ubuntu jammy-updates/universe Translation-en [226 kB]
Get:17 http://ap-southeast-2.ec2.archive.ubuntu.com/ubuntu jammy-updates/universe amd64 c-n-f Metadata [22.1 kB]
Get:18 http://ap-southeast-2.ec2.archive.ubuntu.com/ubuntu jammy-updates/multiverse amd64 Packages [41.6 kB]
Get:19 http://ap-southeast-2.ec2.archive.ubuntu.com/ubuntu jammy-updates/multiverse Translation-en [9768 B]
Get:20 http://ap-southeast-2.ec2.archive.ubuntu.com/ubuntu jammy-updates/multiverse amd64 c-n-f Metadata [472 B]
Get:21 http://ap-southeast-2.ec2.archive.ubuntu.com/ubuntu jammy-backports/main amd64 Packages [41.7 kB]
Get:22 http://ap-southeast-2.ec2.archive.ubuntu.com/ubuntu jammy-backports/main Translation-en [10.5 kB]
Get:23 http://ap-southeast-2.ec2.archive.ubuntu.com/ubuntu jammy-backports/main amd64 c-n-f Metadata [388 B]
Get:24 http://ap-southeast-2.ec2.archive.ubuntu.com/ubuntu jammy-backports/restricted amd64 c-n-f Metadata [116 B]
Get:25 http://ap-southeast-2.ec2.archive.ubuntu.com/ubuntu jammy-backports/universe amd64 Packages [24.3 kB]
Get:26 http://ap-southeast-2.ec2.archive.ubuntu.com/ubuntu jammy-backports/universe Translation-en [16.5 kB]
Get:27 http://ap-southeast-2.ec2.archive.ubuntu.com/ubuntu jammy-backports/universe amd64 c-n-f Metadata [644 B]
Get:28 http://ap-southeast-2.ec2.archive.ubuntu.com/ubuntu jammy-backports/multiverse amd64 c-n-f Metadata [116 B]
Get:29 http://security.ubuntu.com/ubuntu jammy-security/main amd64 Packages [1051 kB]      
Get:30 http://security.ubuntu.com/ubuntu jammy-security/main Translation-en [200 kB]
Get:31 http://security.ubuntu.com/ubuntu jammy-security/restricted amd64 Packages [1226 kB]
Get:32 http://security.ubuntu.com/ubuntu jammy-security/restricted Translation-en [200 kB]
Get:33 http://security.ubuntu.com/ubuntu jammy-security/universe amd64 Packages [823 kB]
Get:34 http://security.ubuntu.com/ubuntu jammy-security/universe Translation-en [156 kB]
Get:35 http://security.ubuntu.com/ubuntu jammy-security/universe amd64 c-n-f Metadata [16.8 kB]
Get:36 http://security.ubuntu.com/ubuntu jammy-security/multiverse amd64 Packages [36.5 kB]
Get:37 http://security.ubuntu.com/ubuntu jammy-security/multiverse Translation-en [7060 B]
Get:38 http://security.ubuntu.com/ubuntu jammy-security/multiverse amd64 c-n-f Metadata [260 B]
Fetched 28.8 MB in 4s (6662 kB/s)                               
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
26 packages can be upgraded. Run 'apt list --upgradable' to see them.
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
ca-certificates is already the newest version (20230311ubuntu0.22.04.1).
ca-certificates set to manually installed.
curl is already the newest version (7.81.0-1ubuntu1.15).
curl set to manually installed.
The following NEW packages will be installed:
  apt-transport-https
0 upgraded, 1 newly installed, 0 to remove and 26 not upgraded.
Need to get 1510 B of archives.
After this operation, 170 kB of additional disk space will be used.
Get:1 http://ap-southeast-2.ec2.archive.ubuntu.com/ubuntu jammy-updates/universe amd64 apt-transport-https all 2.4.11 [1510 B]
Fetched 1510 B in 0s (93.4 kB/s)              
Selecting previously unselected package apt-transport-https.
(Reading database ... 64799 files and directories currently installed.)
Preparing to unpack .../apt-transport-https_2.4.11_all.deb ...
Unpacking apt-transport-https (2.4.11) ...
Setting up apt-transport-https (2.4.11) ...
Scanning processes...                                                                                                                         
Scanning linux images...                                                                                                                      

Running kernel seems to be up-to-date.

No services need to be restarted.

No containers need to be restarted.

No user sessions are running outdated binaries.

No VM guests are running outdated hypervisor (qemu) binaries on this host.
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
The following additional packages will be installed:
  bridge-utils containerd dns-root-data dnsmasq-base pigz runc ubuntu-fan
Suggested packages:
  ifupdown aufs-tools cgroupfs-mount | cgroup-lite debootstrap docker-doc rinse zfs-fuse | zfsutils
The following NEW packages will be installed:
  bridge-utils containerd dns-root-data dnsmasq-base docker.io pigz runc ubuntu-fan
0 upgraded, 8 newly installed, 0 to remove and 26 not upgraded.
Need to get 69.7 MB of archives.
After this operation, 267 MB of additional disk space will be used.
Get:1 http://ap-southeast-2.ec2.archive.ubuntu.com/ubuntu jammy/universe amd64 pigz amd64 2.6-1 [63.6 kB]
Get:2 http://ap-southeast-2.ec2.archive.ubuntu.com/ubuntu jammy/main amd64 bridge-utils amd64 1.7-1ubuntu3 [34.4 kB]
Get:3 http://ap-southeast-2.ec2.archive.ubuntu.com/ubuntu jammy-updates/main amd64 runc amd64 1.1.7-0ubuntu1~22.04.1 [4249 kB]
Get:4 http://ap-southeast-2.ec2.archive.ubuntu.com/ubuntu jammy-updates/main amd64 containerd amd64 1.7.2-0ubuntu1~22.04.1 [36.0 MB]
Get:5 http://ap-southeast-2.ec2.archive.ubuntu.com/ubuntu jammy/main amd64 dns-root-data all 2021011101 [5256 B]
Get:6 http://ap-southeast-2.ec2.archive.ubuntu.com/ubuntu jammy-updates/main amd64 dnsmasq-base amd64 2.86-1.1ubuntu0.3 [354 kB]
Get:7 http://ap-southeast-2.ec2.archive.ubuntu.com/ubuntu jammy-updates/universe amd64 docker.io amd64 24.0.5-0ubuntu1~22.04.1 [28.9 MB]
Get:8 http://ap-southeast-2.ec2.archive.ubuntu.com/ubuntu jammy/universe amd64 ubuntu-fan all 0.12.16 [35.2 kB]
Fetched 69.7 MB in 2s (40.9 MB/s)     
Preconfiguring packages ...
Selecting previously unselected package pigz.
(Reading database ... 64803 files and directories currently installed.)
Preparing to unpack .../0-pigz_2.6-1_amd64.deb ...
Unpacking pigz (2.6-1) ...
Selecting previously unselected package bridge-utils.
Preparing to unpack .../1-bridge-utils_1.7-1ubuntu3_amd64.deb ...
Unpacking bridge-utils (1.7-1ubuntu3) ...
Selecting previously unselected package runc.
Preparing to unpack .../2-runc_1.1.7-0ubuntu1~22.04.1_amd64.deb ...
Unpacking runc (1.1.7-0ubuntu1~22.04.1) ...
Selecting previously unselected package containerd.
Preparing to unpack .../3-containerd_1.7.2-0ubuntu1~22.04.1_amd64.deb ...
Unpacking containerd (1.7.2-0ubuntu1~22.04.1) ...
Selecting previously unselected package dns-root-data.
Preparing to unpack .../4-dns-root-data_2021011101_all.deb ...
Unpacking dns-root-data (2021011101) ...
Selecting previously unselected package dnsmasq-base.
Preparing to unpack .../5-dnsmasq-base_2.86-1.1ubuntu0.3_amd64.deb ...
Unpacking dnsmasq-base (2.86-1.1ubuntu0.3) ...
Selecting previously unselected package docker.io.
Preparing to unpack .../6-docker.io_24.0.5-0ubuntu1~22.04.1_amd64.deb ...
Unpacking docker.io (24.0.5-0ubuntu1~22.04.1) ...
Selecting previously unselected package ubuntu-fan.
Preparing to unpack .../7-ubuntu-fan_0.12.16_all.deb ...
Unpacking ubuntu-fan (0.12.16) ...
Setting up dnsmasq-base (2.86-1.1ubuntu0.3) ...
Setting up runc (1.1.7-0ubuntu1~22.04.1) ...
Setting up dns-root-data (2021011101) ...
Setting up bridge-utils (1.7-1ubuntu3) ...
Setting up pigz (2.6-1) ...
Setting up containerd (1.7.2-0ubuntu1~22.04.1) ...
Created symlink /etc/systemd/system/multi-user.target.wants/containerd.service → /lib/systemd/system/containerd.service.
Setting up ubuntu-fan (0.12.16) ...
Created symlink /etc/systemd/system/multi-user.target.wants/ubuntu-fan.service → /lib/systemd/system/ubuntu-fan.service.
Setting up docker.io (24.0.5-0ubuntu1~22.04.1) ...
Adding group `docker' (GID 122) ...
Done.
Created symlink /etc/systemd/system/multi-user.target.wants/docker.service → /lib/systemd/system/docker.service.
Created symlink /etc/systemd/system/sockets.target.wants/docker.socket → /lib/systemd/system/docker.socket.
Processing triggers for dbus (1.12.20-2ubuntu4.1) ...
Processing triggers for man-db (2.10.2-1) ...
Scanning processes...                                                                                                                         
Scanning linux images...                                                                                                                      

Running kernel seems to be up-to-date.

No services need to be restarted.

No containers need to be restarted.

No user sessions are running outdated binaries.

No VM guests are running outdated hypervisor (qemu) binaries on this host.
deb https://packages.cloud.google.com/apt kubernetes-xenial main
Hit:1 http://ap-southeast-2.ec2.archive.ubuntu.com/ubuntu jammy InRelease
Hit:2 http://ap-southeast-2.ec2.archive.ubuntu.com/ubuntu jammy-updates InRelease
Hit:3 http://ap-southeast-2.ec2.archive.ubuntu.com/ubuntu jammy-backports InRelease
Hit:4 http://security.ubuntu.com/ubuntu jammy-security InRelease                                               
Get:5 https://packages.cloud.google.com/apt kubernetes-xenial InRelease [8993 B]
Get:6 https://packages.cloud.google.com/apt kubernetes-xenial/main amd64 Packages [69.9 kB]
Fetched 78.9 kB in 1s (86.0 kB/s)   
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
26 packages can be upgraded. Run 'apt list --upgradable' to see them.
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
The following additional packages will be installed:
  conntrack cri-tools ebtables kubernetes-cni socat
The following NEW packages will be installed:
  conntrack cri-tools ebtables kubeadm kubectl kubelet kubernetes-cni socat
0 upgraded, 8 newly installed, 0 to remove and 26 not upgraded.
Need to get 81.5 MB of archives.
After this operation, 318 MB of additional disk space will be used.
Get:1 http://ap-southeast-2.ec2.archive.ubuntu.com/ubuntu jammy/main amd64 conntrack amd64 1:1.4.6-2build2 [33.5 kB]
Get:2 http://ap-southeast-2.ec2.archive.ubuntu.com/ubuntu jammy/main amd64 ebtables amd64 2.0.11-4build2 [84.9 kB]
Get:3 http://ap-southeast-2.ec2.archive.ubuntu.com/ubuntu jammy/main amd64 socat amd64 1.7.4.1-3ubuntu4 [349 kB]
Get:4 https://packages.cloud.google.com/apt kubernetes-xenial/main amd64 cri-tools amd64 1.26.0-00 [18.9 MB]
Get:5 https://packages.cloud.google.com/apt kubernetes-xenial/main amd64 kubernetes-cni amd64 1.2.0-00 [27.6 MB]
Get:6 https://packages.cloud.google.com/apt kubernetes-xenial/main amd64 kubelet amd64 1.20.0-00 [18.8 MB]                                   
Get:7 https://packages.cloud.google.com/apt kubernetes-xenial/main amd64 kubectl amd64 1.20.0-00 [7942 kB]                                   
Get:8 https://packages.cloud.google.com/apt kubernetes-xenial/main amd64 kubeadm amd64 1.20.0-00 [7707 kB]                                   
Fetched 81.5 MB in 24s (3373 kB/s)                                                                                                           
Selecting previously unselected package conntrack.
(Reading database ... 65167 files and directories currently installed.)
Preparing to unpack .../0-conntrack_1%3a1.4.6-2build2_amd64.deb ...
Unpacking conntrack (1:1.4.6-2build2) ...
Selecting previously unselected package cri-tools.
Preparing to unpack .../1-cri-tools_1.26.0-00_amd64.deb ...
Unpacking cri-tools (1.26.0-00) ...
Selecting previously unselected package ebtables.
Preparing to unpack .../2-ebtables_2.0.11-4build2_amd64.deb ...
Unpacking ebtables (2.0.11-4build2) ...
Selecting previously unselected package kubernetes-cni.
Preparing to unpack .../3-kubernetes-cni_1.2.0-00_amd64.deb ...
Unpacking kubernetes-cni (1.2.0-00) ...
Selecting previously unselected package socat.
Preparing to unpack .../4-socat_1.7.4.1-3ubuntu4_amd64.deb ...
Unpacking socat (1.7.4.1-3ubuntu4) ...
Selecting previously unselected package kubelet.
Preparing to unpack .../5-kubelet_1.20.0-00_amd64.deb ...
Unpacking kubelet (1.20.0-00) ...
Selecting previously unselected package kubectl.
Preparing to unpack .../6-kubectl_1.20.0-00_amd64.deb ...
Unpacking kubectl (1.20.0-00) ...
Selecting previously unselected package kubeadm.
Preparing to unpack .../7-kubeadm_1.20.0-00_amd64.deb ...
Unpacking kubeadm (1.20.0-00) ...
Setting up conntrack (1:1.4.6-2build2) ...
Setting up kubectl (1.20.0-00) ...
Setting up ebtables (2.0.11-4build2) ...
Setting up socat (1.7.4.1-3ubuntu4) ...
Setting up cri-tools (1.26.0-00) ...
Setting up kubernetes-cni (1.2.0-00) ...
Setting up kubelet (1.20.0-00) ...
Created symlink /etc/systemd/system/multi-user.target.wants/kubelet.service → /lib/systemd/system/kubelet.service.
Setting up kubeadm (1.20.0-00) ...
Processing triggers for man-db (2.10.2-1) ...
Scanning processes...                                                                                                                         
Scanning linux images...                                                                                                                      

Running kernel seems to be up-to-date.

No services need to be restarted.

No containers need to be restarted.

No user sessions are running outdated binaries.

No VM guests are running outdated hypervisor (qemu) binaries on this host.
ubuntu@ip-172-31-4-107:~$ sudo kubeadm init
I0101 01:21:07.438128    3483 version.go:251] remote version is much newer: v1.29.0; falling back to: stable-1.20
[init] Using Kubernetes version: v1.20.15
[preflight] Running pre-flight checks
	[WARNING SystemVerification]: this Docker version is not on the list of validated versions: 24.0.5. Latest validated version: 19.03
[preflight] Pulling images required for setting up a Kubernetes cluster
[preflight] This might take a minute or two, depending on the speed of your internet connection
[preflight] You can also perform this action in beforehand using 'kubeadm config images pull'
[certs] Using certificateDir folder "/etc/kubernetes/pki"
[certs] Generating "ca" certificate and key
[certs] Generating "apiserver" certificate and key
[certs] apiserver serving cert is signed for DNS names [ip-172-31-4-107 kubernetes kubernetes.default kubernetes.default.svc kubernetes.default.svc.cluster.local] and IPs [10.96.0.1 172.31.4.107]
[certs] Generating "apiserver-kubelet-client" certificate and key
[certs] Generating "front-proxy-ca" certificate and key
[certs] Generating "front-proxy-client" certificate and key
[certs] Generating "etcd/ca" certificate and key
[certs] Generating "etcd/server" certificate and key
[certs] etcd/server serving cert is signed for DNS names [ip-172-31-4-107 localhost] and IPs [172.31.4.107 127.0.0.1 ::1]
[certs] Generating "etcd/peer" certificate and key
[certs] etcd/peer serving cert is signed for DNS names [ip-172-31-4-107 localhost] and IPs [172.31.4.107 127.0.0.1 ::1]
[certs] Generating "etcd/healthcheck-client" certificate and key
[certs] Generating "apiserver-etcd-client" certificate and key
[certs] Generating "sa" key and public key
[kubeconfig] Using kubeconfig folder "/etc/kubernetes"
[kubeconfig] Writing "admin.conf" kubeconfig file
[kubeconfig] Writing "kubelet.conf" kubeconfig file
[kubeconfig] Writing "controller-manager.conf" kubeconfig file
[kubeconfig] Writing "scheduler.conf" kubeconfig file
[kubelet-start] Writing kubelet environment file with flags to file "/var/lib/kubelet/kubeadm-flags.env"
[kubelet-start] Writing kubelet configuration to file "/var/lib/kubelet/config.yaml"
[kubelet-start] Starting the kubelet
[control-plane] Using manifest folder "/etc/kubernetes/manifests"
[control-plane] Creating static Pod manifest for "kube-apiserver"
[control-plane] Creating static Pod manifest for "kube-controller-manager"
[control-plane] Creating static Pod manifest for "kube-scheduler"
[etcd] Creating static Pod manifest for local etcd in "/etc/kubernetes/manifests"
[wait-control-plane] Waiting for the kubelet to boot up the control plane as static Pods from directory "/etc/kubernetes/manifests". This can take up to 4m0s
[apiclient] All control plane components are healthy after 11.002731 seconds
[upload-config] Storing the configuration used in ConfigMap "kubeadm-config" in the "kube-system" Namespace
[kubelet] Creating a ConfigMap "kubelet-config-1.20" in namespace kube-system with the configuration for the kubelets in the cluster
[upload-certs] Skipping phase. Please see --upload-certs
[mark-control-plane] Marking the node ip-172-31-4-107 as control-plane by adding the labels "node-role.kubernetes.io/master=''" and "node-role.kubernetes.io/control-plane='' (deprecated)"
[mark-control-plane] Marking the node ip-172-31-4-107 as control-plane by adding the taints [node-role.kubernetes.io/master:NoSchedule]
[bootstrap-token] Using token: c8hncp.5yhtjuu192jxj0ce
[bootstrap-token] Configuring bootstrap tokens, cluster-info ConfigMap, RBAC Roles
[bootstrap-token] configured RBAC rules to allow Node Bootstrap tokens to get nodes
[bootstrap-token] configured RBAC rules to allow Node Bootstrap tokens to post CSRs in order for nodes to get long term certificate credentials
[bootstrap-token] configured RBAC rules to allow the csrapprover controller automatically approve CSRs from a Node Bootstrap Token
[bootstrap-token] configured RBAC rules to allow certificate rotation for all node client certificates in the cluster
[bootstrap-token] Creating the "cluster-info" ConfigMap in the "kube-public" namespace
[kubelet-finalize] Updating "/etc/kubernetes/kubelet.conf" to point to a rotatable kubelet client certificate and key
[addons] Applied essential addon: CoreDNS
[addons] Applied essential addon: kube-proxy

Your Kubernetes control-plane has initialized successfully!

To start using your cluster, you need to run the following as a regular user:

  mkdir -p $HOME/.kube
  sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
  sudo chown $(id -u):$(id -g) $HOME/.kube/config

Alternatively, if you are the root user, you can run:

  export KUBECONFIG=/etc/kubernetes/admin.conf

You should now deploy a pod network to the cluster.
Run "kubectl apply -f [podnetwork].yaml" with one of the options listed at:
  https://kubernetes.io/docs/concepts/cluster-administration/addons/

Then you can join any number of worker nodes by running the following on each as root:

kubeadm join 172.31.4.107:6443 --token c8hncp.5yhtjuu192jxj0ce \
    --discovery-token-ca-cert-hash sha256:9f6c6316ae29f51a8d0a12d01aa429546770ca7f2e64ebc92eee972137a16382 
ubuntu@ip-172-31-4-107:~$ mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
ubuntu@ip-172-31-4-107:~$ kubectl get nodes
NAME              STATUS     ROLES                  AGE   VERSION
ip-172-31-4-107   NotReady   control-plane,master   74s   v1.20.0
ubuntu@ip-172-31-4-107:~$ kubectl apply -f https://github.com/weaveworks/weave/releases/download/v2.8.1/weave-daemonset-k8s.yaml
serviceaccount/weave-net created
clusterrole.rbac.authorization.k8s.io/weave-net created
clusterrolebinding.rbac.authorization.k8s.io/weave-net created
role.rbac.authorization.k8s.io/weave-net created
rolebinding.rbac.authorization.k8s.io/weave-net created
daemonset.apps/weave-net created
ubuntu@ip-172-31-4-107:~$ sudo kubeadm token create --print-join-command
kubeadm join 172.31.4.107:6443 --token nepvad.go4fm1iwp2gogkad     --discovery-token-ca-cert-hash sha256:9f6c6316ae29f51a8d0a12d01aa429546770ca7f2e64ebc92eee972137a16382 
ubuntu@ip-172-31-4-107:~$ kubectl get nodes
NAME               STATUS   ROLES                  AGE     VERSION
ip-172-31-11-137   Ready    <none>                 32s     v1.20.0
ip-172-31-4-107    Ready    control-plane,master   7m35s   v1.20.0
ubuntu@ip-172-31-4-107:~$ sudo docker ps
CONTAINER ID   IMAGE                   COMMAND                  CREATED         STATUS         PORTS     NAMES
42835bef7bbd   bfe3a36ebd25            "/coredns -conf /etc…"   6 minutes ago   Up 6 minutes             k8s_coredns_coredns-74ff55c5b-52jvp_kube-system_1f9e91d1-84e2-44f3-8dec-291d833f3874_0
fee7ce30d1ba   bfe3a36ebd25            "/coredns -conf /etc…"   6 minutes ago   Up 6 minutes             k8s_coredns_coredns-74ff55c5b-lfjfk_kube-system_725c64c7-42ba-43e8-8172-afdcdd9b4308_0
a3e62d4056a3   k8s.gcr.io/pause:3.2    "/pause"                 6 minutes ago   Up 6 minutes             k8s_POD_coredns-74ff55c5b-52jvp_kube-system_1f9e91d1-84e2-44f3-8dec-291d833f3874_0
4dfa15cff45b   k8s.gcr.io/pause:3.2    "/pause"                 6 minutes ago   Up 6 minutes             k8s_POD_coredns-74ff55c5b-lfjfk_kube-system_725c64c7-42ba-43e8-8172-afdcdd9b4308_0
3054d8513128   weaveworks/weave-kube   "/home/weave/launch.…"   6 minutes ago   Up 6 minutes             k8s_weave_weave-net-qzcdx_kube-system_5049351c-4660-49aa-9858-bd96121259dd_1
99f1385c5678   weaveworks/weave-npc    "/usr/bin/launch.sh"     6 minutes ago   Up 6 minutes             k8s_weave-npc_weave-net-qzcdx_kube-system_5049351c-4660-49aa-9858-bd96121259dd_0
2d51208a453d   k8s.gcr.io/pause:3.2    "/pause"                 6 minutes ago   Up 6 minutes             k8s_POD_weave-net-qzcdx_kube-system_5049351c-4660-49aa-9858-bd96121259dd_0
d1b94911c92a   46e2cd1b2594            "/usr/local/bin/kube…"   8 minutes ago   Up 8 minutes             k8s_kube-proxy_kube-proxy-kcwrp_kube-system_ed6dc7d8-e302-49e2-982f-6afcc0c12a8d_0
4e9067d431f7   k8s.gcr.io/pause:3.2    "/pause"                 8 minutes ago   Up 8 minutes             k8s_POD_kube-proxy-kcwrp_kube-system_ed6dc7d8-e302-49e2-982f-6afcc0c12a8d_0
5dbd12a2ddf0   323f6347f5e2            "kube-apiserver --ad…"   8 minutes ago   Up 8 minutes             k8s_kube-apiserver_kube-apiserver-ip-172-31-4-107_kube-system_6de0573a48ba81b48f81cc8490e28640_0
cac097d38f84   9155e4deabb3            "kube-scheduler --au…"   8 minutes ago   Up 8 minutes             k8s_kube-scheduler_kube-scheduler-ip-172-31-4-107_kube-system_e8f872f9a07112e96e684366d7248982_0
1136c6b659dd   d6296d0e06d2            "kube-controller-man…"   8 minutes ago   Up 8 minutes             k8s_kube-controller-manager_kube-controller-manager-ip-172-31-4-107_kube-system_61f973905d90a5dc3adda42429669ba1_0
04298b87ff1c   0369cf4303ff            "etcd --advertise-cl…"   8 minutes ago   Up 8 minutes             k8s_etcd_etcd-ip-172-31-4-107_kube-system_9be0937a78662024ca45bb831feddb10_0
58a0f693094e   k8s.gcr.io/pause:3.2    "/pause"                 8 minutes ago   Up 8 minutes             k8s_POD_kube-apiserver-ip-172-31-4-107_kube-system_6de0573a48ba81b48f81cc8490e28640_0
319e1a1514f9   k8s.gcr.io/pause:3.2    "/pause"                 8 minutes ago   Up 8 minutes             k8s_POD_etcd-ip-172-31-4-107_kube-system_9be0937a78662024ca45bb831feddb10_0
91dff2beefee   k8s.gcr.io/pause:3.2    "/pause"                 8 minutes ago   Up 8 minutes             k8s_POD_kube-scheduler-ip-172-31-4-107_kube-system_e8f872f9a07112e96e684366d7248982_0
b8c20ac8110e   k8s.gcr.io/pause:3.2    "/pause"                 8 minutes ago   Up 8 minutes             k8s_POD_kube-controller-manager-ip-172-31-4-107_kube-system_61f973905d90a5dc3adda42429669ba1_0
ubuntu@ip-172-31-4-107:~$ Connection to ec2-3-27-110-9.ap-southeast-2.compute.amazonaws.com closed by remote host.
Connection to ec2-3-27-110-9.ap-southeast-2.compute.amazonaws.com closed.
khairi@khairi-Lenovo-YOGA-520-14IKB:~/Downloads$ 


# My Terminal (Worker)
khairi@khairi-Lenovo-YOGA-520-14IKB:~/Downloads$ sudo ssh -i "two-tier-app-k8s.pem" ubuntu@ec2-13-239-34-239.ap-southeast-2.compute.amazonaws.com
[sudo] password for khairi: 
The authenticity of host 'ec2-13-239-34-239.ap-southeast-2.compute.amazonaws.com (13.239.34.239)' can't be established.
ED25519 key fingerprint is SHA256:31YZPfSL+buqv3ODQqLpvnsJW4INis/Xv6rpBBtsW3k.
This key is not known by any other names
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added 'ec2-13-239-34-239.ap-southeast-2.compute.amazonaws.com' (ED25519) to the list of known hosts.
Welcome to Ubuntu 22.04.3 LTS (GNU/Linux 6.2.0-1017-aws x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  System information as of Mon Jan  1 01:15:47 UTC 2024

  System load:  0.02880859375     Processes:             114
  Usage of /:   20.6% of 7.57GB   Users logged in:       0
  Memory usage: 5%                IPv4 address for eth0: 172.31.11.137
  Swap usage:   0%

Expanded Security Maintenance for Applications is not enabled.

0 updates can be applied immediately.

Enable ESM Apps to receive additional future security updates.
See https://ubuntu.com/esm or run: sudo pro status


The list of available updates is more than a week old.
To check for new updates run: sudo apt update


The programs included with the Ubuntu system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Ubuntu comes with ABSOLUTELY NO WARRANTY, to the extent permitted by
applicable law.

To run a command as administrator (user "root"), use "sudo <command>".
See "man sudo_root" for details.

ubuntu@ip-172-31-11-137:~$ # using 'sudo su' is not a good practice.
sudo apt update
sudo apt-get install -y apt-transport-https ca-certificates curl
sudo apt install docker.io -y

sudo systemctl enable --now docker # enable and start in single command.

# Adding GPG keys.
curl -fsSL "https://packages.cloud.google.com/apt/doc/apt-key.gpg" | sudo gpg --dearmor -o /etc/apt/trusted.gpg.d/kubernetes-archive-keyring.gpg

# Add the repository to the sourcelist.
echo 'deb https://packages.cloud.google.com/apt kubernetes-xenial main' | sudo tee /etc/apt/sources.list.d/kubernetes.list

sudo apt update 
sudo apt install kubeadm=1.20.0-00 kubectl=1.20.0-00 kubelet=1.20.0-00 -y
Hit:1 http://ap-southeast-2.ec2.archive.ubuntu.com/ubuntu jammy InRelease
Get:2 http://ap-southeast-2.ec2.archive.ubuntu.com/ubuntu jammy-updates InRelease [119 kB]
Get:3 http://ap-southeast-2.ec2.archive.ubuntu.com/ubuntu jammy-backports InRelease [109 kB]
Get:4 http://ap-southeast-2.ec2.archive.ubuntu.com/ubuntu jammy/universe amd64 Packages [14.1 MB]
Get:5 http://security.ubuntu.com/ubuntu jammy-security InRelease [110 kB]            
Get:6 http://ap-southeast-2.ec2.archive.ubuntu.com/ubuntu jammy/universe Translation-en [5652 kB]
Get:7 http://ap-southeast-2.ec2.archive.ubuntu.com/ubuntu jammy/universe amd64 c-n-f Metadata [286 kB]
Get:8 http://ap-southeast-2.ec2.archive.ubuntu.com/ubuntu jammy/multiverse amd64 Packages [217 kB]
Get:9 http://ap-southeast-2.ec2.archive.ubuntu.com/ubuntu jammy/multiverse Translation-en [112 kB]
Get:10 http://ap-southeast-2.ec2.archive.ubuntu.com/ubuntu jammy/multiverse amd64 c-n-f Metadata [8372 B]
Get:11 http://ap-southeast-2.ec2.archive.ubuntu.com/ubuntu jammy-updates/main amd64 Packages [1263 kB]
Get:12 http://ap-southeast-2.ec2.archive.ubuntu.com/ubuntu jammy-updates/main Translation-en [260 kB]
Get:13 http://ap-southeast-2.ec2.archive.ubuntu.com/ubuntu jammy-updates/restricted amd64 Packages [1250 kB]
Get:14 http://ap-southeast-2.ec2.archive.ubuntu.com/ubuntu jammy-updates/restricted Translation-en [203 kB]
Get:15 http://ap-southeast-2.ec2.archive.ubuntu.com/ubuntu jammy-updates/universe amd64 Packages [1020 kB]
Get:16 http://ap-southeast-2.ec2.archive.ubuntu.com/ubuntu jammy-updates/universe Translation-en [226 kB]
Get:17 http://ap-southeast-2.ec2.archive.ubuntu.com/ubuntu jammy-updates/universe amd64 c-n-f Metadata [22.1 kB]
Get:18 http://ap-southeast-2.ec2.archive.ubuntu.com/ubuntu jammy-updates/multiverse amd64 Packages [41.6 kB]
Get:19 http://ap-southeast-2.ec2.archive.ubuntu.com/ubuntu jammy-updates/multiverse Translation-en [9768 B]
Get:20 http://ap-southeast-2.ec2.archive.ubuntu.com/ubuntu jammy-updates/multiverse amd64 c-n-f Metadata [472 B]
Get:21 http://ap-southeast-2.ec2.archive.ubuntu.com/ubuntu jammy-backports/main amd64 Packages [41.7 kB]
Get:22 http://ap-southeast-2.ec2.archive.ubuntu.com/ubuntu jammy-backports/main Translation-en [10.5 kB]
Get:23 http://ap-southeast-2.ec2.archive.ubuntu.com/ubuntu jammy-backports/main amd64 c-n-f Metadata [388 B]
Get:24 http://ap-southeast-2.ec2.archive.ubuntu.com/ubuntu jammy-backports/restricted amd64 c-n-f Metadata [116 B]
Get:25 http://ap-southeast-2.ec2.archive.ubuntu.com/ubuntu jammy-backports/universe amd64 Packages [24.3 kB]
Get:26 http://ap-southeast-2.ec2.archive.ubuntu.com/ubuntu jammy-backports/universe Translation-en [16.5 kB]
Get:27 http://ap-southeast-2.ec2.archive.ubuntu.com/ubuntu jammy-backports/universe amd64 c-n-f Metadata [644 B]
Get:28 http://ap-southeast-2.ec2.archive.ubuntu.com/ubuntu jammy-backports/multiverse amd64 c-n-f Metadata [116 B]
Get:29 http://security.ubuntu.com/ubuntu jammy-security/main amd64 Packages [1051 kB]      
Get:30 http://security.ubuntu.com/ubuntu jammy-security/main Translation-en [200 kB]
Get:31 http://security.ubuntu.com/ubuntu jammy-security/restricted amd64 Packages [1226 kB]
Get:32 http://security.ubuntu.com/ubuntu jammy-security/restricted Translation-en [200 kB]
Get:33 http://security.ubuntu.com/ubuntu jammy-security/universe amd64 Packages [823 kB]
Get:34 http://security.ubuntu.com/ubuntu jammy-security/universe Translation-en [156 kB]
Get:35 http://security.ubuntu.com/ubuntu jammy-security/universe amd64 c-n-f Metadata [16.8 kB]
Get:36 http://security.ubuntu.com/ubuntu jammy-security/multiverse amd64 Packages [36.5 kB]
Get:37 http://security.ubuntu.com/ubuntu jammy-security/multiverse Translation-en [7060 B]
Get:38 http://security.ubuntu.com/ubuntu jammy-security/multiverse amd64 c-n-f Metadata [260 B]
Fetched 28.8 MB in 5s (6380 kB/s)                                
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
26 packages can be upgraded. Run 'apt list --upgradable' to see them.
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
ca-certificates is already the newest version (20230311ubuntu0.22.04.1).
ca-certificates set to manually installed.
curl is already the newest version (7.81.0-1ubuntu1.15).
curl set to manually installed.
The following NEW packages will be installed:
  apt-transport-https
0 upgraded, 1 newly installed, 0 to remove and 26 not upgraded.
Need to get 1510 B of archives.
After this operation, 170 kB of additional disk space will be used.
Get:1 http://ap-southeast-2.ec2.archive.ubuntu.com/ubuntu jammy-updates/universe amd64 apt-transport-https all 2.4.11 [1510 B]
Fetched 1510 B in 0s (98.5 kB/s)              
Selecting previously unselected package apt-transport-https.
(Reading database ... 64799 files and directories currently installed.)
Preparing to unpack .../apt-transport-https_2.4.11_all.deb ...
Unpacking apt-transport-https (2.4.11) ...
Setting up apt-transport-https (2.4.11) ...
Scanning processes...                                                                                                                         
Scanning linux images...                                                                                                                      

Running kernel seems to be up-to-date.

No services need to be restarted.

No containers need to be restarted.

No user sessions are running outdated binaries.

No VM guests are running outdated hypervisor (qemu) binaries on this host.
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
The following additional packages will be installed:
  bridge-utils containerd dns-root-data dnsmasq-base pigz runc ubuntu-fan
Suggested packages:
  ifupdown aufs-tools cgroupfs-mount | cgroup-lite debootstrap docker-doc rinse zfs-fuse | zfsutils
The following NEW packages will be installed:
  bridge-utils containerd dns-root-data dnsmasq-base docker.io pigz runc ubuntu-fan
0 upgraded, 8 newly installed, 0 to remove and 26 not upgraded.
Need to get 69.7 MB of archives.
After this operation, 267 MB of additional disk space will be used.
Get:1 http://ap-southeast-2.ec2.archive.ubuntu.com/ubuntu jammy/universe amd64 pigz amd64 2.6-1 [63.6 kB]
Get:2 http://ap-southeast-2.ec2.archive.ubuntu.com/ubuntu jammy/main amd64 bridge-utils amd64 1.7-1ubuntu3 [34.4 kB]
Get:3 http://ap-southeast-2.ec2.archive.ubuntu.com/ubuntu jammy-updates/main amd64 runc amd64 1.1.7-0ubuntu1~22.04.1 [4249 kB]
Get:4 http://ap-southeast-2.ec2.archive.ubuntu.com/ubuntu jammy-updates/main amd64 containerd amd64 1.7.2-0ubuntu1~22.04.1 [36.0 MB]
Get:5 http://ap-southeast-2.ec2.archive.ubuntu.com/ubuntu jammy/main amd64 dns-root-data all 2021011101 [5256 B]
Get:6 http://ap-southeast-2.ec2.archive.ubuntu.com/ubuntu jammy-updates/main amd64 dnsmasq-base amd64 2.86-1.1ubuntu0.3 [354 kB]
Get:7 http://ap-southeast-2.ec2.archive.ubuntu.com/ubuntu jammy-updates/universe amd64 docker.io amd64 24.0.5-0ubuntu1~22.04.1 [28.9 MB]
Get:8 http://ap-southeast-2.ec2.archive.ubuntu.com/ubuntu jammy/universe amd64 ubuntu-fan all 0.12.16 [35.2 kB]
Fetched 69.7 MB in 2s (39.5 MB/s)     
Preconfiguring packages ...
Selecting previously unselected package pigz.
(Reading database ... 64803 files and directories currently installed.)
Preparing to unpack .../0-pigz_2.6-1_amd64.deb ...
Unpacking pigz (2.6-1) ...
Selecting previously unselected package bridge-utils.
Preparing to unpack .../1-bridge-utils_1.7-1ubuntu3_amd64.deb ...
Unpacking bridge-utils (1.7-1ubuntu3) ...
Selecting previously unselected package runc.
Preparing to unpack .../2-runc_1.1.7-0ubuntu1~22.04.1_amd64.deb ...
Unpacking runc (1.1.7-0ubuntu1~22.04.1) ...
Selecting previously unselected package containerd.
Preparing to unpack .../3-containerd_1.7.2-0ubuntu1~22.04.1_amd64.deb ...
Unpacking containerd (1.7.2-0ubuntu1~22.04.1) ...
Selecting previously unselected package dns-root-data.
Preparing to unpack .../4-dns-root-data_2021011101_all.deb ...
Unpacking dns-root-data (2021011101) ...
Selecting previously unselected package dnsmasq-base.
Preparing to unpack .../5-dnsmasq-base_2.86-1.1ubuntu0.3_amd64.deb ...
Unpacking dnsmasq-base (2.86-1.1ubuntu0.3) ...
Selecting previously unselected package docker.io.
Preparing to unpack .../6-docker.io_24.0.5-0ubuntu1~22.04.1_amd64.deb ...
Unpacking docker.io (24.0.5-0ubuntu1~22.04.1) ...
Selecting previously unselected package ubuntu-fan.
Preparing to unpack .../7-ubuntu-fan_0.12.16_all.deb ...
Unpacking ubuntu-fan (0.12.16) ...
Setting up dnsmasq-base (2.86-1.1ubuntu0.3) ...
Setting up runc (1.1.7-0ubuntu1~22.04.1) ...
Setting up dns-root-data (2021011101) ...
Setting up bridge-utils (1.7-1ubuntu3) ...
Setting up pigz (2.6-1) ...
Setting up containerd (1.7.2-0ubuntu1~22.04.1) ...
Created symlink /etc/systemd/system/multi-user.target.wants/containerd.service → /lib/systemd/system/containerd.service.
Setting up ubuntu-fan (0.12.16) ...
Created symlink /etc/systemd/system/multi-user.target.wants/ubuntu-fan.service → /lib/systemd/system/ubuntu-fan.service.
Setting up docker.io (24.0.5-0ubuntu1~22.04.1) ...
Adding group `docker' (GID 122) ...
Done.
Created symlink /etc/systemd/system/multi-user.target.wants/docker.service → /lib/systemd/system/docker.service.
Created symlink /etc/systemd/system/sockets.target.wants/docker.socket → /lib/systemd/system/docker.socket.
Processing triggers for dbus (1.12.20-2ubuntu4.1) ...
Processing triggers for man-db (2.10.2-1) ...
Scanning processes...                                                                                                                         
Scanning linux images...                                                                                                                      

Running kernel seems to be up-to-date.

No services need to be restarted.

No containers need to be restarted.

No user sessions are running outdated binaries.

No VM guests are running outdated hypervisor (qemu) binaries on this host.
deb https://packages.cloud.google.com/apt kubernetes-xenial main
Hit:1 http://ap-southeast-2.ec2.archive.ubuntu.com/ubuntu jammy InRelease
Hit:2 http://ap-southeast-2.ec2.archive.ubuntu.com/ubuntu jammy-updates InRelease                             
Hit:3 http://ap-southeast-2.ec2.archive.ubuntu.com/ubuntu jammy-backports InRelease                           
Hit:4 http://security.ubuntu.com/ubuntu jammy-security InRelease                                              
Get:5 https://packages.cloud.google.com/apt kubernetes-xenial InRelease [8993 B]
Get:6 https://packages.cloud.google.com/apt kubernetes-xenial/main amd64 Packages [69.9 kB]
Fetched 78.9 kB in 1s (84.1 kB/s)   
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
26 packages can be upgraded. Run 'apt list --upgradable' to see them.
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
The following additional packages will be installed:
  conntrack cri-tools ebtables kubernetes-cni socat
The following NEW packages will be installed:
  conntrack cri-tools ebtables kubeadm kubectl kubelet kubernetes-cni socat
0 upgraded, 8 newly installed, 0 to remove and 26 not upgraded.
Need to get 81.5 MB of archives.
After this operation, 318 MB of additional disk space will be used.
Get:1 http://ap-southeast-2.ec2.archive.ubuntu.com/ubuntu jammy/main amd64 conntrack amd64 1:1.4.6-2build2 [33.5 kB]
Get:2 http://ap-southeast-2.ec2.archive.ubuntu.com/ubuntu jammy/main amd64 ebtables amd64 2.0.11-4build2 [84.9 kB]
Get:3 http://ap-southeast-2.ec2.archive.ubuntu.com/ubuntu jammy/main amd64 socat amd64 1.7.4.1-3ubuntu4 [349 kB]
Get:4 https://packages.cloud.google.com/apt kubernetes-xenial/main amd64 cri-tools amd64 1.26.0-00 [18.9 MB]
Get:5 https://packages.cloud.google.com/apt kubernetes-xenial/main amd64 kubernetes-cni amd64 1.2.0-00 [27.6 MB]
Get:6 https://packages.cloud.google.com/apt kubernetes-xenial/main amd64 kubelet amd64 1.20.0-00 [18.8 MB]
Get:7 https://packages.cloud.google.com/apt kubernetes-xenial/main amd64 kubectl amd64 1.20.0-00 [7942 kB]                                   
Get:8 https://packages.cloud.google.com/apt kubernetes-xenial/main amd64 kubeadm amd64 1.20.0-00 [7707 kB]                                   
Fetched 81.5 MB in 14s (5781 kB/s)                                                                                                           
Selecting previously unselected package conntrack.
(Reading database ... 65167 files and directories currently installed.)
Preparing to unpack .../0-conntrack_1%3a1.4.6-2build2_amd64.deb ...
Unpacking conntrack (1:1.4.6-2build2) ...
Selecting previously unselected package cri-tools.
Preparing to unpack .../1-cri-tools_1.26.0-00_amd64.deb ...
Unpacking cri-tools (1.26.0-00) ...
Selecting previously unselected package ebtables.
Preparing to unpack .../2-ebtables_2.0.11-4build2_amd64.deb ...
Unpacking ebtables (2.0.11-4build2) ...
Selecting previously unselected package kubernetes-cni.
Preparing to unpack .../3-kubernetes-cni_1.2.0-00_amd64.deb ...
Unpacking kubernetes-cni (1.2.0-00) ...
Selecting previously unselected package socat.
Preparing to unpack .../4-socat_1.7.4.1-3ubuntu4_amd64.deb ...
Unpacking socat (1.7.4.1-3ubuntu4) ...
Selecting previously unselected package kubelet.
Preparing to unpack .../5-kubelet_1.20.0-00_amd64.deb ...
Unpacking kubelet (1.20.0-00) ...
Selecting previously unselected package kubectl.
Preparing to unpack .../6-kubectl_1.20.0-00_amd64.deb ...
Unpacking kubectl (1.20.0-00) ...
Selecting previously unselected package kubeadm.
Preparing to unpack .../7-kubeadm_1.20.0-00_amd64.deb ...
Unpacking kubeadm (1.20.0-00) ...
Setting up conntrack (1:1.4.6-2build2) ...
Setting up kubectl (1.20.0-00) ...
Setting up ebtables (2.0.11-4build2) ...
Setting up socat (1.7.4.1-3ubuntu4) ...
Setting up cri-tools (1.26.0-00) ...
Setting up kubernetes-cni (1.2.0-00) ...
Setting up kubelet (1.20.0-00) ...
Created symlink /etc/systemd/system/multi-user.target.wants/kubelet.service → /lib/systemd/system/kubelet.service.
Setting up kubeadm (1.20.0-00) ...
Processing triggers for man-db (2.10.2-1) ...
Scanning processes...                                                                                                                         
Scanning linux images...                                                                                                                      

Running kernel seems to be up-to-date.

No services need to be restarted.

No containers need to be restarted.

No user sessions are running outdated binaries.

No VM guests are running outdated hypervisor (qemu) binaries on this host.
ubuntu@ip-172-31-11-137:~$ sudo kubeadm reset pre-flight checks
[reset] WARNING: Changes made to this host by 'kubeadm init' or 'kubeadm join' will be reverted.
[reset] Are you sure you want to proceed? [y/N]: y
[preflight] Running pre-flight checks
W0101 01:24:31.895148    3553 removeetcdmember.go:79] [reset] No kubeadm config, using etcd pod spec to get data directory
[reset] No etcd config found. Assuming external etcd
[reset] Please, manually reset etcd to prevent further issues
[reset] Stopping the kubelet service
[reset] Unmounting mounted directories in "/var/lib/kubelet"
W0101 01:24:31.901567    3553 cleanupnode.go:99] [reset] Failed to evaluate the "/var/lib/kubelet" directory. Skipping its unmount and cleanup: lstat /var/lib/kubelet: no such file or directory
[reset] Deleting contents of config directories: [/etc/kubernetes/manifests /etc/kubernetes/pki]
[reset] Deleting files: [/etc/kubernetes/admin.conf /etc/kubernetes/kubelet.conf /etc/kubernetes/bootstrap-kubelet.conf /etc/kubernetes/controller-manager.conf /etc/kubernetes/scheduler.conf]
[reset] Deleting contents of stateful directories: [/var/lib/dockershim /var/run/kubernetes /var/lib/cni]

The reset process does not clean CNI configuration. To do so, you must remove /etc/cni/net.d

The reset process does not reset or clean up iptables rules or IPVS tables.
If you wish to reset iptables, you must do so manually by using the "iptables" command.

If your cluster was setup to utilize IPVS, run ipvsadm --clear (or similar)
to reset your system's IPVS tables.

The reset process does not clean your kubeconfig files and you must remove them manually.
Please, check the contents of the $HOME/.kube/config file.
ubuntu@ip-172-31-11-137:~$ kubeadm join 172.31.4.107:6443 --token nepvad.go4fm1iwp2gogkad     --discovery-token-ca-cert-hash sha256:9f6c6316ae29f51a8d0a12d01aa429546770ca7f2e64ebc92eee972137a16382 --v5
unknown flag: --v5
To see the stack trace of this error execute with --v=5 or higher
ubuntu@ip-172-31-11-137:~$ kubeadm join 172.31.4.107:6443 --token nepvad.go4fm1iwp2gogkad     --discovery-token-ca-cert-hash sha256:9f6c6316ae29f51a8d0a12d01aa429546770ca7f2e64ebc92eee972137a16382 --v=5
I0101 01:27:12.446755    3575 join.go:395] [preflight] found NodeName empty; using OS hostname as NodeName
I0101 01:27:12.446882    3575 initconfiguration.go:104] detected and using CRI socket: /var/run/dockershim.sock
[preflight] Running pre-flight checks
I0101 01:27:12.446952    3575 preflight.go:90] [preflight] Running general checks
[preflight] Some fatal errors occurred:
	[ERROR IsPrivilegedUser]: user is not running as root
[preflight] If you know what you are doing, you can make a check non-fatal with `--ignore-preflight-errors=...`
error execution phase preflight
k8s.io/kubernetes/cmd/kubeadm/app/cmd/phases/workflow.(*Runner).Run.func1
	/workspace/src/k8s.io/kubernetes/_output/dockerized/go/src/k8s.io/kubernetes/cmd/kubeadm/app/cmd/phases/workflow/runner.go:235
k8s.io/kubernetes/cmd/kubeadm/app/cmd/phases/workflow.(*Runner).visitAll
	/workspace/src/k8s.io/kubernetes/_output/dockerized/go/src/k8s.io/kubernetes/cmd/kubeadm/app/cmd/phases/workflow/runner.go:421
k8s.io/kubernetes/cmd/kubeadm/app/cmd/phases/workflow.(*Runner).Run
	/workspace/src/k8s.io/kubernetes/_output/dockerized/go/src/k8s.io/kubernetes/cmd/kubeadm/app/cmd/phases/workflow/runner.go:207
k8s.io/kubernetes/cmd/kubeadm/app/cmd.newCmdJoin.func1
	/workspace/src/k8s.io/kubernetes/_output/dockerized/go/src/k8s.io/kubernetes/cmd/kubeadm/app/cmd/join.go:172
k8s.io/kubernetes/vendor/github.com/spf13/cobra.(*Command).execute
	/workspace/src/k8s.io/kubernetes/_output/dockerized/go/src/k8s.io/kubernetes/vendor/github.com/spf13/cobra/command.go:850
k8s.io/kubernetes/vendor/github.com/spf13/cobra.(*Command).ExecuteC
	/workspace/src/k8s.io/kubernetes/_output/dockerized/go/src/k8s.io/kubernetes/vendor/github.com/spf13/cobra/command.go:958
k8s.io/kubernetes/vendor/github.com/spf13/cobra.(*Command).Execute
	/workspace/src/k8s.io/kubernetes/_output/dockerized/go/src/k8s.io/kubernetes/vendor/github.com/spf13/cobra/command.go:895
k8s.io/kubernetes/cmd/kubeadm/app.Run
	/workspace/src/k8s.io/kubernetes/_output/dockerized/go/src/k8s.io/kubernetes/cmd/kubeadm/app/kubeadm.go:50
main.main
	_output/dockerized/go/src/k8s.io/kubernetes/cmd/kubeadm/kubeadm.go:25
runtime.main
	/usr/local/go/src/runtime/proc.go:204
runtime.goexit
	/usr/local/go/src/runtime/asm_amd64.s:1374
ubuntu@ip-172-31-11-137:~$ sudo kubeadm join 172.31.4.107:6443 --token nepvad.go4fm1iwp2gogkad     --discovery-token-ca-cert-hash sha256:9f6c6316ae29f51a8d0a12d01aa429546770ca7f2e64ebc92eee972137a16382 --v=5
I0101 01:27:41.254308    3583 join.go:395] [preflight] found NodeName empty; using OS hostname as NodeName
I0101 01:27:41.254423    3583 initconfiguration.go:104] detected and using CRI socket: /var/run/dockershim.sock
[preflight] Running pre-flight checks
I0101 01:27:41.254506    3583 preflight.go:90] [preflight] Running general checks
I0101 01:27:41.254555    3583 checks.go:249] validating the existence and emptiness of directory /etc/kubernetes/manifests
I0101 01:27:41.254652    3583 checks.go:286] validating the existence of file /etc/kubernetes/kubelet.conf
I0101 01:27:41.254688    3583 checks.go:286] validating the existence of file /etc/kubernetes/bootstrap-kubelet.conf
I0101 01:27:41.254732    3583 checks.go:102] validating the container runtime
I0101 01:27:41.282649    3583 checks.go:128] validating if the "docker" service is enabled and active
I0101 01:27:41.324746    3583 checks.go:335] validating the contents of file /proc/sys/net/bridge/bridge-nf-call-iptables
I0101 01:27:41.324824    3583 checks.go:335] validating the contents of file /proc/sys/net/ipv4/ip_forward
I0101 01:27:41.324923    3583 checks.go:649] validating whether swap is enabled or not
I0101 01:27:41.325012    3583 checks.go:376] validating the presence of executable conntrack
I0101 01:27:41.325106    3583 checks.go:376] validating the presence of executable ip
I0101 01:27:41.325188    3583 checks.go:376] validating the presence of executable iptables
I0101 01:27:41.325248    3583 checks.go:376] validating the presence of executable mount
I0101 01:27:41.325306    3583 checks.go:376] validating the presence of executable nsenter
I0101 01:27:41.325397    3583 checks.go:376] validating the presence of executable ebtables
I0101 01:27:41.325479    3583 checks.go:376] validating the presence of executable ethtool
I0101 01:27:41.325547    3583 checks.go:376] validating the presence of executable socat
I0101 01:27:41.325658    3583 checks.go:376] validating the presence of executable tc
I0101 01:27:41.325765    3583 checks.go:376] validating the presence of executable touch
I0101 01:27:41.325848    3583 checks.go:520] running all checks
	[WARNING SystemVerification]: this Docker version is not on the list of validated versions: 24.0.5. Latest validated version: 19.03
I0101 01:27:41.424142    3583 checks.go:406] checking whether the given node name is reachable using net.LookupHost
I0101 01:27:41.425459    3583 checks.go:618] validating kubelet version
I0101 01:27:41.510221    3583 checks.go:128] validating if the "kubelet" service is enabled and active
I0101 01:27:41.522343    3583 checks.go:201] validating availability of port 10250
I0101 01:27:41.522570    3583 checks.go:286] validating the existence of file /etc/kubernetes/pki/ca.crt
I0101 01:27:41.522604    3583 checks.go:432] validating if the connectivity type is via proxy or direct
I0101 01:27:41.522680    3583 join.go:465] [preflight] Discovering cluster-info
I0101 01:27:41.522713    3583 token.go:78] [discovery] Created cluster-info discovery client, requesting info from "172.31.4.107:6443"
I0101 01:27:51.523607    3583 token.go:215] [discovery] Failed to request cluster-info, will try again: Get "https://172.31.4.107:6443/api/v1/namespaces/kube-public/configmaps/cluster-info?timeout=10s": context deadline exceeded (Client.Timeout exceeded while awaiting headers)
I0101 01:28:07.520972    3583 token.go:215] [discovery] Failed to request cluster-info, will try again: Get "https://172.31.4.107:6443/api/v1/namespaces/kube-public/configmaps/cluster-info?timeout=10s": net/http: request canceled while waiting for connection (Client.Timeout exceeded while awaiting headers)
I0101 01:28:23.178195    3583 token.go:215] [discovery] Failed to request cluster-info, will try again: Get "https://172.31.4.107:6443/api/v1/namespaces/kube-public/configmaps/cluster-info?timeout=10s": net/http: request canceled while waiting for connection (Client.Timeout exceeded while awaiting headers)
I0101 01:28:38.815625    3583 token.go:215] [discovery] Failed to request cluster-info, will try again: Get "https://172.31.4.107:6443/api/v1/namespaces/kube-public/configmaps/cluster-info?timeout=10s": context deadline exceeded (Client.Timeout exceeded while awaiting headers)
I0101 01:28:54.846428    3583 token.go:215] [discovery] Failed to request cluster-info, will try again: Get "https://172.31.4.107:6443/api/v1/namespaces/kube-public/configmaps/cluster-info?timeout=10s": net/http: request canceled while waiting for connection (Client.Timeout exceeded while awaiting headers)
I0101 01:29:07.194426    3583 token.go:116] [discovery] Requesting info from "172.31.4.107:6443" again to validate TLS against the pinned public key
I0101 01:29:07.202571    3583 token.go:133] [discovery] Cluster info signature and contents are valid and TLS certificate validates against pinned roots, will use API Server "172.31.4.107:6443"
I0101 01:29:07.202608    3583 discovery.go:51] [discovery] Using provided TLSBootstrapToken as authentication credentials for the join process
I0101 01:29:07.202625    3583 join.go:479] [preflight] Fetching init configuration
I0101 01:29:07.202634    3583 join.go:517] [preflight] Retrieving KubeConfig objects
[preflight] Reading configuration from the cluster...
[preflight] FYI: You can look at this config file with 'kubectl -n kube-system get cm kubeadm-config -o yaml'
I0101 01:29:07.212413    3583 interface.go:400] Looking for default routes with IPv4 addresses
I0101 01:29:07.212437    3583 interface.go:405] Default route transits interface "eth0"
I0101 01:29:07.212578    3583 interface.go:208] Interface eth0 is up
I0101 01:29:07.212645    3583 interface.go:256] Interface "eth0" has 2 addresses :[172.31.11.137/20 fe80::bc:75ff:fed6:600b/64].
I0101 01:29:07.212681    3583 interface.go:223] Checking addr  172.31.11.137/20.
I0101 01:29:07.212691    3583 interface.go:230] IP found 172.31.11.137
I0101 01:29:07.212707    3583 interface.go:262] Found valid IPv4 address 172.31.11.137 for interface "eth0".
I0101 01:29:07.212721    3583 interface.go:411] Found active IP 172.31.11.137 
I0101 01:29:07.217176    3583 preflight.go:101] [preflight] Running configuration dependant checks
I0101 01:29:07.217340    3583 controlplaneprepare.go:211] [download-certs] Skipping certs download
I0101 01:29:07.217457    3583 kubelet.go:110] [kubelet-start] writing bootstrap kubelet config file at /etc/kubernetes/bootstrap-kubelet.conf
I0101 01:29:07.218428    3583 kubelet.go:118] [kubelet-start] writing CA certificate at /etc/kubernetes/pki/ca.crt
I0101 01:29:07.219156    3583 kubelet.go:139] [kubelet-start] Checking for an existing Node in the cluster with name "ip-172-31-11-137" and status "Ready"
I0101 01:29:07.221766    3583 kubelet.go:153] [kubelet-start] Stopping the kubelet
[kubelet-start] Writing kubelet configuration to file "/var/lib/kubelet/config.yaml"
[kubelet-start] Writing kubelet environment file with flags to file "/var/lib/kubelet/kubeadm-flags.env"
[kubelet-start] Starting the kubelet
[kubelet-start] Waiting for the kubelet to perform the TLS Bootstrap...
I0101 01:29:12.498786    3583 cert_rotation.go:137] Starting client certificate rotation controller
I0101 01:29:12.501730    3583 kubelet.go:188] [kubelet-start] preserving the crisocket information for the node
I0101 01:29:12.501756    3583 patchnode.go:30] [patchnode] Uploading the CRI Socket information "/var/run/dockershim.sock" to the Node API object "ip-172-31-11-137" as an annotation

This node has joined the cluster:
* Certificate signing request was sent to apiserver and a response was received.
* The Kubelet was informed of the new secure connection details.

Run 'kubectl get nodes' on the control-plane to see this node join the cluster.

ubuntu@ip-172-31-11-137:~$ kubectl get nodes
The connection to the server localhost:8080 was refused - did you specify the right host or port?
ubuntu@ip-172-31-11-137:~$ sudo docker ps
CONTAINER ID   IMAGE                   COMMAND                  CREATED              STATUS              PORTS     NAMES
f6050b06c1b9   weaveworks/weave-npc    "/usr/bin/launch.sh"     About a minute ago   Up About a minute             k8s_weave-npc_weave-net-n8bjd_kube-system_6d8f9802-5f8d-4322-b9ef-7b5df84eed1d_0
53fb50787197   weaveworks/weave-kube   "/home/weave/launch.…"   About a minute ago   Up About a minute             k8s_weave_weave-net-n8bjd_kube-system_6d8f9802-5f8d-4322-b9ef-7b5df84eed1d_0
141b4b1bd7b4   k8s.gcr.io/kube-proxy   "/usr/local/bin/kube…"   About a minute ago   Up About a minute             k8s_kube-proxy_kube-proxy-b9g6z_kube-system_ad3f17ed-edf3-4d68-abcb-2b65b5d78c7a_0
2954593a0a0c   k8s.gcr.io/pause:3.2    "/pause"                 About a minute ago   Up About a minute             k8s_POD_weave-net-n8bjd_kube-system_6d8f9802-5f8d-4322-b9ef-7b5df84eed1d_0
cae751b3950d   k8s.gcr.io/pause:3.2    "/pause"                 About a minute ago   Up About a minute             k8s_POD_kube-proxy-b9g6z_kube-system_ad3f17ed-edf3-4d68-abcb-2b65b5d78c7a_0
ubuntu@ip-172-31-11-137:~$ Connection to ec2-13-239-34-239.ap-southeast-2.compute.amazonaws.com closed by remote host.
Connection to ec2-13-239-34-239.ap-southeast-2.compute.amazonaws.com closed.











