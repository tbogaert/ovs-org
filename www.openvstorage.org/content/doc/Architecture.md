The Building Blocks
====================

### Introduction

The below paragraphs explain which technology was used for building Open
vStorage. The goal was a to build a distributed storage solution which
was optimized for performance and high reliability and availability.
Most of the code is written in Javascript, Python and C. The vStorage
Driver is written in C as performance optimization.

### JumpScale

[JumpScale](http://www.jumpscale.com/) is the glue which allows Open
vStorage to run in multi-node Cluster fashion. It allows to automate the
deployment and configuration of each of the Nodes and the different
components of a single Node while keeping the Cluster perspective in
mind. JumpScale is also responsible for aggregating the logs of all
Nodes but allows to keep track of individual logs of each Node by
assigning a unique ID to every component in the Open vStorage Cluster.

### Single Node

Each Node consists of the same building block: ![](images/Framework-2512-2146435c-0.png)

Together the Nodes offer a single unified Namespace which can be used to
deploy Virtual Machines across different hypervisors. The goal of the
Open vStorage was not to do any Virtual Machine orchestration but to
focus only on offering high performing Storage Storage .

Some of the Objects are completely stored in [Arakoon]( {{< relref "Architecture.md" >}}#arakoon) (User,
Group, Role, Branding, pMachine). Other Objects (vMachines, vDisk,
vPool, volumestoragerouter) are partly stored in Arakoon while the
dynamic properties, properties which frequently change, are retrieved
dynamically when necessary. This has the option to limit the stress on
the [Framework]( {{< relref "Architecture.md" >}}#framework) caused by the GUI and Administrative tasks.
When needed the dynamic properties are retrieved from the [vStorage
Driver]( {{< relref "Architecture.md" >}}#vstoragedriver ) and cached ([Memcached]( {{< relref "Architecture.md" >}}#memcached )).

<a name="framewor" class="internal-ref"></a>
### Framework

<a name="celery" class="internal-ref"></a>
#### Celery

[Celery](http://www.celeryproject.org/) is at the core of the Framework.
In Open vStorage Celery is used as a job queue for all tasks which need
to be executed. Celery was a natural selection as it is distributed,
simple and flexible.

[Celery
beat](http://docs.celeryproject.org/en/latest/userguide/periodic-tasks.html),
the scheduler inside Celery, is used to execute tasks at regular
intervals such as taking automatic snapshots, scrubbing and replicate
Snapshots to another Cluster.

#### RabbitMQ

RabbitMQ is the default broker for [Celery]( {{< relref "Architecture.md" >}}#celery ). All communication
is over is the AMQP (Advanced Message Queuing Protocol). There are 2
queues in Open vStorage: a queue for Celery and one for the [vStorage
Driver]( {{< relref "Architecture.md" >}}#vstoragedriver ). For example the vStorage Driver will put
events in the voldrv job queue whenever a vDisk is created. The RabbitMQ
consumer will create a task based upon the event a Celery task to store
the vDisks data in the Model in the Celery queue. This task gets
executed by the Celery worker running on one of the Nodes in the Open
vStorage Cluster.

#### Django

[Django](https://www.djangoproject.com/) is used as Framework to build
the GUI and the API. Both the GUI and API are running over https for
security reasons.

Within Open vStorage there are 2 types of API:

-   Customer API: An https exposed separate customer API is provided and
    exposes high level actions that can be integrated in 3rd party
    software. It is designed to be seldom changed, and it should be well
    defined and future proof. In case there are changes required, it
    will have to pass/flow through a deprecation policy. You can browse
    the Customer API at
    [<https://localhost/api/customer/>](https://localhost/api/customer/).
-   Internal API: An https exposed separate internal API provides
    various high level actions that are used in the HTML gui. It's only
    purpose it to provide call entry points for things it needs. This
    means not every Library module, class or method will be exposed, as
    there is not need to do so. As the HTML GUI is the only consumer, it
    will change as per HTML design changes. This can be at any build
    containing GUI changes. You can browse the internal API at
    [<https://localhost/api/internal/>](https://localhost/api/internal/).

<a name="openvstorage" class="internal-ref"></a>
#### The Open vStorage Library

The Open vStorage Library (ovs.lib) contains the actual business logic
of the Open vStorage Application. This is a native Python package which
can be consumed by any other Python code in the Framework. This Libary
gets consumed by the vStorage Driver its RabbitMQ consumers, the
Frontend GUI by means of the Internal API and 3rd party applications and
integrators which use the Customer API.

Each Object Class has a Controller, e.g. vMachineController and
vPoolController. These Controller classes consist of a library of Celery
tasks which can be executed by the respective Objects.

<a name="memcached" class="internal-ref"></a>
#### Memcached

[Memcached](http://memcached.org/) is used to serve all the data objects
to the GUI. In combines the data of [Arakoon]( {{< relref "Architecture.md" >}}#arakoon ), which is used
to store data persistent, and the vStorage Driver.

<a name="arakoon" class="internal-ref"></a>
#### Arakoon

[Arakoon](http://arakoon.org/) is an open source, distributed key-value
store that guarantees consistency above anything else. It was selected
to ensure that at anytime the data in the distributed data was
consistent. Eventual consistency is not an option when dealing with a
highly performant and highly reliable solution. In our case the Arakoon
cluster consists of3 nodes that contain the full range of key-value
pairs. Each node carries all the data, yet one node is assigned to be
the master node. The master node manages all the updates for all the
clients. Clients only talk to the master node, but may contact other
nodes to discover who the master node is; they do so because nodes can
assume different roles.

<a name="vstoragedriver" class="internal-ref"></a>
### vStorage Driver

More info will be added soon.