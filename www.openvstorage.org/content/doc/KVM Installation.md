+++
#Don't remove title!
title = "KVM Installation"
+++

### Introduction

This section provides a detailed step-by-step guide on how to setup Open
vStorage on 3 or more nodes running the KVM hypervisor. At the end of
the guide you will be ready to deploy a Virtual Machine on the Open
vStorage Cluster. When you encounter issues or are stuck somewhere, do
not hesitate to ask for help in the public [Open vStorage
Forum](https://groups.google.com/forum/#!forum/open-vstorage). *Only
.raw disks are supported on KVM with Open vStorage.*

### What do you need to install Open vStorage on multiple nodes?

-   Live Migration will only be possible in case a central Storage
    Backend (Ceph, Swift, ...) or a distributed file system is selected
    for the actual data of the vMachines. vMotion on a vPool with local
    disk will not be possible.
-   At least 3 KVM compliant servers. For performance reasons at least 1
    SSD or PCI-flash card (min. 100GB) is required.
-   [Ubuntu server 14.04.2 64 bit
    ISO](http://releases.ubuntu.com/14.04.2/ubuntu-14.04.2-server-amd64.iso).
-   A Bitbucket account. You can create a [Bitbucket account for
    free](https://bitbucket.org/).
-   At least 400 GB of free space on the OS partition.
-   In case you want to install Open vStorage Hyper-converged, at least 3 additional SATA disks are required for the Open vStorage Backend.
-   Per GB read cache on SSD/PCI-e flash, you will need to reserve 10 MB RAM of the Host for Open vStorage.

### Prerequisites before starting this guide:

-   Enable all C-states in the BIOS of the Hosts for optimal
    performance.
-   Ensure no remainder of previous Open vStorage installs are left
    behind (e.g. previous vMachines or vPools) on the servers.


<a name="installkvm" class="internal-ref"></a>
### Install KVM on all nodes

-   Install [Ubuntu server 14.04.2 64 bit
    ISO](http://releases.ubuntu.com/14.04.2/ubuntu-14.04.2-server-amd64.iso)
    on the server, select your language and select *Install Ubuntu
    Server*.
-   Confirm your language, select your location and configure your
    keyboard.
-   Configure the network of the node. Select the NIC which is connected
    to the Public port group. Enter the IP address, netmask, gateway and
    DNS servers. The GUI will be made available on the set IP address.
-   Set a hostname, create a new user (do not use ovs as username) and
    enter a password. There is no need to encrypt the home directory.
-   Select your timezone and partition the disks of the server. Write
    the changes to the disk.
-   Leave the HTTP proxy information blank and select *No automatic
    updates*.
-   Install the Open SSH server and the Virtual Machine Host.
-   Install the GRUB boot loader.
-   Finish the Ubuntu installation by selecting *\<Continue\>*.

![](images/finish\_install.png)

-   Boot the server and install additional KVM packages.

~~~~ {.sourceCode .python}
sudo apt-get install kvm libvirt0 python-libvirt virtinst
~~~~

Repeat the above steps for all KVM Nodes in the Open vStorage Cluster.


 <a name="configureubuntu" class="internal-ref"></a>
### Configure Ubuntu

-   Update the root password to allow direct root login via ssh. Login
    and type

~~~~ {.sourceCode .python}
sudo su -
passwd
~~~~

-   Make sure that PermitRootLogin is set to yes (instead of
    without-password) in /etc/ssh/sshd\_config and restart ssh by typing
    *restart ssh*.
-   Decrease the swapiness of the OS:

~~~~ {.sourceCode .python}
echo 1 > /proc/sys/vm/swappiness
echo "vm.swappiness=1" >> /etc/sysctl.conf
~~~~

-   Configure an NTP Server and install ntpd:

~~~~ {.sourceCode .python}
sudo apt-get install ntp


* Edit /etc/ntp.conf to add/remove NTP servers. By default these servers are configured:
~~~~

~~~~ {.sourceCode .python}
# Use servers from the NTP Pool Project. Approved by Ubuntu Technical Board
# on 2011-02-08 (LP: #104525). See http://www.pool.ntp.org/join.html for
# more information.
server 0.ubuntu.pool.ntp.org
server 1.ubuntu.pool.ntp.org
server 2.ubuntu.pool.ntp.org
server 3.ubuntu.pool.ntp.org


* After changing the config file you have to reload the ntpd:
~~~~

~~~~ {.sourceCode .python}
sudo /etc/init.d/ntp reload
~~~~




-   You can now go to the [Install the Open vStorage
    software]( {{< relref "KVM Installation.md" >}}#installovs ) section.

Repeat the above steps for all nodes in the Open vStorage Cluster.

<a name="installovs" class="internal-ref"></a>
### Install the Open vStorage software on all Storage Routers

The Open vStorage software first needs to be installed on all KVMs.
Execute the next steps in the shell of all KVM Nodes:

-   Add the repo to your sources

~~~~ {.sourceCode .python}
echo "deb http://apt-ovs.cloudfounders.com beta/" > /etc/apt/sources.list.d/ovsaptrepo.list
~~~~

There are 2 options to install Open vStorage. The first option is to
install Open vStorage and the Open vStorage Backend packages. When you
install the Open vStorage Backend packages you can use the disks inside
the Nova host as Tier 2 storage (hyperconverged). You will need a
license to enable these packages. There is free license with
restrictions available which can be requested from the License section
under [Administration](/doc/Using%20the%20GUI#administration). The second
option is to only install the Open vStorage core packages. Without the
Open vStorage Backend packages you will need to set up a separate Tier 2
Storage Backend (Swift, Ceph, GlusterFS, ...). There is no license
required when installing only the Open vStorage core packages.

-   Install Open vStorage HyperConverged

~~~~ {.sourceCode .python}
apt-get update; apt-get install openvstorage-hc
~~~~

-   Install the Open vStorage packages *without* the Open vStorage
    Backend packages

~~~~ {.sourceCode .python}
apt-get update; apt-get install  openvstorage
~~~~

### Initialize the first Storage Router

-   You are now ready to initialize the first Storage Router. Execute in
    the KVM shell:

~~~~ {.sourceCode .python}
ovs setup
~~~~

The initialization script will ask a couple of questions:

-   Enter the root credentials for the KVM Node.
-   It will search for existing Open vStorage Clusters in the network.
    In case it has found a Cluster, select the option *Don't join any of
    these clusters.*.
-   Enter a name for the Open vStorage Cluster.
-   Open vStorage will detect the available drives in the host and
    propose the best suited partitioning layout based upon the detected
    SSDs and SATA devices. You can override this by selecting option 2
    in the menu and update the proposed partition layout. The partitions
    used by Open vStorage are as follows:
    -   /var/tmp: temporary filesystem which is used during scrubbing
        (cleanup of out of retention data). This can be on SATA. This
        must be at least a good couple of GB.
    -   /mnt/bfs/: required only in case of a local backend (no vMotion
        will be possible). This should go to SATA. The more the better
        in this case as this is where VM data will end up. In case you
        use an object store this partition isn't used.
    -   /mnt/db: mountpoint for the databases.This should be on SSD and
        needs to be selected during installation.
    -   /mnt/md: mountpoint for the volume metadata. This should be on
        SSD.
    -   /mnt/cachex : mountpoints for the different caches (where X goes
        from 1 to as many SSDs in the server). This should be on SSD.
-   Some examples of the default partition layout:
    -   In case you have only 1 disk which also contains the OS, all
        partitions will be created as directories under the root file
        system.
    -   In case you have 1 additional SATA disk, we will use 20% for
        /var/tmp and the rest for the local backend (/mnt/bfs). In case
        you have 2 SATA disks we will assign each to a separate SATA.
    -   In case you have a single SSD, /mnt/db (25%), /mnt/md (25%) and
        the cache (50%) will be created on this SSD.
    -   In case you have 2 or more SSDs Open vStorage will put the
        databases (/mnt/db - 25%) on the first SSD and the metadata
        (/mnt/md - 25%) on the second SSD. The rest of these SSDs (75%)
        will be used to create caches. SSD 3 and up will be used
        completely to create cache partitions.
-   Select the Public IP address of the KVM Node.
-   Select KVM as hypervisor. In case VMware is used as hypervisor, use
    the [ESXi install documentation](/doc/ESXi%20Installation).
-   Select the public IP address of the Storage Router.
-   Select a mountpoint for the database. The default mountpoint is
    /mnt/db.
-   Enter the root password of the Storage Router to exchange the necessary SSH
    keys.

When the install is completed a message will be displayed and you can
start using Open vStorage.

~~~~ {.sourceCode .python}
++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++
+++ Setup complete. +++
+++ Point your browser to http://<IP of the Storage Router> to start using Open vStorage +++
++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++
~~~~

### Apply for a free license
**This step is only required in case you installed the Open Storage
Backend packages (openvstorage-hc). In case you only installed the Open vStorage core
packages you can skip to the [add vPool section]( {{< relref "KVM Installation.md" >}}#addvpool).**

Apply for a free community license in the [Administration section](/doc/Using the GUI#administration).  To apply for a license fill in the
form with your name, company name, email address, telephone number and
indicate that you accept the license agreement. You will receive a license key
by email on the provided email address. Click *Add license* to activate the license. A community license is limited to 4 hosts, 16 ASDs and 49 vDisks. [Contact us](contactus) in case you need a larger license.


### Create an Open vStorage Backend

**This step is only required in case you installed the Open Storage
Backend packages (openvstorage-hc). In case you only installed the Open vStorage core
packages you can skip to the [add vPool section]( {{< relref "KVM Installation.md" >}}#addvpool).**

-   Open the [Open vStorage GUI](/doc/Using%20the%20GUI) on the public IP of
    the Storage Router and enter with the default login and password:
    admin/admin.
-   Select from the menu *Backends* and click the *Add Backend* button.

![](images/addbackend.png)


-   Give the Backend a name and click *Finish*. Only the letters a-z,
    numbers and dashes (not as first or last character) are allowed. A
    minimum of 3 and a maximum of 50 characters has to be maintained.
-   Once the Backend is created in the model, the page will refresh and
    the Backend Details page will be featured.
-   By default all nodes with a Storage Router are shown. Additional
    nodes can be added by clicking the *Discover* button.

-   Select the Initialize all disks icon ( ![](images/heartbeat.png) ) of each ASD
    Node in the list. The initialization might take a while.

-   Once all disks are *Available* (dark blue), select the Claim all
    disks icon ( ![](images/bullseye.png) ) of each ASD Node.
-   You can now [create a vPool](#addvpool) which uses this Backend by
    selecting Open vStorage Backend as Backend Type.

-   Optionally you can create a new [preset](../../doc/backends#presets). Select the <i class="fa fa-flip-vertical fa-code-fork"></i>-icon on the Backend detail page to see the default policies or create a new one.


<a name="addvpool" class="internal-ref"></a>
### Create the vPool

-   In case the GUI isn't open yet, open the [Open vStorage
    GUI](/doc/Using%20the%20GUI) on the public IP of the Storage Router and
    enter with the default login and password: admin/admin.
-   Select from the menu *vPools* and click the *Add new vPool* button.

![](images/addnewvpool.png)


-   Enter a name for the vPool and select the type of Storage Backend:
    -   In case *Local FS* (File System) is selected, no additional info
        is required. Moving Virtual Machines between nodes will not be
        possible. Use this option in single node environments only.
    -   In case *S3 compatible* is selected you will need to provide an
        access key, a secret key and connection (url or IP) and port to
        access the S3 compatible Storage Backend.
    -   In case *Ceph S3* is selected you will need to provide an access
        key, a secret key and connection (url or IP) and port to access
        the Ceph Storage Backend.
    -   In case *SWIFT S3* is selected you will need to provide an
        access key, a secret key and connection (url or IP) and port to
        access the Swift Storage Backend.
    -   In case *Distributed FS* (distributed filesystem) is selected,
        no additional info is required. Select this option in case you
        want to use an external NFS share or a distributed file system.
        Please note that the file system or share must be mounted on
        each node.
    -   In case Open vStorage Backend is selected, you can select one of
        previously created local [Backends](/doc/backends). Leave the *Use
        local Open vStorage Backend* checked and press *Next* to load
        the available Backends. Use this option in case you want to run
        Open vStorage hyperconverged (using the SATA disk inside the
        Storage Router as Tier 2 storage). Select a Preset from  the dropdown. This Preset defines how data is stored on the backend (e.f. 3-way replication). You can add more Presets in the detail page of a [Backend](/doc/backends). **Once the vPool is created the Preset can't be changed.**
-   Select the Storage Router as Initial Storage Router and enter the
    root password of the Storage Router. Click *Next* to continue.

![](images/addnewvpool\_tab1.png)

-   On the second tab
    -   In case of a local backend or distributed FS, select a
        mountpoint for the backend filesystem to store the data.
    -   Select a filesystem which will be used during scrubbing.
    -   Select a mountpoint for the metadata.
    -   Select a mountpoint for the different caches (or press enter to
        select the default).
    -   Select the Storage IP and vRouter port (the next 2 ports will
        also be used) to use.

![](images/addnewvpool\_tab2.png)

-   On the third tab
    -   In case you have a [Hypervisor Management Center](../../doc/Using%20the%20GUI#hmc) (OpenStack) configured, you can automatically configure the vPool on the hypervisor. Select the checkbox to automatically configure Cinder on the Nova host.
    -   In case you don't have a Hypervisor Management Center configured, you will not be able to check the checkbox. Some manual actions might have to be taken to correctly configure the KVM host to use the vPool.

Additional vPools can be added to the Storage Router by executing the
same steps again.

### Add more nodes to the Open vStorage Cluster

Once the Open vStorage software is installed on the first node, you can
add more nodes to the cluster. This requires [KVM to be
installed]]( {{< relref "KVM Installation.md" >}}#installkvm ) and [configured]( {{< relref "KVM Installation.md" >}}#configureubuntu ) and the [Open
vStorage software must be installed]( {{< relref "KVM Installation.md" >}}#installovs )  on these nodes.

Execute in the KVM shell of every node (concurrent installations of multiple nodes isn't supported):

~~~~ {.sourceCode .python}
ovs setup
~~~~

The initialization script will ask a couple of questions:

-   Enter the root credentials for the KVM Node.
-   It will search for existing Open vStorage Clusters in the network.
    Select the Cluster created earlier.
-   By default Open vStorage will suggest a partition layout based upon
    the detected disks. You can override this by selecting option 2 in
    the menu and update the proposed partition layout.
-   Select the Public IP address of the KVM Node.
-   Select KVM as hypervisor. In case VMware is used as hypervisor, use
    the [ESXi install documentation](/doc/ESXi%20Installation).
-   Select the public IP address of the Storage Router.
-   Select a mountpoint for the database. The default mountpoint is
    /mnt/db.
-   Enter the root password of the Storage Router to exchange the
    necessary SSH keys.

When the install is completed a message will be displayed and you can
now [extend the vPool]( {{< relref "KVM Installation.md" >}}#extendvpool ) to this Storage Router.

~~~~ {.sourceCode .python}
+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++
+++ Setup complete. +++
+++ Point your browser to http://<IP of the KVM> to start using Open vStorage +++
+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++
~~~~

Repeat the above commands for all Storage Routers you want to add to the
Open vStorage Cluster.

 <a name="extendvpool" class="internal-ref"></a>
### Extending a vPool across multiple nodes

-   Extend the vPool from the first node to additional nodes by going to
    the vPool detail page and selecting the nodes where you want the
    vPool to be available. Adding the vPool to another Storage Router
    will use the exact same settings for the temporary filesystem, the
    metadata, the cache and the Storage IP as on the first one.

### Final remarks

Visit the [Open vStorage GUI](/doc/Using%20the%20GUI) by navigating with an
HTML5 compatible browser to the IP address of the node to see the
storage performance statistics. *Also, keep in mind only .raw disks are
supported on KVM with Open vStorage.*
