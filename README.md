## A step-by-step guide to hosting Codalab-v2 instance in de.NBI OpenStack cloud

### Contents

- [1. Introduction](#1-introduction)
  - [1.1 Acknowledgements](#acknowledgements)
  - [1.2 Understand some lingo](#before-we-start)
  - [1.3 Prerequisites](#prerequisites)
- [2. deNBI dashboard](#2-deNBI-dashboard)
  - [2.1 SSH-keys](#ssh-keys)
  - [2.2 Resource modification](#resource-modification)
  - [2.3 Wiki help](#wiki-help)
  - [2.4 OpenStack login](#openstack-login)
- [3. Create VM instances](#3-create-vm-instances-using-the-openstack-dashboard)
  - [3.1 Create required network topology](#create-required-network-topology)
  - [3.2 Launch VM that will host Codalabv2 main instance](#launch-vm-that-will-host-codalabv2-main-instance)
  - [3.3 Associate the Floating IP address](#associate-the-floating-ip-address)
  - [3.4 Launch VM that will host Codalabv2 worker instance](#launch-vm-that-will-host-codalabv2-worker-instance)
- [4. SSH into your VM](#4-ssh-into-your-vm)
  - [4.1 Ping test and change Netplan](#ping-test-and-change-netplan)
  - [4.2 Download Docker](#download-docker)
  - [4.3 Download Docker Compose](#download-docker-compose)
  - [4.4 Change Docker MTU value](#change-docker-mtu-value)
- [5. Install Codalabv2 competition instance](#5-install-codalabv2-competition-instance)
  - [5.1 Codalabv2 Installation](#codalabv2-installation)
  - [5.2 Edit the `.env` file](#edit-the-env-file)
- [6. Install Codalabv2 worker instance](#6-install-codalabv2-worker-instance)
  - [6.1 Broker URL](#broker-url)
- [7. Install the METEOR COMETH app](#7-install-the-meteor-cometh-app)
  - [7.1 COMETH app Installation](#cometh-app-installation)
  - [7.2 COMETH app configuration](#cometh-app-configuration)

## 1. Introduction

### Acknowledgements

I would like to specifically acknowledge the extensive technical help and support provided by [Matin Braun](https://www.hidih.org/research/health-data) on (issues related to deNBI, OpenStack cloud setup etc) and Eric Carmichael (https://ericcarmichael.com/) on (Codalabv2 installation). More importantly, they are super cool, smart and enthusiastic people to work with. Thanks a ton!! :thumbsup:

---

### Before we start

- **What is COMETH ?**

- **What is Codalab ?**

- **What is de.NBI ?**

- **What is ELIXIR ?**

- **What is de.NBI cloud ?**

- **What is OpenStack ?**

----

### Prerequisites

This tutorial presumes that you have already performed these steps -

- Created a valid functioning deNBI cloud account
- Requested and obtained the necessary computational resources for setting up a virtual machine (VM)

---

If not, fulfil these prerequisites by following these steps - 

- **Create an ELIXIR account**

An ELIXIR account can be most simply created if you have a valid account for any of these participating [German institutions](https://elixir-europe.org/about-us/who-we-are/nodes/germany). Alternatively, if you do not belong to these institutions, ELIXIR accounts can also be created through - 

- Google account
- Linkedin
- ORCID

> **NOTE:** During the ELIXIR account registration process kindly note down your _Elixir username_. This will be needed and will come handy later on.

- **Create a deNBI account**

Perform your account registration and request for a project resources allocation (#CPU, memory, storage etc) by following the instructions provided here - 

1. [Getting started](https://cloud.denbi.de/get-started/)
2. [Registration](https://cloud.denbi.de/wiki/registration/)
3. [Resource allocation](https://cloud.denbi.de/wiki/portal/allocation/)

> **NOTE:** Your project request will be approved by the Admins only if you (are a PI) or your PI belongs to deNBI participating German institute. You can of course add as many members from participating or non-participating institutes.

Ok, so now you have a deNBI cloud account and the Admins have sanctioned your project resources. Let's continue with the next steps.

[Back to top](#contents)

---

## 2. deNBI dashboard

### SSH Keys

### Resource modification

### Wiki help

### OpenStack login

![alt text](/images/01_deNBI_landingpage.jpg)

**Figure 1. deNBI dashboard landing page. In boxes, shown important sections.**

---

[Back to top](#contents)

---

## 3. Create VM instances using the OpenStack dashboard

> Also, look into the detailed explanation for the various aspects of the [OpenStack dashboard](https://docs.openstack.org/horizon/latest/user/index.html)

> **IMPORTANT:** Make sure to ask the Admins for a public IP address while making the request for resouces for the VMs incase you want your web server (codalabv2 instance) to be visible publicly.

### Create required network topology

1. You have logged into OpenStack dashboard and reached its landing page.

![alt text](/images/02_openstack_network_landingpage.jpg)

**Figure 2. Openstack dashboard landing page. In boxes, shown the way to network topology.**

---

2. Check the initial network topology available, you can change between `Topology` and `Graph` views. Go to - ``` Projects > Network > Network topology ```

![alt text](/images/03_openstack_network_topology_firstlook.jpg)

**Figure 3. The initial cloud network topology.**

---

3. Create a new router named `dmz-ext` and connect it to the external network also named `dmz-ext`. Go to - ``` Projects > Network > Routers > +Create Router ```
  
![alt text](/images/04_openstack_create_router.jpg)
  
**Figure 4. Create a new router.**

---

4. Create a new network named `dmz-int`, use `10.0.1.0/24` as the subnet network address and DNS server should be assigned to `172.16.114.8`. Go to - ``` Projects > Network > Networks > +Create Network ```

![alt text](/images/05_openstack_create_networks.jpg)
  
**Figure 5. Create a new network.**

---

5. Add a route from `dmz-int` subnet to `dmz-ext` router by adding a new interface on the `dmz-ext` router. ``` Projects > Network > Routers > dmz-ext > +Add interface ```

![alt text](/images/06_openstack_link_dmz_ezt_and_dmz_int.jpg)
  
**Figure 6. Linking `dmz-ext` and `dmz-int`.**

---

6. After all these steps the final Network topology should look like below. Go to ``` Projects > Network > Network topology ```

![alt text](/images/07_openstack_network_topology_finallook.jpg)

**Figure 7. Final network topology view.**

---

### Launch VM that will host Codalabv2 main instance

In this section we will create the 

1. Go to - ```Project > Compute > Instances```

2. Click on `Launch instance`

3. In `Details` tab, provide an instance name

4. In `Source` tab, **Select Boot Source** as `Image` and set **Create New Volume** to `No`. Also, select `Ubuntu-20.04` as your image

5. In `Flavor` tab, select `deNBI medium` as your VM of choice

6. In `Network` tab, select both `dmz-int` and `MapMyCorona-network`

7. Launch the instance

![alt text](/images/08_openstack_launch_instance.jpg)

**Figure 8. Launching a VM instance.**

---

### Associate the Floating IP address

1. Go to ```Project > Compute > Instances```. 

2. From the drop down menu, select **Associate Floasting IP**

3. From the list of IPs, select `172.16.114.203`. This IP has been associated to the Public IP address which was made available to us by the admins.

4. Make sure to select the port `10.0.1.86`, i.e the one associated to `dmz-int` whcih is visble publicly, as we eventually want to access the webserver running in this VM from our local browsers.

![alt text](/images/09_openstack_associate_floatingIP.jpg)

**Figure 9. Associating floating IPs.**

---

5. Next, make sure that the **security groups**  allow `port 80/443`, i.e `HTTP and HTTPS`. Go to `Project > Network > Security Groups > Manage Rules

6. If these ports or any other required ports (by the webserver) are not open, then open them using `+Add Rule`

![alt text](/images/10_openstack_security_groups.jpg)

**Figure 10. Security groups to allow important ports.**

---

### Launch VM that will host Codalabv2 worker instance

Now, we will also launch 3 VM instances that will serve as **compute workers**. Repeat the same steps from [above](#launch-vm-that-will-host-codalabv2-main-instance) to launch instance and the the [steps](#associate-the-floating-ip-address) to associate an IP address.

The three worker VMs will be

1. Small memory worker VM with 16GB RAM, 8VPCUs and 20GB storage
2. Large memory worker VM with 64GB RAM, 32 VCPUs and 20GB storage
3. GPU based worker VM with 64GB RAM, 16VCPUs and 20GB storage

**The most important differences while launcing these VMs are -**

1. While launching the instance **do not** link the `dmz-int` network in `Network` tab. **Only** select `MapMyCorona-network`
2. While associating the floating IP only select these IP withe these numbering - `172.16.103.*`

If all went well, the VM overview at ```Project > Compute > Instances``` and the network topology at ``` Projects > Network > Network topology ``` should look like below

![alt text](/images/11_openstack_VM_overview.jpg)

**Figure 11. Final VM overview and VM network topology.**

---

[Back to top](#contents)

---

## 4. SSH into your VM

Add the following to your `~/.ssh/config` file. Doing this allows us to access the VMs directly from the local terminal without having to go through a jump host. Make sure to use the correct **User** this is the ELIXIR username provided in the deNBI Profile page, see [here](#2-deNBI-dashboard). Also the location and file `~/.ssh/mmc.key` should have your **private SSH key**, which is the counterpart of the **public SSH key** stored in deNBI dashboard, see [here](#2-deNBI-dashboard).

```
#-------------------------------------------
# Berlin deNBI
#-------------------------------------------

Host denbi-jumphost-01.bihealth.org
  User ashwinkr
  IdentityFile ~/.ssh/mmc.key
  ServerAliveInterval 120

Host 172.16.103.* 172.16.114.203
  ProxyJump denbi-jumphost-01.bihealth.org
  IdentityFile ~/.ssh/mmc.key
  ServerAliveInterval 120
  
```
Hopefully, after this you can login to the VM by simply executing the following in your terminal 

```
ssh ubuntu@172.16.114.203
ssh ubuntu@172.16.103.93
ssh ubuntu@172.16.103.94
ssh ubuntu@172.16.103.180
```

You migh get a message like shown below, simply type `yes` and you will be in your main VM -

```
The authenticity of host '172.16.114.203 (<no hostip for proxy command>)' can't be established.
ECDSA key fingerprint is SHA256:Sak9x3waWdiPGhmncyLZBLXdLNq4ovj7Vn/zZTfz5Rw.
Are you sure you want to continue connecting (yes/no/[fingerprint])
```

### Ping test and change Netplan

```

 Ping test
 
 ```
 
 ping 10.0.0.5
 ping 10.0.0.200
 ping 10.0.0.127
 
 ```

```

### Download Docker

### Download Docker Compose

### Change Docker MTU value

[Back to top](#contents)

---

## 5. Install Codalabv2 competition instance

### Codalabv2 Installation

### Edit the `.env` file

[Back to top](#contents)

---

## 6. Install Codalabv2 worker instance

### Broker URL

[Back to top](#contents)

---

## 7. Install the METEOR COMETH app

### COMETH app Installation

### COMETH app configuration

[Back to top](#contents)

---
