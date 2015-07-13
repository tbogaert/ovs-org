### vDisks


### Introduction

A vDisks is a virtual disk served by Open vStorage. vDisks can be part
of a vMachine or stand-alone. By default all vDisks are snapshotted once
per hour. This snapshot schedule can not be changed for the moment.

### vDisk Overview

The vDisk overview lists all the vDisks in the Open vStorage Cluster.

![](images/vdisk\_overview.png)


For each vDisk following info is displayed:

-   Name: Name of the vDisk. You can click on the name of a vDisk to see
    [more details]( {{< relref "vdisks.md" >}}#vdisk_details ) and execute actions.
-   vMachine: The vMachine to which the vDisk is attached.
-   vPool: The vPool the vDisks is stored on.
-   VSA: The Virtual Storage Appliance (VSA) to which the vDisk connects
    to access its storage.
-   Size: Size of the vDisk.
-   Stored Data: Total size of the current data and the Snapshots
    without the overhead imposed by the Backend redundancy.
-   Cache : The current amount of Cache Hits on the vPool by the vDisk.
-   IOPS: The current amount of IOPS delivered by the vPool to the
    vDisk.
-   Read: The current read speed of the vDisk.
-   Write: The current write speed of the vDisk.
-   FOC: Status of the FailOver Cache, a copy of the write cache of the
    vDisk, on another GSR to prevent dataloss. The status can be healthy
    of degraded.

<a name="vdisk_details" class="internal-ref"></a>
### vDisk Details

The vDisk Details page displays the detailed performance statistics of a
single vDisk.

Following information about the vMachine is displayed on the detail
page:

-   vMachine: The vMachine to which the vDisk is attached.
-   vPool: The vPool the vDisks is stored on.
-   GSR: The Grid Storage Router (GSR) to which the vDisk connects to
    access its storage.
-   Size: Size of the vDisk.
-   Stored Data: Total size of the current data and the Snapshots
    without the overhead imposed by the Backend redundancy.
-   FOC: Status of the FailOver Cache, a copy of the write cache of the
    vDisk, on another GSR to prevent dataloss. The status can be healthy
    of degraded.
-   \# Snapshots: The amount of snapshots stored of the vDisks.
-   Cache : The current amount of Cache Hits on the vPool by the vDisk.
-   IOPS: The current amount of IOPS delivered by the vPool to the
    vDisk.
-   Read: The current read speed of the vDisk.
-   Write: The current write speed of the vDisk.

### vDisk Actions

#### Rollback

Rollback the vDisk and to a previous
snapshot. This action can only be executed on stopped vMachines and the
rollback can not be undone.

### Snapshots

At the bottom of the vDisk Details page all snapshots of the vDisk are
listed.