### vPools

### Introduction

A vPool is a Virtual Storage Pool, a Filesystem, used to deploy
vMachines. A vPool can span multiple VSAs and connects to a single
Storage Backend. The vPools overview lists all the vPools in the Open
vStorage Cluster. Note that a vPool can not be added or updated through
the Open vStorage GUI for the moment.

### vPool Overview

The vPool overview lists all the vPools in the Open vStorage Cluster.

![](images/vpool\_overview.png)


For each vPool following info is displayed:

-   Name : Name of the vPool. You can click on the name of a vPool to
    see [more details]( {{< relref "vpools.md" >}}#details ) and execute actions.
-   Stored Data: Total size of the current data and the Snapshots
    without the overhead imposed by the Backend redundancy.
-   Cache : The current amount of Cache Hits on the vPool.
-   IOPS: The current amount of IOPS delivered by the vPool to all
    vDisks.
-   Type: Type of the Storage Backend. Possible values are Local FS
    (Local File Sytem), S3 compatible, Ceph S3, ...
-   Connection: The Connection (IP, URL, Domainname, Zone, ...) for the
    Storage Backend.
-   Login: Login/Username used to connect to the Storage Backend.

<a name="details" class="internal-ref"></a>

### vPool Details

The vPool Details page displays the detailed performance statistics of a
single vPool. There are 2 types of statistics: Frontend and Backend
statistics. The Frontend statistics are the performance metrics as seen
by the vDisks. The Backend statistics are informative. A write to the
Storage Backend only happens when data is evicted from the write cache
and a read only happens when the data is not in the cache of the vPool.
Low values on Backend performance are not necessary giving low
performance values, as seen by vDisks, on the Frontend as all data might
reside in cache. A lot of cache misses and low values for the Backend
performance on the other hand will result in a slow vDisk.

The Frontend details for a vPool are:

-   Stored Data: Total size of the current data and the Snapshots
    without the overhead imposed by the Backend redundancy.
-   Cache : The current amount of Cache Hits on the vPool.
-   IOPS: The current amount of IOPS delivered by the vPool to all
    vDisks.
-   Read Speed: The current read speed for all vDisks on the vPool
    together.
-   Write Speed: The current write speed for all vDisks on the vPool
    together.
-   vMachines: The amount of vDisks on the vMachines.
-   vDisks: The amount of vDisks on the vPool.

The Backend details for a vPool are

-   Type: Type of the Storage Backend.
-   Connection: The Connection (IP, URL, Domainname, Zone, ...) for the
    Storage Backend.
-   Login: Login/Username used to connect to the Storage Backend.
-   Read Speed: The current read speed from the Storage Backend.
-   Write Speed: The current write speed to the Storage Backend.

The Configuration details for a vPool are

-   Cache Strategy: Cache on Read or Cache on Write
-   Deduped mode: Deduped or Non-Deduped. When set to deduped all vDisks will be using a deduped read cache per Storage Router. On the individual vDisk page you can change the setting so the volumes becomes non-deduped and has its own cache. When the vPool setting is set to Non-Deduped all vDisks will have their own cache location. In this case it will not be possible to overrule this setting on the vDisk detail page.
-   SCO Size: The size of the Storage Conatiner Objects (a collection of writes) which gets stored on the Backend.
-   Write Buffer: The amount of data that can be in the DTL but not available in the Backend.
-   DTL mode: The way new writes are stored in the Distributed Transaction Log (DTL). The DTL is making sure that you don't have data loss when a host goes down. The DTL of a volume is configured on another host as where the volume is running and contains the outstanding writes (writes which are not yet on the backend). Every write to the write buffer (local SSDs) of a volume also gets transferred to the DTL on another host. This transfer can be disabled (no DTL), synchronously or asynchronously.
-   DTL Transport: Displays whether the vPool is using RDMA or standard TCP to transfer the DTL data.

These setting will be by default applied to all vDisks. The settings can be changed on the individual vDisk Detail pages.

### vPool Actions

#### Add a vPool
**Before you can add a vPool to a Storage Router make sure you have configured the physical disks of the Storage Router.**
 To add a new vPool to the Open vStorage Cluster execute following steps.

![](images/addnewvpool1.png)


-   Enter a name for the vPool and select the type of Storage Backend:
    -   In case S3 compatible is selected you will need to provide an
        access key, a secret key and connection (url or IP) and port to
        access the Amazon S3 compatible Storage Backend.
    -   In case Ceph S3 is selected you will need to provide an access
        key, a secret key and connection (url or IP) and port to access
        the Ceph Storage Backend.
    -   In case SWIFT S3 is selected you will need to provide an access
        key, a secret key and connection (url or IP) and port to access
        the Swift Storage Backend.
    -   In case Distributed File System is selected, no additional info
        is required. Select this option in case you want to use a
        distributed file system as Tier 2 backend for the VM data. In
        this case VM migration between nodes which both serve the vPool
        will be possible.
    -   In case Open vStorage Backend is selected, you can select one of
        previously created local [Backends](backends). Leave the *Use
        local Open vStorage Backend* checked and press *Next* to load
        the available Backends. Use this option in case you want to run
        Open vStorage hyperconverged (using the SATA disk inside the
        Storage Router as Tier 2 storage).
-   Select the Storage Router as Initial Storage Router. Click *Next* to continue.

![](images/addnewvpool2.png)

-   On the second tab
    -   Specify the Read and write Cache size to be assigned for the vPool on the Storage Router.
    -   Select the Storage IP. Use 127.0.0.1 in case of KVM or the IP in Storage Network for ESXi.

![](images/addnewvpool3.png)

-   On the third tab
    -   Define the Distributed Transaction Log mode: Currently you can set the DTL to on or off.
    -   Select the default Caching method for vDisks.
    -   Select the default deduped/non-deduped policy for vDisks. In case the value is set to deduped, the value can be overwritten on the vDisk detail page.
    -   Select the SCO size (a collection of writes which gets stored on the Backend).
    -   Select the Write Buffer (the amount of data that can be in the DTL but not available in the Backend).

![](images/addnewvpool4.png)

-   On the fourth tab
    -   In case you have a [Hypervisor Management Center](../../doc/Using%20the%20GUI#hmc) (vCenter, OpenStack) configured, you can automatically configure the vPool on the hypervisor.
    -   In case you don't have a Hypervisor Management Center configured, you will not be able to check the checkbox. Some manual actions might have to be taken to correctly configure the host to use the vPool.

![](images/addnewvpool3.png)

-   On the fifth tab
    -   Validate the values and click *Finish* to complete.

#### Sync


Syncs the status in the Open vStorage model
with the actual status on the Hypervisor of all vMachines using the
vPool. It is highly unlikely that the stored model and the reality get
out of sync but it is possible due to f.e. when the task scheduler or
message queue crashes.

### vMachines

Under vMachines all vMachines served by the vPool are listed. To see
more details of a vMachine, click its name.

### vDisks

Under vDisks all vDisks served by the vPool are listed. To see more
details of a vDisk, click its name.

### vPool Administration

#### Storage Routers serving this vPool

All the Storage Routers which are currently serving this vPool are
indicated with a marked checkbox. In case the vPool isn't a local file
system it can be extended across multiple Storage Routers. To add a Storage Router select the
checkbox next to the Storage Router name. To remove the vPool from a Storage Router unselect
the checkbox next to the Storage Router name. Removing a vPool from being served by
a Storage Router is only possible in case there aren't any vDisks being served by
the vPool on that Storage Router. Press *Save changes* to confirm.