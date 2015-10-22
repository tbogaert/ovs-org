+++
#Don't remove title!
title = "CentOS"
tag = "howto"
+++

### Introduction
Next to Ubuntu, Open vStorage can also be installed on centOS 7

### The limitations:
- No name based releases (yet) or quality levels to select from
- Needs external repos (no upstream internally)

### Install Open vStorage on CentOS 7
-   Install CentOS7
-   Install wget
~~~~ {.sourceCode .python}
yum install wget -y
~~~~
-   Add the necessary external repos
~~~~ {.sourceCode .python}
wget -O /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-7.repo
wget -O /etc/yum.repos.d/epel.repo http://mirrors.aliyun.com/repo/epel-7.repo
wget -P /etc/yum.repos.d/ http://packages.efficios.com/repo.files/EfficiOS-RHEL7-x86-64.repo
~~~~
-   Currently there is a dependency on the Fedora 22 repo so add it also
~~~~ {.sourceCode .python}
echo '[fc22]
name=fc22
baseurl=https://download.fedoraproject.org/pub/fedora/linux/releases/22/Everything/x86_64/os
enabled=0' > /etc/yum.repos.d/fc22.repo
rpm --import 'https://pgp.mit.edu/pks/lookup?op=get&search=0x11ADC0948E1431D5'
~~~~
-   Add the Openvstorage RPM repo
~~~~ {.sourceCode .python}
echo '[openvstorage]
name=OVS repo
baseurl=http://yum.openvstorage.org/CentOS/7/x86_64/
enabled=1
gpgcheck=0' > /etc/yum.repos.d/ovs.repo
~~~~
-   First install the dependencies
~~~~ {.sourceCode .python}
yum install --nogpgcheck --enablerepo=fc22 librbd1 librados2 gcc volumedriver-server -y
~~~~ {.sourceCode .python}
-   Install Open vStorage
~~~~ {.sourceCode .python}
yum install --nogpgcheck openvstorage-hc -y
~~~~


[Please let us know](https://groups.google.com/forum/#!forum/open-vstorage) if you have any issues with installing Open vStorage on CentOS :
-   Incomplete packages, install issues due to bugs in install scripts, or missing install scripts
-   Missing dependencies, wrong dependencies, conflicting dependencies
