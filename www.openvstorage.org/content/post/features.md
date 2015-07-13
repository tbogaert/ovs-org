+++
#Don't remove title!
title = "Features"
+++

### Backend Flexibility
‘One Size Fits All’, an idea that used to live in the datacenter and storage world is long gone. Today, specific applications and functionalities demand specific storage. That is why Open vStorage is configured to work as a truly open solution and works with any storage backend. Open vStorage provides the flexibility of using an existing distributed file system, NAS and local object store. This allows for more flexibility and the ability to protect existing investments.

Open vStorage can be used with:

* HDD/JBOD

* Seagate Kinetic drives

* S3 compatible Object Storage such as Ceph, SwiftStack, …

* Distributed Filesystems such as GlusterFS, XtreemFS, …


By using Open vStorage as storage router to these different back-ends, any back-end immediately performs like local storage to your virtual machines. A single host can have multiple vPools and you can select per Virtual Machine which vPool to use. Open vStorage enables features such as advanced caching and unlimited snapshots in an uniform way across all back-ends. On top, with technology as VMware Storage Motion, you can even switch between completely different storage back-ends and archive no longer-used Virtual Machines on a slower or cheaper storage back-end.

### High Storage Performance
Open vStorage was designed from the ground up with performance in mind. Open vStorage runs as a Virtual Storage Appliance (VSA) on every ESXi and presents itself as an NFS datastore to the hypervisor. On KVM Open vStorage is runs bare-metal and is tightly coupled with the hypervisor for an extra performance boost. Software intelligence is used to create a unified namespace, whereby the hosts think they are talking to a fast, external shared storage system. It brings together the benefits of storage local to the server and external scalable storage by leveraging SSD and PCIe flash technology inside the server for acceleration. These blindingly-fast devices act as read and write caches which result in consistent low latency and high IOPS without expensive investments in proprietary Flash Arrays.

Open vStorage brings low latency and high IOPS and bandwidth to the table and also introduces Performance-As-You-Grow economics as each additional host provides a near linear performance increase. Our standard tests routinely clock performance of 50,000+ IOPS per host.

Open vStorage is designed for Virtual Machines and eliminates the I/O blender effect of multiple Virtual Machines reading and writing to the same back-end. The write caching works as a transaction log based cache on fast Flash or SSD redundant storage. This transaction log will immediately confirm the write to the hypervisor for fast response times and turns any random write I/O behavior into a sequential write operation which can be pushed at a slower pace to the back-end. To provide redundancy all writes are also mirrored to a second host.

### OpenStack Integration
Open vStorage can be seamlessly integrated with [OpenStack](http://www.openstack.org), the standard, open-source framework to build public clouds. This allows for example to use Swift, the Object Storage project in OpenStack, to be used as primary Virtual Machine storage. The Open vStorage functionality becomes available through a Cinder plugin.</p>
<iframe src="http://www.youtube.com/embed/IJFb9etugyk" width="560" height="315" frameborder="0" allowfullscreen="allowfullscreen"></iframe>

Some of the benefits you get by installing the Cinder Plugin are:

* Use the native Swift Object Store as primary storage for your Virtual Machines.

* A huge performance increase for traditional storage solutions due to aggressive read and write caching on SSD or PCIe Flash inside the host.

* Integrate storage backends which don't have a Cinder plugin into OpenStack.

### Hypervisor Agnostic
Multi-hypervisor environments are becoming more and more common practice. Mixing hypervisors within one and the same company environment or datacenter is almost necessary as server virtualization and i.e. VDI implementation have quite different requirements for its hypervisors. CloudFounders, the company behind Open vStorage, clearly understands these differences and has designed Open vStorage to work with multiple hypervisors allowing you to work with one Storage approach in multiple environments.

Currently Open vStorage presents itself standard as NFSv3 interface (CIFS to be added at a later date) to hosts. The first version of Open vStorage works on KVM (raw disks) and VMware (5.1 and 5.5), with Xen and Hyper-V support being added shortly.

### Asymmetric Scale Out Architecture
Dual Controller based systems need pre-sizing and do not offer scale out capabilities. Open vStorage on the other hand allows for asymmetric scale out. This allows administrators to scale out hypervisor and storage capacity independently with standard x86-hardware.

Open vStorage has taken a modular approach in its architecture. Additional hosts can easily and within minutes be added to an existing cluster. Additional storage back-ends can with few simple mouse clicks be made available to all hosts in the Open vStorage cluster. This allows to seamlessly scale from a couple of TB on local storage and tap into the almost unlimited storage pool offered by Object Storage.

Open vStorage also solves the scale-out issues of traditional SAN and NAS systems. The systems have limited capabilities in delivering IOPS and bandwidth and can over time become a true bottleneck. Solving this bottleneck requires expensive fork-lift upgrades or an investment in expensive storage arrays. Performance in an Open vStorage Cluster is truly scale-out as each additional host will introduce a predictable, linear performance boost.

### Software Redundancy
Traditionally storage redundancy and fault tolerance are implemented via dual controller hardware systems. Open vStorage uses intelligent software based techniques to provide the same level of reliability at much lower costs and much better scaling.

### VM Centric
Unlike traditional storage where all Virtual Machines are running on the same LUN, Open vStorage is VM centric and allows administrators to manage each disk of a Virtual Machine individually. VM centric means that you can do all storage actions like snapshoting, cloning or replication at the Virtual Machine level, allowing to easily select for example only the most important Virtual Machines for replication.

The Open vStorage Storage Router is optimized to deal with 4K or larger block sizes, making it the ideal storage platform for virtualized workloads. Because the time based approach and deduped caching, Open vStorage can deliver features like zero copy snapshot, cloning, and thin provisioning, making it ideal to spin off Virtual Machines from master templates in a fast and very efficient way.

Open vStorage automatically takes snapshots of data as it is written. These snapshots provide roll-back points for restoring data or undoing an action. They are also used as the basis for Virtual Machine cloning for temporary or testing environments. Open vStorage can take hundreds of point-in-time snapshots by creating a copy of a volumes’ indices. Changes to existing data or new data are redirected to free space. This approach also minimizes the storage used for snapshots, since only changes are maintained. Snapshots can be kept for a week, a month or even longer. Restoring data from a snapshot is also fast and straightforward as no data needs to be copied.

Open vStorage efficiently manages space allocation. This thin provisioning allocates disk space to a volume only as data is written to the Virtual Disk. It’s like creating a Gmail account with unlimited mailbox storage, but in reality, space is assigned as and when needed.