+++
#Don't remove title!
title = "Schedule the Rebalancing"
tag = "howto"
+++

### Introduction
The Open vStorage Backend will automatically make use of new ASDs when they are detected in order to balance the storage across as many disks as possible. The rebalancing process might be a quite resource intensive process as it moves fragments from disks which are highly used to disks which are less used.

### Schedule the Reblancing
The rebalancing of the data across ASDs happens automatically but in some cases it might be good to postpone this process to a better suited time (f.e. outside business hours).

To disable the automatic rebalancing for a backend execute:

~~~~ {.sourceCode .python}
stop ovs-alba-rebalancer_<backendname>
~~~~

To activate the automatic rebalancing again execute:

~~~~ {.sourceCode .python}
start ovs-alba-rebalancer_<backendname>
~~~~


