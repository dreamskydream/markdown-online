Linux中缺少运行库的解决办法
==
1. 编辑 vi /etc/ld.so.conf
2. root权限执行 /sbin/ldconfig -v命令

这样 ldd 才能找到这个库

/etc/ld.so.conf:
这个文件记录了编译时使用的动态链接库的路径。
默认情况下，编译器只会使用/lib和/usr/lib这两个目录下的库文件
如果你安装了某些库，没有指定 –prefix=/usr 这样lib库就装到了/usr/local下，而又没有在/etc/ld.so.conf中添加/usr/local/lib，就会报错了

ldconfig是个什么东东吧 ：
它是一个程序，通常它位于/sbin下，是root用户使用的东东。具体作用及用法可以man ldconfig查到
简单的说，它的作用就是将/etc/ld.so.conf列出的路径下的库文件 缓存到/etc/ld.so.cache 以供使用
因此当安装完一些库文件，(例如刚安装好glib)，或者修改ld.so.conf增加新的库路径后，需要运行一下/sbin/ldconfig
使所有的库文件都被缓存到ld.so.cache中，如果没做，即使库文件明明就在/usr/lib下的，也是不会被使用的，结果
编译过程中抱错，缺少xxx库。

以上尝试后发现还是不行，添加 /usr/lib/x86_64-linux-gnu 有许多QT库文件