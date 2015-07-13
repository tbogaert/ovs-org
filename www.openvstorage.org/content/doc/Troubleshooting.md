### Troubleshooting

### Introduction

Before starting to troubleshoot your Open vStorage Cluster, make sure
all Storage Routers are online and the GUI is working.

### Open vStorage Logs

The main location to search for Open vStorage log files is /var/log/ovs/

The Framework logs in /var/log/ovs/. A few components are defined each
with their own logfiles:

-   api.log - API related log entries
-   arakoon.log - Logs related to the management of Arakoon
-   celery.log - Some of the Celery related logs (e.g. the distributed
    Celery Beat scheduler)
-   dal.log - Log entries related to the DAL (Data Access Layer)
-   django.log - Logs related to the management of Django
-   extensions.log - Logs related to various OVS extensions (e.g.
    Service watchers, Hypervisor clients, ...)
-   lib.log - Generic logs for all business related tasks (the more high
    level tasks - e.g. the creation of a vMachine)

The Volumedriver logs, inside the volumedriver subdirectory
(/var/log/ovs/volumedriver)

-   Volumedriver: \$vPool}.log
-   Failover Cache: foc\_\$vPool}.log

Open vStorage runs a lot of services which all log in their
corresponding service framework logs. For more information about the
services, see below.

-   Upstart (e.g. on Ubuntu)
    -   Location: /var/log/upstart/ovs-\*.log

By executing the below command, all required logfiles can be gathered
and stored into an archive which can be found at
/var/tmp/ovs-{hostname}-logs.tar

~~~~ {.sourceCode .python}
ovs collect logs
~~~~

### Inspecting Celery workers and their tasks.

### API

By using the API resource at <https://>\<ip\>/api/tasks/ one can - after
logging in - get an overview of all tasks active, scheduled, revoked or
reserved on workers. See the API documentation for more details on how
to use the API.

### GUI (celery flower)

While not installed by default, one can install celery flower very easy.
However, this service will only keep track of tasks while it's running.
This means that when troubleshooting a failed taks, one would have to
reproduce this failure again to see its details.

~~~~ {.sourceCode .python}
pip install flower
su - ovs -c "/usr/local/bin/celery flower --port=5555 --broker=amqp://ovs:0penv5tor4ge@<ip>:5672//"
~~~~

This will make the Celery Flower Gui available on <https://>\<ip\>:5555

The Open vStorage services
--------------------------

Open vStorage has two types of nodes; Master nodes and Extra nodes.
Certain services are only available on certain types of nodes of which
you can find an overview below.

To find out which type a node is, execute the following command on that
node. Typically, the first 3 installed nodes are Master.

~~~~ {.sourceCode .python}
cat /etc/avahi/services/ovs_cluster.service | grep _node._tcp | cut -d '_' -f 3
~~~~

To list the running Open vStorage services type in the shell

~~~~ {.sourceCode .python}
ovs monitor services
~~~~

Master nodes have following services running:

-   ovs-arakoon-ovsdb - Arakoon logs for the Framework's persistent data
-   ovs-arakoon-voldrv - Arakoon logs for the Volumedriver's persistent
    data
-   ovs-elasticsearch - Elasticsearch logs, used for log management
-   ovs-memcached - Memcache logs for the Framework's cached data
-   ovs-nginx - The proxy used for serving the GUI
-   ovs-rabbitmq - The Message Queue used by the Framework (e.g. celery)
    and the Volumedriver (e.g. IPC between Framework and Volumedriver)
-   ovs-scheduled-tasks - Logs related to scheduled tasks (e.g. taking
    snapshots, collapsing Arakoon, ...)
-   ovs-snmp - Logs regarding the SNMP service
-   ovs-volumerouter-consumer - The logs of the process that launches
    Framework tasks based on Volumedriver events
-   ovs-watcher - The watcher service to monitor availability of
    distributed services required by various other services.
    -   It acts as a distributed service start/stop trigger; e.g. if
        Arakoon is unavailble on any of the Master nodes, the ovs-worker
        service will also stop on Extra nodes
-   ovs-webapp-api - API/Django process log
-   ovs-workers - Celery process log

For extra nodes following services should be running:

-   ovs-volumerouter-consumer - The logs of the process that launches
    Framework tasks based on Volumedriver events
-   ovs-watcher - The watcher service to monitor availability of
    distributed services required by various other services.
    -   It acts as a distributed service start/stop trigger; e.g. if
        Arakoon is unavailble on any of the Master nodes, the ovs-worker
        service will also stop on Extra nodes
-   ovs-workers - Celery process log

For each vpool served on any node, following services are running:

-   ovs-volumedriver\_\<vpoolname\> - The Volumedriver logs
-   ovs-failovercache\_\<vpoolname\> - The FOC logs