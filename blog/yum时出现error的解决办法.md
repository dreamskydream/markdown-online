yum时出现error的解决办法
==
### 错误内容：
``` 
Unable to detect release version (use ‘–releasever’ to specify release version)

error: rpmdb: damaged header #173 retrieved — skipping.
error: rpmdbNextIterator: skipping h# 173 blob size(55540): BAD, 8 + 16 * il(78) + dl(54284)
```

### 解决办法：
首先：删除/var/lib/下的rpm文件夹
之后：执行命令“rpm --rebuilddb”
最后：安装一个提供版本号的包“yum --releasever 8 install centos-release”
其中--releasever 8是指定版本号，我的是centos8，所以这么写。