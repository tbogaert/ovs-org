+++
#Don't remove title!
title = "Cinder Plugin"
+++

### Introduction

Open vStorage can be used as block storage for OpenStack. To do this you
need to register the Open vStorage plugin as an OpenStack Cinder Plugin. 

In case you want to undo the changes made by the installation  of the Cinder Plugin follow [these steps]( {{< relref "Cinder Plugin.md" >}}#unconfigurecinder).

### Configure Cinder and OpenStack

#### Prerequisites

-   Install OpenStack Juno/Kilo/Liberty on all nodes
-   Install Open vStorage on all [KVM nodes](/doc/KVM Installation). Make
    sure OpenStack is installed first. 
-   In the Administration section, select *Hypervisor Mgmt.*. Click *Add new Center* and complete the form. Next indicate at the bottom which Nova nodes you want to be managed by the OpenStack Controller Node. Do not create a vPool before you have registered the Hypervisor Management Center and added the nodes.
-   Create a vPool and add the vPool to all nodes that run Cinder. In the documentation we  will use the name VPOOL1 and VPOOL2 as an example. In case you don't extend the vPool on all nodes, use storage_availability_zones to make sure the VM gets created on a Nova node which has the vPool configured (Juno, Kilo).
-   
#### Changes required to the controller
- Update the maximal amount of MySQL connections in /etc/mysql/my.cnf to 1000 and restart MySQL.

#### Changes required to all Nova nodes

-   On all Nova nodes execute

~~~~ {.sourceCode .python}
su stack
sed -i '/vnc_enabled.*/c\vnc_enabled = True' /etc/nova/nova.conf
sed -i '/vncserver_listen.*/c\vncserver_listen = 0.0.0.0' /etc/nova/nova.conf
sed -i '/instance_name_template.*/c\instance_name_template = "%(hostname)s"' /etc/nova/nova.conf
sed -i '/force_config_drive.*/c\force_config_drive = False' /etc/nova/nova.conf
sed -i '/verbose =.*/a live_migration_flag = VIR_MIGRATE_UNDEFINE_SOURCE,VIR_MIGRATE_PEER2PEER,VIR_MIGRATE_LIVE' /etc/nova/nova.conf
sed -i '/verbose =.*/a use_cow_images = False' /etc/nova/nova.conf

cat <<EOF >> /etc/nova/nova.conf

[serial_console]
base_url = http://127.0.0.1:6083/
enabled = True
listen = 127.0.0.1
port_range = 10000:20000
proxyclient_address = 127.0.0.1
EOF
~~~~

-   Restart the Nova services
    -   On DevStack restart n-api, n-cpu, n-sch
    -   On OpenStack: service nova-api-os-compute, nova-compute,
        nova-scheduler restart
-   Libvirtd changes on all Nova nodes

~~~~ {.sourceCode .python}
#
# file /etc/libvirt/libvirtd.conf
#
grep -q '^listen_tls' /etc/libvirt/libvirtd.conf && sed -i 's/^listen_tls.*/listen_tls = 0/' /etc/libvirt/libvirtd.conf || echo 'listen_tls = 0' >> /etc/libvirt/libvirtd.conf
grep -q '^listen_tcp' /etc/libvirt/libvirtd.conf && sed -i 's/^listen_tcp.*/listen_tcp = 1/' /etc/libvirt/libvirtd.conf || echo 'listen_tcp = 1' >> /etc/libvirt/libvirtd.conf
grep -q '^auth_tcp' /etc/libvirt/libvirtd.conf && sed -i 's/^auth_tcp.*/auth_tcp = "none"/' /etc/libvirt/libvirtd.conf || echo 'auth_tcp = "none"' >> /etc/libvirt/libvirtd.conf
#
# generate unique uuid for libvirt
#
sed -i 's/.*host_uuid.*/host_uuid = '`uuidgen`'/' /etc/libvirt/libvirtd.conf
#
# file /etc/libvirt/qemu.conf
#
grep -q '.*dynamic_ownership' /etc/libvirt/qemu.conf && sed -i 's/.*dynamic_ownership.*/dynamic_ownership = 0/' /etc/libvirt/qemu.conf || echo 'dynamic_ownership = 0' >> /etc/libvirt/qemu.conf
#
# file /etc/default/libvirt-bin
#
sed -i '/libvirtd_opts.*/c\libvirtd_opts="-d -l"' /etc/default/libvirt-bin
#
# file /etc/init/libvirt-bin.conf
#
sed -i '/.*env libvirtd_opts=.*/c\env libvirtd_opts="-d -l"' /etc/init/libvirt-bin.conf
#
# restart libvirt and check status
#
stop libvirt-bin && start libvirt-bin || start libvirt-bin
ps -ef | grep libvirtd
~~~~

#### Update the instances\_path

-   Execute the following on all the Nova nodes

~~~~ {.sourceCode .python}
# replace with correct vPool name for your environment
export VPOOLNAME=<VPOOLNAME1>

sed -i '/instances_path =.*/c\instances_path = /mnt/'${VPOOLNAME}'/instances' /etc/nova/nova.conf
sudo mkdir -p /mnt/${VPOOLNAME}/instances
~~~~

-   Restart the Nova services

#### Configure passwordless authentication for stack user

-   Passwordless authentication is required for the stack user to allow
    the migrate workflow to complete.

~~~~ {.sourceCode .python}
# on each node as root (set a password for use in next step)
passwd stack
~~~~

~~~~ {.sourceCode .python}
# on each node towards all other nodes
su - stack
ssh-keygen
ssh-copy-id stack@<all_other_nodes>
~~~~

#### Configuring the Open vStorage SNMP daemon
If snmpd is running on port 161 the ovs-snmp daemon will not start as by default it is also using that port. Change the ovs-snmp port by editing the config file (/etc/init/ovs-snmp.conf) and replace "--port 161" with the desired port (e.g. "--port 9161") and then restart the ovs-snmp service (service ovs-snmp restart)


#### Creating an instance directly from an image

Creating an instance directly from an image is not supported. This
action will create a "copy on write" file which only contains the
changes compared to the image. This file is based on the QCOW format
which is currently not supported.

<a name="unconfigurecinder" class="internal-ref"></a>
### Manually unconfigure the Cinder changes
- Revert the libvirtd changes on all Nova nodes in /etc/libvirt/libvirtd.conf,  /etc/libvirt/qemu.conf,  /etc/default/libvirt-bin and /etc/init/libvirt-bin.conf.
- Restart libvirt-bin 
~~~~ {.sourceCode .python}
stop libvirt-bin && start libvirt-bin || start libvirt-bin
ps -ef | grep libvirtd
~~~~
- Remove or revert to the default value the changes made to the instances_path from /etc/nova/nova.conf. Restart n-api and n-cpu services.
- Remove the added users to the
~~~~ {.sourceCode .python}
deluser stack ovs; deluser libvirt-qemu ovs
~~~~
- Disable messaging and consumer
    - Stop the events consumer by "executing service ovs-openstack-events-consumer stop".
    - Remove the lines containing "notification\*" and "notify\*" from /etc/nova/nova.conf.
    - Remove the lines containing "notification*" from /etc/cinder/cinder.conf.
    - Restart the n-api and c-api services
-  Remove the patched code
    - In opt/stack/nova/nova/virt/libvirt/volume.py: find the line starting with "class LibvirtFileVolumeDriver" and remove the whole class definition.
    - In /opt/stack/nova/nova/virt/libvirt/driver.py: find the line "'file=nova.virt.libvirt.volume.LibvirtFileVolumeDriver'," and remove it.
    - In /opt/stack/cinder/cinder/brick/initiator/connector.py: find the line "elif protocol in ["LOCAL", "FILE"]: " and replace with "elif protocol == "LOCAL": ".
    - Restart the c-vol and n-cpu services.
