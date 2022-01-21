Linux中修改limits值
==
在/etc/security/limits.conf 最后增加:

* soft nofile 65535

* hard nofile 65535

修改ulimit值