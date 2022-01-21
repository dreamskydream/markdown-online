Linux更换网卡后网络服务不能启动的问题解决办法
==

1、迁移以后，会存在其中一个网卡无法启动（eth0 or eth1）

[root@ ~]# ifup eth0

WARNING: Deprecated config file /etc/modprobe.conf, all config files belong into /etc/modprobe.d/.

WARNING: Deprecated config file /etc/modprobe.conf, all config files belong into /etc/modprobe.d/.

Device eth0 does not seem to be present, delaying initialization.

2、删除文件

[root@ ~]# mv /etc/udev/rules.d/70-persistent-net.rules /etc/udev/rules.d/70-persistent-net.rules.bak

3、注释掉文件ifcfg-eth0及ifcfg-eth01中MAC地址（HWADDR）

[root@ ~]# cat /etc/sysconfig/network-scripts/ifcfg-eth0

DEVICE=eth0

BOOTPROTO=static

#HWADDR="XX:XX:XX:XX:XX:XX"

ONBOOT=yes

TYPE=Ethernet

4、重启服务器（重启网卡不好使）