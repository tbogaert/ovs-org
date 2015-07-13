### HOWTO

### Introduction

The HOWTO-section answers all your "How do I do ... with Open
vStorage"-questions. Did we miss something? Let us know and we will add
it or leave a question in the [Open vStorage discussion
group](https://groups.google.com/forum/#!forum/open-vstorage)!

### How to shutdown the Open vStorage Storage Router

Open (as root) a Putty Session to the Storage Router and execute the
following line of code:

~~~~ {.sourceCode .python}
shutdown -h now
~~~~

### How to remove a datastore after removing the Storage Router

The correct sequence to remove a Storage Router, is to first move all
vMachine to another ESXi and only then delete the Storage Router. But in
case you didn't move the vMachine and the Storage Router is already
reinstalled, use following sequence to cleanup the ESXi Host.

-   In the vSphere GUI remove all vMachines from the ESXi by
    right-clicking and selecting Remove from Inventory.
-   Under Configuration \> Storage, select the Rescan All option.
-   Select the datastore offered by the Storage Router and select delete
    from the top right menu.