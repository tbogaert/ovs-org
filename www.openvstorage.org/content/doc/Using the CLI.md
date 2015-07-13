+++
#Don't remove title!
title = "Using The CLI"
+++

### Introduction

Open vStorage can be manged from an interactive Python CLI. We strongly
recommend you to use ipython as your standard python shell, ipython is
an amazing tool which allows you to work in a shell with a lot of
flexibility.

This section is for experienced Open vStorage user. Initiaiting the
wrong command might result in dataloss.

### Start the CLI

To start the interactive ipython session, open a Putty session as root
to the Open vStorage GSR and type:

~~~~ {.sourceCode .python}
ovs
~~~~

The interactive python session will start up

~~~~ {.sourceCode .python}
IPython 0.13.2 -- An enhanced Interactive Python.
?         -> Introduction and overview of IPython's features.
%quickref -> Quick reference.
help      -> Python's own help system.
object?   -> Details about 'object', use 'object??' for extra details.

In [1]:
~~~~

### Basic Tasks

### List all vMachines

~~~~ {.sourceCode .python}
from ovs.dal.hybrids.vmachine import VMachine
from ovs.dal.lists.vmachinelist import VMachineList

vmachine_list = VMachineList.get_vmachines()

for vmachine in vmachine_list:
    print vmachine.name
~~~~

### List all audited actions

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