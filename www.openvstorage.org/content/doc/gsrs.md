### GSRs

### Introduction

A Grid Storage Router (GSR) is a Virtual Machine or Host running the
Open vStorage software. A single GSR can host multiple [vPools](/doc/vpools)
as datastore for vMachines. Each ESXi can only host a single GSR.

### GSR Overview

The GSR overview lists all the GSRs in the Open vStorage Cluster.

![](images/vsa\_overview.png)


For each GSR following info is displayed:

-   Status: status of the GSR. The status can be online (green) or
    offline (red). In case the status is set to online, the GSR will be
    able to serve vDisks.
-   Name: Name of the GSR. You can click on the name of a GSR to see
    [more details]( {{< relref "gsrs.md" >}}#details) and execute actions.
-   IP: IP address in the Public network of the GSR.
-   Host: Friendly name of the ESXi given during installation of the
    GSR. Hoover over the name to see the Public IP address of the Host.
-   Type: Type of Hypervisor.
-   vDisks: Amount of vDisks served by the GSR.
-   Stored Data: The total amount of Stored Data of all vDisks served by
    the GSR.
-   Cache: The current amount of Cache Hits (in percent) of all vDisks
    served by the GSR.
-   IOPS: The current amount of IOPS of all vDisks served by the GSR.
-   Read: The aggregated read speed of all vDisks served by the GSR.
-   Write: The aggregated write speed of all vDisks served by the GSR.

<a name="details" class="internal-ref"></a>
### GSR Details

The GSR Details page displays the detailed performance statistics of a
single GSR, the actions and allows to configure vPools.

The details for a GSR are:

-   IP: IP address in the Public network of the GSR.
-   Host: Friendly name of the ESXi given during installation of the
    GSR.
-   Host IP: The Public IP address of the Host.
-   Type: Type of Hypervisor.
-   vPools: Amount of vPools served by the GSR.
-   vMachines: Amount of vMachines served by the GSR.
-   vDisks: Amount of vDisks served by the GSR.
-   Stored Data: Total size of the current data and the Snapshots
    without the overhead imposed by the Backend redundancy.
-   Cache: The current amount of Cache Hits (in percent) of all vDisks
    served by the GSR.
-   IOPS: The current amount of IOPS of all vDisks served by the GSR.
-   Read: The aggregated read speed of all vDisks served by the GSR.
-   Write: The aggregated write speed of all vDisks served by the GSR.

### GSR Administration

#### vPools served by the GSR

See which vPools are served by the GSR. Currently it is not possible to
change through the GUI whether or not a vPools is served by a certain
GSR.

#### GSR logs

To see the logs of the GSR, click the *Open Kibana* link.