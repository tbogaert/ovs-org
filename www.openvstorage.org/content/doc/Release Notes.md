### Release Notes

### Introduction

In this section you can find the release information for each new Open
vStorage version. You can find the release date, the new functionality
and a list of bug fixes for each version. Open vStorage is released
under the [Apache License, Version
2.0](http://www.apache.org/licenses/LICENSE-2.0).

### Open vStorage 1.1.0

h4.Logging and log collecting We have added logging to Open vStorage.
The logs are also centrally gathered and stored in a distributed Search
Engine (Elasticsearch). The logs can be viewed, browsed, searched and
analyzed through Kibina, a very nice designed GUI.

### HA Support

Open vStorage does with this release not only support vMotion but also
the HA functionality of VMware. This means that in case an ESXi Host
dies and vCenter starts the VMs on another Host, the volumes will
automatically be migrated along.

### Open vStorage 1.0.3 Beta

### Remove a vPool through the GUI

In version 1.0.2 it was already possible to create a vPool and extend
the vPool across Hosts through the GUI. To make the whole vPool
management process complete and more user friendly, we have added the
option to remove a vPool from a Host. This is of course only possible in
case there are no vDisks being served from the vPool to a vMachine on
that Host.

### New version of Arakoon

Arakoon 1.7, the distributed key-value store that guarantees consistency
above anything else, integrated in Open vStorage had some major rework
done. This release also fixes bugs we encountered.

### Other

Some small improvements include adding an NTP server by default to the
Host and displaying the different used ports on the GSR detail page.

### Open vStorage 1.0.2 Beta

### KVM

Open vStorage supports KVM as Hypervisor.

### Swift Support

Open vStorage supports Swift (SAIO - Swift All In One, Swiftstack, ...)
as Storage Backend.

### Configuration of a vPool through the GUI

You can now add a new vPool through the GUI to a Grid Storage Router
(GSR). In case the vPool is a not a local filesystem, you can also
stretch it across multiple GSRs.

### Protection against moving a vDisk to a Grid Storage Router (GSR) where it can't be served

Each GSR can only serve only a limited amount of vDisks. In case a vDisk
is moved between GSRs, f.e. due to a vMotion, the process will first
check if there are enough resources available to serve the vDisk. In
case there are not enough free resources on the destination GSR, the
handover will be aborted.

### Open vStorage 1.0.1 Beta

### Scalable up to 50 GSRs

Up to Open vStorage GSRs can be grouped in a grid to form a scale-out
virtualized Storage Platform.

### Management GUI

Open vStorage comes with an intuitive HTML5 management GUI. The GUI can
be opened from each GSR and gets automatically redirected when a GSR
goes offline.

### VMware ESXi 5.1/5.5

Open vStorage supports VMware ESXi 5.1/5.5 as Hypervisor.

### High Availability features

-   VMware vMotion, the live migration of running Virtual Machines, is
    supported without any user interaction. In case an ESXi Host goes
    down, the Virtual Machines can be restarted on another ESXi Host. In
    this case a manual action is required to prevent split-brain
    scenarios.
-   To ensure consistency and availability, the metadata is stored in
    [Arakoon](http://arakoon.org/), a distributed key-value store that
    guarantees consistency above anything else.
-   Non-VM-volume data (f.e. vmx files and Virtual Machine log files) is
    replicated across all Hosts in the Open vStorage Cluster.

### VM Centric

Open vStorage offers VM Centric storage features such as:

-   Unlimited Virtual Machine snapshots
-   Rollback of a single Disk of a Virtual Machine
-   Quick Virtual Machine cloning through Templates

### Advanced Caching Mechanisms

Open vStorage has an advanced write-cache policy on SSD or PCI-based
flash with FailOver Cache on a second Host in the Open vStorage Cluster.
The read cache is deduplicated for all Virtual Machines served by the
GSR.

### Multiple Storage Backend

Open vStorage supports following Storage Backends:

-   Local filesystem (includes SAN and NAS)
-   S3 API-compatible storage (S3? Ceph, ...)

Open vStorage exposes each Storage Backend as a NFS filesystem which can
be mounted by ESXi as Datastore.

### Monitoring

Open vStorage comes with basic real-time storage monitoring. Included
metrics are IOPS, Cache Hit Ratio, read and write speed.

### Easy installation

Open vStorage can be easily installed and configured through the
JumpScale jpackage system and included install scripts.

### Known Issues

A list of Known Issues with can be found [here](Known%20Issues).