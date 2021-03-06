+++
#Don't remove title!
title = "Getting started"
+++

<a name="Introduction" class="internal-ref"></a>
### Introduction

This section provides a quick getting started guide on how to setup Open
vStorage on your servers. A detailed guide on how to setup Open vStorage
can be found [here](/doc/Installation). You can select between the
[VMware](/doc/ESXi-Installation) and [KVM](/doc/KVM%20Installation) installation
instructions. Once the installation is complete you can start creating
vMachines on top of Open vStorage.

<a name="KVM" class="internal-ref"></a>
### KVM

The quick install guide for KVM:

-   Install [Ubuntu server 14.04.2 64 bit
    ISO](http://releases.ubuntu.com/14.04.2/ubuntu-14.04.2-server-amd64.iso)
    on the server. Install the Open SSH server and the Virtual Machine
    Host.
-   Install the KVM packages (see below instructions) or install
    OpenStack Juno (See [OpenStack
    documentation](http://docs.openstack.org/juno/install-guide/install/apt/content/))
    on all nodes

~~~~ {.sourceCode .python}
sudo apt-get install kvm libvirt0 python-libvirt virtinst
~~~~

-   Update the root password to allow direct root login via ssh.

~~~~ {.sourceCode .python}
sudo su -
passwd
~~~~
-   Decrease the swapiness of the OS:

~~~~ {.sourceCode .python}
echo 1 > /proc/sys/vm/swappiness
echo "vm.swappiness=1" >> /etc/sysctl.conf
~~~~
-   Update the VM dirty_background_bytes:

~~~~ {.sourceCode .python}
echo "134217728" > /proc/sys/vm/dirty_background_bytes
echo "vm.dirty_background_bytes = 134217728" >> /etc/sysctl.conf
~~~~

-   Install the Open vStorage software (Open vStorage core and backend
    packages) on all KVM nodes:

~~~~ {.sourceCode .python}
echo "deb http://apt.openvstorage.org chicago-community main" > /etc/apt/sources.list.d/ovsaptrepo.list
apt-get update; apt-get install openvstorage-hc
~~~~

-   Next execute following command in the shell of the first KVM and
    answer the configuration questions.

~~~~ {.sourceCode .python}
ovs setup
~~~~

-   Open the GUI on the IP address of the KVM node and login with the
    default administrator credentials username:admin, password:admin.
-   Go to the Storage Router detail page and select the Physical Disk Management tab. Assign a DB and scrub role to at least one SSD disk. Assign read and write roles to SSDs you want to use as cache.
-   In case you want to run Open vStorage hyperconverged, create an Open
    vStorage Backend in the Backend section. Note that running Open
    vStorage as hyperconverged solution will require you to [get a free
    license](http://license.openvstorage.com).
-   To add more nodes execute in the KVM shell of every node (concurrent installations of multiple nodes isn't supported):

~~~~ {.sourceCode .python}
ovs setup
~~~~

-   If you installed OpenStack, configure the [Cinder
    Plugin](/doc/Cinder%20Plugin).
-   Next, create your first vPool in the vPools section.
-   Feel free to [contribute](/doc/Contribute) to the Open vStorage code or
    [get help from the Open vStorage
    community](https://groups.google.com/forum/#!forum/open-vstorage).
    Keep in mind that only .raw disks are supported on KVM with Open
    vStorage.

<a name="VMware" class="internal-ref"></a>
### VMware

The quick install guide for ESXi:

-   Read through the [Essentials](/doc/Essentials) to understand what is
    supported and what not.
-   Download the [Ubuntu server 14.04.2 64 bit
    ISO](http://releases.ubuntu.com/14.04.2/ubuntu-14.04.2-server-amd64.iso)
    and the
    [deployOVS](http://download.openvstorage.com/index.php?file=deployOvs)
    script.
-   On all ESXi nodes, upload the Ubuntu 14.04.2 Server ISO and the
    [deployOvs.py
    script](https://github.com/openvstorage/openvstorage/blob/master/scripts/deployment/deployOvs.py)
    to the ESXi Datastore and execute "./deployOvs.py --image
    /vmfs/volumes/datastore1/ubuntu-14.04.2-server-amd64.iso".
-   Select a Public Network and a Storage Network and follow the
    on-screen install instructions.
-   Install Ubuntu inside the Storage Router (a VM on VMware running the
    Open vStorage software) of each ESXi node. Next to the Public IP
    address, give each Storage Router the IP address in the Storage
    Network.
-   Install the Open vStorage software (Open vStorage core and backend
    packages) inside each Storage Router.

~~~~ {.sourceCode .python}
echo "deb http://apt.openvstorage.org chicago-community main" > /etc/apt/sources.list.d/ovsaptrepo.list
apt-get update; apt-get install openvstorage-hc
~~~~

-   Next execute following command in the shell of the first Storage
    Router and answer the configuration questions.

~~~~ {.sourceCode .python}
ovs setup
~~~~

-   Open the GUI on the IP address of the KVM node and login with the
    default administrator credentials username:admin, password:admin.
-   Go to the Storage Router detail page and select the Physical Disk Management tab. Assign a DB and scrub role to at least one SSD disk. Assign read and write roles to SSDs you want to use as cache.
-   In case you want to run Open vStorage hyperconverged, create an Open
    vStorage Backend in the Backend section. Note that running Open
    vStorage as hyperconverged solution will require you to [get a free
    license](http://license.openvstorage.com). Next, create your first
    vPool in the vPools section.
-   Add the vPool as Datastore to ESXi. Enter as Server the *Storage* IP
    address of the Storage Router,as Folder /mnt/name of the vPool and
    give the Datastore the name of the vPool.
-   Create a new Virtual Machine in the vSphere GUI with vDisks on the
    vPool.
-   To add more Storage Routers, execute in the shell of every Storage Router (concurrent installations of multiple nodes isn't supported):

~~~~ {.sourceCode .python}
ovs setup
~~~~

-   Extend the vPool across the newly created Storage Routers in the
    Open vStorage GUI.
-   Feel free to [contribute](/doc/Contribute) to the Open vStorage code or
    [get help from the Open vStorage
    community](https://groups.google.com/forum/#!forum/open-vstorage).
