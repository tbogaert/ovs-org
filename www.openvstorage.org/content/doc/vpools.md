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
-   
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

### vPool Actions

#### Add a vPool

-   To add a new vPool to the Open vStorage Cluster

![](images/addnewvpool.png)


-   Enter a name for the vPool and select the type of Storage Backend:
    -   In case Local FS(File System) is selected, no additional info is
        required. Use this option only in case you want to setup a quick
        test environment on a single node, single disk backend and do
        not want to test VM migration between nodes.
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
-   Select the Storage Router as Initial Storage Router and enter the
    root password of the Storage Router. Click *Next* to continue.

![](images/addnewvpool\_tab1.png)

-   On the second tab
    -   Select a filesystem for the temporary filesystem to use during
        scrubbing. The default filesystem is /var/tmp.
    -   Select a mountpoint for the distributed filesystem with the
        virtual machine configuration data.
    -   Select a mountpoint for the metadata. The default mountpoint is
        /mnt/md.
    -   Select a mountpoint for the cache (or press enter to select the
        default). The default is /mnt/cache.
    -   Select the Storage IP to use.

![](images/addnewvpool\_tab1.png)

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
system it can be extended across multiple GSRs. To add a GSR select the
checkbox next to the GSR name. To remove the vPool from a GSR unselect
the checkbox next to the GSR name. Removing a vPool from being served by
a GSR is only possible in case there aren't any vDisks being served by
the vPool on that GSR. Press *Save changes* to confirm.