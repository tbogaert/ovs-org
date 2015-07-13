### Open vStorage Monitoring

An open vStorage can be monitored through SNMP with your favorite
monitoring tool (Nagios, Zenoss, ...). Please refer to the documentation
of your monitoring tool on how to setup SNMP monitoring. The OIDs use a
predefines naming scheme: e.g. 1.3.6.1.4.1.29961.x.y.z . In this scheme x is
the class of the object, y is the instance of teh object and z the
attribute (and/or sub-attributes).

**If snmpd is running on port 161 the ovs-snmp daemon will not start as by default it is also using that port. Change the ovs-snmp port by editing the config file (/etc/init/ovs-snmp.conf) and replace "--port 161" with the desired port (e.g. "--port 9161") and then restart the ovs-snmp service (service ovs-snmp restart)**


The different types are:

-   vMachines: 1.3.6.1.4.1.29961.0.y.z
-   vDisks: 1.3.6.1.4.1.29961.1.y.z
-   Physical Machines: 1.3.6.1.4.1.29961.2.y.z
-   vPools: 1.3.6.1.4.1.29961.3.y.z
-   Storage Driver: 1.3.6.1.4.1.29961.4.y.z
-   Storage Routers: 1.3.6.1.4.1.29961.10.y.z
-   vTemplates: 1.3.6.1.4.1.29961.11.y.z

You can extract all OID of a certain object by running the next code
sample from the CLI:

~~~~ {.sourceCode .python}
ovs #start an interactive python session
from ovs.extensions.snmp.ovssnmpserver import OVSSNMPServer
server = OVSSNMPServer()
server.get_mappings('<GUID of the object>')
e.g:
Out:
   {u'1.3.6.1.4.1.29961.11.0.0': 'guid',
    u'1.3.6.1.4.1.29961.11.0.1': 'name',
    u'1.3.6.1.4.1.29961.11.0.2': '#children'}
~~~~

The result is a dict with as key the OID and as values the attribute
name. Attributes starting with \# are computed attributes by the SNMP
agent.

An an example we present the OIDs after a clean install.

~~~~ {.sourceCode .python}
1.3.6.1.4.1.29961.2.0.2 : host_status
1.3.6.1.4.1.29961.2.0.1 : name
1.3.6.1.4.1.29961.2.0.0 : guid
1.3.6.1.4.1.29961.10.0.11 : #vdisks
1.3.6.1.4.1.29961.10.0.10 : status
1.3.6.1.4.1.29961.10.0.13 : #stored_data
1.3.6.1.4.1.29961.10.0.12 : #vmachines
1.3.6.1.4.1.29961.10.0.9 : machineid
1.3.6.1.4.1.29961.10.0.8 : ip
1.3.6.1.4.1.29961.10.0.1 : name
1.3.6.1.4.1.29961.10.0.0 : guid
1.3.6.1.4.1.29961.10.0.3 : pmachine_host_status
1.3.6.1.4.1.29961.10.0.2 : hypervisor_status
1.3.6.1.4.1.29961.10.0.5 : devicename
1.3.6.1.4.1.29961.10.0.4 : description
1.3.6.1.4.1.29961.10.0.7 : hypervisorid
1.3.6.1.4.1.29961.10.0.6 : failover_mode
~~~~

Note: Open vStorage does at the moment not support SNMP traps.