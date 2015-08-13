+++
#Don't remove title!
title = "ESXi Installation"
+++

### Introduction

This section provides a detailed step-by-step guide on how to setup Open
vStorage on 3 or more Nodes. The guide requires you to have at least 3
freshly installed ESXi servers. At the end of the guide you will be
ready to deploy a Virtual Machine on the Open vStorage Cluster. When you
encounter issues or are stuck somewhere, do not hesitate to ask for help
in the public [Open vStorage
Forum](https://groups.google.com/forum/#!forum/open-vstorage).

### What do you need to install Open vStorage on multiple nodes?


-   vMotion will only be possible in case a central Storage Backend
    (Ceph, Swift, ...) or a distributed file system is selected for the
    actual data of the vMachines. vMotion on a vPool with local disk
    will not be possible.
-   At least 3 VMware ESXi compliant servers with at least 3 disks and
    install media for ESXi. For performance reasons at least 1 SSD or
    PCI-flash card is required.
-   [Ubuntu server 14.04.2 64 bit
    ISO](http://releases.ubuntu.com/14.04.2/ubuntu-14.04.2-server-amd64.iso).
-   A Bitbucket account. You can create a [Bitbucket account for
    free](https://bitbucket.org/).
-   The
    [deployOvs.py](http://download.openvstorage.com/index.php?file=deployOvs)
    script (browse to openvstorage / scripts / deployment /) from
    [Bitbucket](https://bitbucket.org/openvstorage/openvstorage/).
-   In case you want to install Open vStorage Hyper-converged, at least 3 additional SATA disks are required for the Open vStorage Backend.
-   Per GB read cache on SSD/PCI-e flash, you will need to reserve 10 MB RAM in the Open vStorage Storage Router.

### Prerequisites before starting this guide: 

-   Enable all C-states in the BIOS of the Hosts for optimal
    performance.
-   The ESXi 5.1 P01/5.5 OS is installed (preferably on 2 disks in RAID
    1). Each Host has an IP address in the Public network and SSH is
    enabled.
-   The ESXi Hosts need to have an SSD/Flash disk (min. 100GB).
-   Ensure that the ESXi Hosts have at least 50GB of free space on the
    Datastore.
-   Ensure no remainder of previous Open vStorage installs are left
    behind (e.g. previous vMachines, Storage Routers or vPools) on the
    Hosts.
-   The [Ubuntu server 14.04.2 64 bit
    ISO](http://releases.ubuntu.com/14.04.2/ubuntu-14.04.2-server-amd64.iso)
    and the
    [deployOvs.py](http://download.openvstorage.com/index.php?file=deployOvs)
    script are uploaded to the ESXi Datastore of each Host.
-   VMware vSphere is required for High Availability (HA).

### Prepare the Network on the ESXi Hosts for Open vStorage

Open vStorage requires 2 networks to be available:

-   Public: the public side, serving the GUI and API.
-   Storage: connecting the Grid Storage Router and the ESXi Host.

Below you can find an example network configuration. Adjust it to your
situation.

-   Open the vSphere Client, connect to the ESXi Host and provide
    credentials when prompted. Select the *Configuration* tab.

![](images/configurationtab.png)

-   Select *Networking* from the hardware section.

![](images/networking.png)

-   Select *Properties...* of vSwitch0.

![](images/properties.png)

-   Select VM Network and select *Edit...*

![](images/vmnetwork.png)

-   Set the Network Label to Public. Click *OK* to confirm.

![](images/vmnetworkproperties.png)

-   Click *Close* to save the changes to vSwitch0.

![](images/vswitch0.png)

-   Create a second Virtual Switch. Select *Add Networking...*.

![](images/addnetworking.png)

-   Select *Virtual Machine* as Connection Type and click *Next \>*.

![](images/connectiontype.png)

-   Unselect all NICs and click *Next \>*.

![](images/no\_nic.png)

-   Change the Network Label to *Storage* and click *Next \>*.

![](images/privateswitch.png)

-   Click *Finish* to create the Private Switch.

![](images/vswitch1.png)

-   Select *Properties...* of vSwitch1 and select *Add*.

![](images/nic\_add.png)

-   Select *VMkernel* as Connection Type and click *Next \>*.
-   Leave everything to default and click *Next \>*.

![](images/connection\_settings.png)

-   Select an IP address for the ESXi Host and define the Subnet Mask.
    Click *Next \>*.

![](images/second\_ip.png)

-   Click *Finish*.

![](images/second\_nic\_finish.png)

-   After the above steps, the networking should look like below.

![](images/vswitches.png)

Repeat the above steps for all ESXi Nodes in the Open vStorage Cluster.
Additional networks are advised in case you want to enable the HA
features of VMware. Please refer to the VMware best practices

### Create the Open vStorage Storage Router


On each ESXi Host create an Open vStorage Storage Router. You can use
the [DeployOvs.py]({{< relref "ESXi Installation.md" >}}#usedeployscript) script (download it
[here](http://download.openvstorage.com/index.php?file=deployOvs)) to
create the Virtual Storage Router or you can create the Open vStorage VM
[manually]({{< relref "ESXi Installation.md" >}}#rooter-manually).

<a name="usedeployscript" class="internal-ref"></a>
### Use the Open vStorage deploy script

The deploy script will create a Virtual Machine with 3 disks and
configure it as Open vStorage Storage Router. The first disk (OS) will
be stored as VMDK on the default Datastore, the second disk (SSD or PCI
Flash Card) is used for the database and the caches and the third disk
(SATA disk) can be used as local filesystem storage. In case no SATA
disks are found or if you use -s as parameter, the script will skip the
creation of the 3rd disk.

-   Login with Putty into the ESXi SSH Shell and type

~~~~ {.sourceCode .python}
cd /vmfs/volumes/datastore1/
chmod +x deployOvs.py
./deployOvs.py --image /vmfs/volumes/datastore1/ubuntu-14.04.2-server-amd64.iso
~~~~

-   Select the Public network and the Storage network.
-   Select the amount of disk space to reserve for the Open vStorage
    Storage Router. Minimum required space on the Datastore is 50 GB.
-   If more than 1 SSD disk or PCI Flash Card is found, select one to
    (raw device) map to the Storage Router.
-   If more than 1 SATA disk is found, select one to (raw device) map to
    the Storage Router.
-   You can now go to the [Install Ubuntu]({{< relref "ESXi Installation.md" >}}#installubuntu) section.

Repeat the above steps for all ESXi Nodes in the Open vStorage Cluster.
<a name="rooter-manually" class="internal-ref"></a>
### Manually create the Open vStorage Storage Router

-   Open the vSphere Client and provide credentials when prompted.
-   Create a VM with a *Custom* configuration.
    -   Enter a name for the Storage Router and click *Next*.
    -   Select Datastore1 and click *Next*.
    -   Select virtual Machine Version: 8 and click *Next*.
    -   Select Linux and the appropriate distro (Ubuntu-64 bit) from the
        dropdown list and click *Next*.
    -   Select 1 virtual socket, 4 cores per virtual socket and click
        *Next*.
    -   Select 16GB RAM and click *Next*.
    -   Select 2 NICs, VMXNET3 as adapter and add one to each Network
        (Public and Storage). Click *Next*.
    -   Select as SCSI controller LSI Logic SAS and click *Next*.
    -   Select *Create a new virtual disk*, disk size 400GB and leave
        the rest to default. Click *Next*.
    -   Leave the rest to default and click *Next*.
    -   Tick the "Edit the virtual machine before completion"-box and
        select *Finish*.
-   Map the SATA disk and SSD to the Storage Router
    -   Login with Putty into the ESXi SSH Shell
    -   Execute *esxcli storage vmfs extent list*. It returns the Device
        Name used for the Datastore

~~~~ {.sourceCode .python}
Volume Name  VMFS UUID                            Extent Number  Device Name                                                               Partition

-----------  -----------------------------------  -------------  ------------------------------------------------------------------------  ---------

datastore1   52b06671-2a98eaec-2fe8-5404a68a0311              0  t10.ATA_____INTEL_SSDSA2CW120G3_____________________BTPR141501EM120LGN__          3


* List the disks in the ESXi host
~~~~

~~~~ {.sourceCode .python}
cd /vmfs/devices/disks/
/dev/disks # ls -al


* Map the SATA disk(s) and SSD (which are not part of the Datastore) to the ESXi Host so you can use them in the Storage Router.
~~~~

~~~~ {.sourceCode .python}
vmkfstools -z /vmfs/devices/disks/vml.<id of the SATA disk> /vmfs/volumes/datastore1/<name of the GSR>/hdd1.vmdk
vmkfstools -z /vmfs/devices/disks/vml.<id of the SSD disk> /vmfs/volumes/datastore1/<name of the GSR>/ssd1.vmdk


For example:
vmkfstools -z /vmfs/devices/disks/vml.0100000000202020202057442d5743415a4144303837393733574443205744 /vmfs/volumes/datastore1/ovs/hdd1.vmdk
vmkfstools -z /vmfs/devices/disks/vml.0100000000425450523134313530304c313132304c474e2020494e54454c20 /vmfs/volumes/datastore1/ovs/ssd1.vmdk
~~~~

-   For performance reasons, configure the power management parameters
    of the ESXi Host.

~~~~ {.sourceCode .python}
esxcli system settings advanced set -o /Power/UsePStates --int-value=0
esxcli system settings advanced set -o /Power/UseCStates --int-value=1
~~~~

-   In the vSphere Client
    -   Select the Open vStorage Storage Router and select *Edit
        Settings*.
    -   Add a new Hard disk by selecting *Add ...*, *Hard Disk*, *Use an
        existing virtual disk* and select the SSD disk or PCI Flash Card
        (ssd1.vmdk) from the Datastore1/\<name of the Storage Router\>.
    -   Add a new Hard disk by selecting *Add ...*, *Hard Disk*, *Use an
        existing virtual disk* and select the SATA disk (hdd1.vmdk) from
        the Datastore1/\<name of the Storage Router\>. Do the same for
        the other HDD disks.
    -   Select the CD/DVD drive and select the Ubuntu ISO from the
        Datastore as Device Type. Don't forget to check the *Connect at
        power on* checkbox.
    -   Select the Options tab, select Boot Options and tick the *Force
        BIOS setup*-checkbox.
    -   Click *OK* to save the changes.
-   You can now go to the [Install Ubuntu]({{< relref "ESXi Installation.md" >}}#installubuntu) section.

Repeat the above steps for all ESXi Nodes in the Open vStorage Cluster.

<a name="installubuntu" class="internal-ref"></a>
### Install Ubuntu on each Storage Router in the Open vStorage Cluster

-   Boot the Open vStorage Storage Router.
-   The Open vStorage Virtual Machine will boot into the BIOS Setup
    Utility. In the BIOS settings, go to the Boot tab.

![](images/boottab.png)
1px solid black!

-   Select the *CD-ROM Drive* by pressing the *down key* twice. Move the
    *CD-ROM Drive* to the first position by pressing *+* twice.

![](images/boottabcdrom.png)

-   Press *F10* to save and exit the Boot Utility. Select *Yes* to save
    the changes.

![](images/boottabsave.png)

-   Next install Ubuntu, select your language and select *Install Ubuntu
    Server*.
-   Confirm your language, select your location and configure your
    keyboard.
-   Configure the network of the Storage Router. Select the NIC which is
    connected to the Public port group. (In case you used the deploy
    script, select eth1.) If unsure, right-click the VM, click *Edit
    Settings* and verify which NIC is connected to the Public port
    group. Enter the IP address, netmask, gateway and DNS servers. The
    GUI will be made available on the set IP address.
-   Set a hostname, create a new user (do not use ovs as username) and
    enter a password. There is no need to encrypt the home directory.
-   Select your timezone.
-   Partition the disks by selecting *Manual*.

![](images/select\_manual.png)

-   Clear all disks of the Storage Router (no partitions on any of the
    disks). The result should be as the screenshot below.

![](images/sreenshot\_of\_disks.png)

-   Select *Guided partitioning*. Select *Guided - use entire disk*.

![](images/guided\_user\_entire\_disk.png)

-   Select sda (marked with *VMware Virtual disk*) as disk to partition.

![](images/partition\_select\_sda.png)

-   Write the changes to the disk.

![](images/write\_changes\_to\_disk.png)

-   Leave the HTTP proxy information blank and select *No automatic
    updates*.
-   Install the Open SSH server.
-   Install the GRUB boot loader. Select *No* when prompted to install
    it to the master boot record.

![](images/bootloader\_select\_no.png)

-   Now you can explicitly specify /dev/sda as location for the GRUB
    boot loader. Otherwise it will try to install the GRUB boot loader
    on /dev/sdb, which will result in an error.

![](images/bootloader\_dev\_sda.png)

-   Finish the Ubuntu installation by selecting *\<Continue\>*.

![](images/finish\_install.png)

-   The Open vStorage Storage Router will boot from the installed Ubuntu
    OS.
-   In the vSphere GUI right-click the Open vStorage Storage Router,
    select *CD/DVD drive* and uncheck the *Connect at power on*
    checkbox.

Repeat the above steps for all ESXi Nodes in the Open vStorage Cluster.


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

-   Add the same Storage IP address to the second NIC of each Storage
    Router. The IP address needs to be in the Storage network. To set
    the IP edit /etc/network/interfaces (adjust the IP and interface
    according to your setup).

~~~~ {.sourceCode .python}
auto eth0
iface eth0 inet static
        address 172.22.1.100
        netmask 255.255.255.0
        network 172.22.1.0
        broadcast 172.22.1.255
~~~~

-   Restart the network
-   You can now go to the [Install the Open vStorage
    software]({{< relref "ESXi Installation.md" >}}#installopenvstorage) section.

Repeat the above steps for all Storage Routers in the Open vStorage
Cluster.

<a name="installopenvstorage" class="internal-ref"></a>
Install the Open vStorage software on all the Storage Routers
-------------------------------------------------------------

The Open vStorage software first needs to be installed on all Storage
Routers. Execute the next steps in the shell of all Storage Routers:

-   Add the repo to your sources

~~~~ {.sourceCode .python}
echo "deb http://apt-ovs.cloudfounders.com beta/" > /etc/apt/sources.list.d/ovsaptrepo.list
~~~~

There are 2 options to install Open vstorage. The first option is to
install Open vStorage and the Open vStorage Backend packages. When you
install the Open vStorage Backend packages you can use the disks inside
the Nova host as Tier 2 storage (hyperconverged). You will need a
license to enable these packages. There is free license with
restrictions available which can be requested from the License section
under [Administration](/doc/Using%20the%20GUI/#administration). The second
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
apt-get update; apt-get install openvstorage
~~~~

### Initialize the first Storage Router

-   You are now ready to initialize the first Storage Router. Execute in
    the Storage Router shell:

~~~~ {.sourceCode .python}
ovs setup
~~~~

The initialization script will ask a couple of questions:

-   Enter the root credentials for the Storage Router.
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
-   Select the Public IP address of the Storage Router.
-   Select VMware as hypervisor. In case KVM is used as hypervisor, use
    the [KVM install documentation](/doc/KVM Installation).
-   Enter the hostname of the host on which the Storage Router is
    running, the IP address and provide login and password.
-   Select the public IP address of the Storage Router.
-   Select a mountpoint for the database. The default mountpoint is
    /mnt/db.
-   Enter the root password of the Storage Router to exchange the
    necessary SSH keys.

When the install is completed a message will be displayed and you can
you can start using Open vStorage.

~~~~ {.sourceCode .python}
++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++
+++ Setup complete. +++
+++ Point your browser to http://<IP of the Storage Router> to start using Open vStorage +++
++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++
~~~~

### Apply for a free license
**This step is only required in case you installed the Open Storage
Backend packages (openvstorage-hc). In case you only installed the Open vStorage core
packages you can skip to the [add vPool section]]( {{< relref "ESXi Installation.md" >}}#addvpop ).**

Apply for a free community license in the [Administration section](../../doc/Using%20the%20GUI#administration).  To apply for a license fill in the
form with your name, company name, email address, telephone number and
indicate that you accept the license agreement. You will receive a license key
by email on the provided email address. Click *Add license* to activate the license. A community license is limited to 4 hosts, 16 ASDs and 49 vDisks. [Contact us](contactus) in case you need a larger license.

### Create an Open vStorage Backend

**This step is only required in case you installed the Open Storage
Backend packages. In case you only installed the Open vStorage core
packages you can skip to the [add vPool section]( {{< relref "ESXi Installation.md" >}}#addvpool ) ..**

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
-   Select the Initialize all disks icon (heartbeat.png) of each ASD Node in the list. The initialization might take a while.
-   Once all disks are Available (dark blue), select the Claim all disks icon (bullseye.png) of each ASD Node.
-   Optionally you can create a new [preset](../../doc/backends#presets). Select the <i class="fa fa-flip-vertical fa-code-fork"></i>-icon on the Backend detail page to see the default policies or create a new one.
-   You can now [create a vPool]( {{< relref "ESXi Installation.md" >}}#addvpool ) which uses this Backend by
    selecting Open vStorage Backend as Backend Type.

<a name="addvpool" class="internal-ref"></a>
### Create the vPool

-   In case the GUI isn't open yet, open the [Open vStorage
    GUI](/doc/Using%20the%20GUI) on the public IP of the Storage Router and
    enter with the default login and password: admin/admin.
-   Select from the menu *vPools* and click the *Add new vPool* button.

![](images/addnewvpool.png)


-   Enter a name for the vPool and select the type of Storage Backend:
    -   In case *Local FS* (File System) is selected, no additional info
        is required. vMotion between nodes will not be possible. Use
        this option in single node environments only.
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
        Storage Router as Tier 2 storage). Select a Preset from  the dropdown. This Preset defines how data is stored on the backend (e.f. 3-war replication). You can add more Presets in the detail page of a [Backend](/doc/backends). **Once the vPool is created the Preset can't be changed.**
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
    -   In case you have a [Hypervisor Management Center](../../doc/Using%20the%20GUI#hmc) (vCenter) configured, you can automatically configure the vPool on the hypervisor. 
    -   In case you don't have a Hypervisor Management Center configured, you will not be able to check the checkbox. Some manual actions might have to be taken to correctly configure the ESXi host to use the vPool.

The current Open vStorage release only supports one vPool.

### Add the vPool as Datastore

-   Open the vSphere Client of the ESXi hosting the first Storage Router
    and provide credentials when prompted. Select the *Configuration*
    tab.
-   Select *Storage* from the Hardware section.
-   Select *Add Storage ...*
-   Select *Network File System* as Storage Type and click *Next*
-   Enter the shared folder Properties
    -   Server: the *Storage* IP address of the Storage Router
    -   Folder: /mnt/name of the vPool
    -   Datastore Name: name of the vPool
-   Click *Next* and *Finish*
-   Once the vPool is mounted, you can use it as Datastore for
    [vMachines created in the vSphere GUI]( {{<relref "ESXi Installation.md" >}}#create_vmachine ).

### Add more Storage Routers to the Open vStorage Cluster

Once the Open vStorage software is installed on the Storage Router of
the first Host, you can add more Storage Routers and additional ESXi
Hosts. This requires an [OS to be installed]({{< relref "ESXi Installation.md" >}}#installubuntu) and
[configured]( {{< relref "ESXi Installation.md" >}}#configureubuntu) and the [Open vStorage software must be
installed]({{< relref "ESXi Installation.md" >}}#installopenvstorage) on these Storage Routers.

Execute in the shell of every Storage Router (concurrent installations of multiple nodes isn't supported):

~~~~ {.sourceCode .python}
ovs setup
~~~~

The initialization script will ask a couple of questions:

-   Enter the root credentials for the Storage Router.
-   It will search for existing Open vStorage Clusters in the network.
    Select the Cluster created earlier.
-   Select if you want to use a local disk for Backend Storage.
-   Select an SSD as cache device.
-   Select the Public IP address of the Storage Router.
-   Select VMware as hypervisor. In case KVM is used as hypervisor, use
    the [KVM install documentation](/doc/KVM%20Installation).
-   Enter the hostname of the host on which the Storage Router is
    running, the IP address and provide login and password.
-   Select the public IP address of the Storage Router.
-   Select a mountpoint for the database. The default mountpoint is
    /mnt/db.
-   Enter the root password of the Storage Router to exchange the
    necessary SSH keys.

When the install is completed a message will be displayed and you can
now [add the first vPool]( {{< relref "ESXi Installation.md" >}}#addvpool ) to this Storage Router.

~~~~ {.sourceCode .python}
+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++
+++ Setup complete. +++
+++ Point your browser to http://<IP of the Storage Router> to start using Open vStorage +++
+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++
~~~~

Repeat the above commands for all Storage Routers you want to add to the
Open vStorage Cluster.

### Extending a vPool across multiple Storage Routers

-   Extend the vPool from the first Storage Router to additional Storage
    Routers by going to the vPool detail page and selecting the Storage
    Routers where you want the vPool to be available. Adding the vPool
    to another Storage Router will use the exact same settings for the
    temporary filesystem, the metadata, the cache and the Storage IP as
    on the first one.
-   Next open the vSphere Client on each ESXi Host and provide
    credentials when prompted. Select the *Configuration* tab.
-   Select *Storage* from the Hardware section.
-   Select *Add Storage ...*
-   Select *Network File System* as Storage Type and click *Next*
-   Enter the shared folder Properties
    -   Server: the *Storage* IP address of the Storage Router
    -   Folder: /mnt/name of the vPool
    -   Datastore Name: name of the vPool
-   Click *Next* and *Finish*
-   Once the vPool is mounted, you can use it as Datastore for
    [vMachines created in the vSphere GUI]( {{< relref "ESXi Installation.md" >}}#create_vmachine ).


<a name="create_vmachine" class="internal-ref"></a>
### Create a vMachine

Once a vPool is initialized and mounted to the ESXi, you can use it as
Open vStorage datastore for vMachines.

-   Open the vSphere Client.
-   Create a virtual machine following the standard virtual machine
    creation wizard.
-   On the storage tab, select the mounted vPool as datastore.

![](images/select\_vpool.png)

-   Continue the virtual machine creation wizard and click *Finish*.

### Add vCenter and enable HA

-   In the Open vStorage GUI, open the Administration section and select
    Hypervisor Mgmt.
-   Click *Add new Center*, provide a friendly name, select vCenter and
    provide an administrator username, password and IP address.
-   In the Managed Hosts section, indicate all Hosts which are managed
    by the vCenter instance. This will allow these Hosts to be protected
    by HA in case this is activated in vCenter.

### Final remarks

Visit the [Open vStorage GUI](/doc/Using%20the%20GUI) by navigating with an
HTML5 compatible browser to the IP address of the Open vStorage Storage
Router to see the storage performance statistics.
