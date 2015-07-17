+++
#Don't remove title!
title = "Using the GUI"
+++


An Open vStorge Cluster can be managed through a single web based
interface. The below section will explain how to access and manage an
Open vStorage Cluster through this GUI.

### Log In

The GUI to manage an Open vStorage can be found by navigating with an
HTML5 compatible browsers to the IP address of any of the Grid Storage
Routers (GSRs) in your cluster. In case you use HTTP instead of HTTPS,
the URL will be rewritten to use HTTPS. HTTPS is used to make sure all
the communication between your pc and the Open vStorage Cluster is
secure. When opening the GUI for the first time, you will be presented
with a message about the security certificate. Proceed anyway.

-   You will see the following login screen:

![](images/login.png)


-   Enter your credentials. The default administrator credentials are
    username:*admin*, password:*admin*.
-   Click the *Sign In* button.

When the login was successful, you will be transferred to the [Open
vStorage Dashboard]( {{< relref "Using the GUI.md" >}}#dashboard ).

<a name="dashboard" class="internal-ref"></a>
### The Open vStorage Dashboard

The Open vStorage Dashboard is the default starting point when working
with an Open vStorage Cluster. The Dashboard is a view on the health,
performance and status of the Cluster. unsupported

![](images/dashboard\_small.png)


Following panels, from left to right and top to bottom, can be found on
the Dashboard:

-   Storage Routers: The Storage Routers panel list all the Storage Routers
    registered in the Open vStorage Cluster. For each Storage Router the status,
    name, IP address and amount of vDisks is displayed.
-   Open vStorage Backends (Optional): The Open vStorage Backend panel displays all registered Open vStorage Backends and their status. This panel is only displayed in case the hyper-converged version is installed.
-   Cache Hit Ratio: The Cache Hit Ratio panel contains the percentage
    of the cache hits to the total amount of cache lookups for all
    vDisks. Below the current amount of IOPS is displayed for the whole
    Cluster. The panel gets updated every 5 seconds.
-   vMachines: The vMachine panels lists the top vMachines according to
    IOPS and Stored Data. You can use the drop-down list to select the
    property to filter on. Click the number next to the vMachines title
    to go directly to the list of vMachines.
-   vDisk Statistics: The vDisk Statistics panels lists the average on
    IOPS, Read and Write Speed and Cache Hits over 5 seconds summarized
    for all vDisks in the Custer. The values are updated automatically
    every 5 seconds.
-   vPools: The vPools panels lists the top vPools according to Stored
    Data and Bandwidth. Use the drop-down list to select the property to
    filter on.

At the bottom of the page a panel is displayed with the amount of cache
hits, the total amount of data (including meta-data) written to the
Storage Backend, the data read from the Storage Backend and the bandwith
saved by Cache Hits. A read is served from the cache when it is found in
the read or write cache and no request is needed to fetch the data from
the Backend. The total amount of data written to the Storage Backend is
larger than the amount of data currently stored on the Backend as data
gets scrubbed when it falls outside the retention window. Data gets read
from the Backend when a cache miss occurs and the data needs to be
fetched from the Backend. The amount of data is the actual data plus the
overhead of the meta-data that needs to be retrieved. Due to its
aggressive caching mechanism, Open vStorage allows to minimize the load
on the Storage Backend. The total amount of bandwidth saved by the
caching is expressed by the Bandwidth Saved statistic.

### The Menu

The top navigation lists following sections:

-   [Storage Routers](/doc/gsrs): the Storage Routers running the Open
    vStorage software.
-   [vPools](/doc/vpools): the available Virtual Storage Pools which can be
    used to deploy vMachines.
-   [vMachines](/doc/vmachines): the Virtual Machines with vDisks served by
    Open vStorage.
-   [vDisks](/doc/vdisks): the virtual disks served by Open vStorage.
-   [vTemplates](/doc/vtemplates): the blueprints for a new vMachines.
-   [Backends](/doc/backends): the Open vStorage Backends (only applicable
    when running hyperconverged).

<a name="administration" class="internal-ref"></a>
### Administration

The Administration section acts as the central location to manage and
configure your Open vStorage Cluster.

### Administrative Actions

Following actions are available for logged in users of the GUI:

#### About Open vStorage

The About page displays the installed Open vStorage packages installed
on every Node in the Open vStorage Cluster.

![](images/aboutopenvstorage.png)


Add the bottom you can find an overview of the important Open vStorage
related websites such as the [Project
Website](http://www.openvstorage.com) and the [Source
Code](https://bitbucket.org/openvstorage/openvstorage).

<a name="hmc" class="internal-ref"></a>
#### Hypervisor Management

The Hypervisor Management allows to register Hypervirsor Management
Centers (HMC) such as VMware vCenter or an OpenStack Controller Node. To
add a HMC, click the *Add new Center* button and provide a friendly
name, type, administrator username, password and IP address.

Once a HMC is added following details will be displayed:

-   Name: friendly name used in the GUI.
-   IP: IP address the HMC is listening on.
-   Port: Port the HMC is listening on.
-   Type: Type of the Hypervisor Management Center.
-   Username: Administrator username of the HMC.
-   OVS Hosts/Total: The amount of Hosts known by the Open vStorage
    framework versus the total amount of Hosts registered in the HMC.
-   Actions: Delete the HMC.

Below the Hosts known by the Open vStorage framework are listed. For
each Host following info is displayed:

-   Name: Name of the Host.
-   IP: IP address of the Host.
-   Type: Hypervisor of the Host.
-   Mgtm. Center: The Hypervisor Management Center managing the Host.

In case a Hosts is highlighted in red, it means the Host is known by the
Open vStorage framework but not linked to the HMC managing this Host.
Click the Mgmt. Center value to edit and select the correct HMC from the
list. *Hosts highlighted in red are not covered for automated
fail-over.*

#### User Management

Under User Management you can add new users or delete existing users.
There are 2 groups of users: Viewers and Administrators. Viewers can
only see the dashboard and the detail pages but can not launch any
actions. Administrators can view all pages and launch actions. To edit a
password of a user, select the key-icon next to the username.

Under the OAuth 2 Clients section, you can see an overview of all
configured OAuth clients for the selected user. By default no OAuth2
client is generated. To add an OAuth2 client for a user, simply add a
name and select all appropriate roles.

All user actions and all actions executed by the Open vStorage core are
logged for 30 days. To list all logged actions open an interactive
ipython session from the command line of a Storage Router by typing:

~~~~ {.sourceCode .python}
ovs
~~~~

In the ipython session execute

~~~~ {.sourceCode .python}
from ovs.dal.lists.loglist import LogList
for log in LogList.get_logs():
    print log
~~~~

The result for a single entry will look like:

~~~~ {.sourceCode .python}
{
    "storagedriver_guid": "dcc17681-a157-454e-be7b-a229afddafae",
    "method_args": [],
    "method_kwargs": {
        "storagedriver_id": "local0050569647a1",
        "name": "/test/test.vmx"
    },
    "module": "ovs.lib.vmachine",
    "source": "VOLUMEDRIVER_TASK",
    "time": 1411726920.744713,
    "user_guid": null,
    "guid": "725699e3-e2af-448f-b165-8eecc1e5a491",
    "method": "delete_from_voldrv",
    "metadata": null
}
~~~~

#### User Mgmt.

Under User Mgmt. you get can overview of all the registered users and
OAuth 2 Clients that have access to the Open vStorage environment.

Under Users you get an overview of all the users. To add a new user, add
a name in the textbox, select the group and press the check mark to
confirm. There are 2 groups: Administrators and Viewers. Viewers can not
make any changes and have no access to the Administration section.
Administrators can make changes to the Open vStorage environment (f.e.
create new users and vPools) and have access to the Administration
section.

Under OAuth 2 Clients you get an overview of all the registered OAuth 2
Clients. To add a new client, add a name in the textbox, select the role
and press the check mark. The Client ID and secret token will
automatically be generated.

#### Statistics

Under Statistics you get an overview of the load on the framework. Under
Open vStorage DAL, you get an overview of the performance of the Data
Abstraction Layer: the amount of objects being loaded from the database
and values retrieved from teh cache. The Memcache section displays the
current Memcache, the caching component of the API, activity, its size
in memory and uptime.

#### Support

Under Support you can manage the support options of your Open vStorage
environment and download the log files. When asked by the Open vStorage
Support Team , click the *download logfiles* link and mail the logfiles
of the Open vStorage nodes to the support team. Under the Call Home
section you can enable or disable the heartbeats and whether or not
Remote Access is enabled. Enabling Remote Access will set up a VPN
tunnel between the central datacenter and the individual Open vStorage
environment. This will allow the Open vStorage Support team to remotely
log in and troubleshoot the issue. The content of the heartbeats can be
found at the bottom of the page under Heartbeat data.

<a name="Licenses"></a>
#### Licenses

Under Licenses you get an overview of the licenses entered for the Open
vStorage environment. To apply for a free community license fill in the
form with your name, company name, email address, telephone number and
indicate that you accept the license agreement. Click *Register* to send
the request. ![](images/register.png)
 You will receive a license key
by email on the provided email address. Click *Add license* to add a new
or upgrade an existing license and unlock the [Backend](/doc/backends)
functionality. The free community license has a build-in restriction
which allows it to be used on maximum 4 Storage Routers and in total 16
(ASD) disks. You will be able to create 50 namespaces. Each vPool and
vDisk is considered to be a namespace. For example in a single vPool
environment, you will be able to create 49 vDisks.

#### Updates
Under updates you can see if there are updates available for the different components of Open vStorage. Click the button to update the different components.

#### API

Links to the API of Open vStorage. You can find more info
[here](/doc/Using%20the%20API) on using the Open vStorage API.

#### Log out

Safely ends the session and logs the current user out of the Open
vStorage GUI.
