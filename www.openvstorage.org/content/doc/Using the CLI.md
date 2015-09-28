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

