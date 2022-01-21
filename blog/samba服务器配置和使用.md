samba服务器配置和使用
==
安装：

`#dnf install samba`

端口：

管理工作组：UDP137，138

共享数据：TCP139，445（不一定存在）

启动服务：

`#systemctl start  smb`

`#systemctl enable smb`

配置文件（/etc/samba/smb.conf）:

1.先设定好服务器整体环境方面的参数

[global]

config file = /etc/samba/conf/smb.conf.%m

说明：config file可以让你使用另一个配置文件来覆盖缺省的配置文件。如果文件 不存在，则该项无效。这个参数很有用，可以使得samba配置更灵活，可以让一台samba服务器模拟多台不同配置的服务器。比如，你想让PC1（主机 名）这台电脑在访问Samba Server时使用它自己的配置文件，那么先在/etc/samba/host/下为PC1配置一个名为smb.conf.pc1的文件，然后在 smb.conf中加入：config file = /etc/samba/conf/smb.conf.%m。这样当PC1请求连接Samba Server时，smb.conf.%m就被替换成smb.conf.pc1。这样，对于PC1来说，它所使用的Samba服务就是由 smb.conf.pc1定义的，而其他机器访问Samba Server则还是应用smb.conf。
``` 
#用户组

workgroup = mygroup

#欢迎信息

server string = Samba Server Version %v

#加密

security = user

#使用TDB 数据库格式

passdb backend = tdbsam

#不加载打印机

load printers = no

#共享名

[homes]

#说明

comment = Home Directories

#除了使用者自己外,不可被其他人浏览

browseable = no

#挂载后可擦写此分享

writable = yes

#建立档案的权限为 664

create mode = 0664

#建立目录的权限为 775

directory mode = 0775

#指定允许访问该共享资源的用户&&多个用户或者组中间用逗号隔开，如果要加入一个组就用"@组名"表示,%s表示代替前一个[]里的内容

valid users = %S

valid users = MYDOMAIN\%S

[share]

comment = Public Stuff

#实际的 Linux 上面的目录位置

path = /home/samba

#用来指定该共享的管理员（对该共享具有完全控制权限）

admin users = admin

#指定允许访问该共享资源的用户或者组

valid users = share,@share

#指定不允许访问该共享资源的用户

invalid users = guest

#除了使用者自己外,不可被其他人浏览

browseable = no

#指定可以在该共享下写入文件的用户

write list = share,@share

#指定该共享是否允许guest账户访问

public = no

#指定该共享路径是否可写

writable = yes

#可以在该共享下写入文件的用户和组

write list = share,@share
```
2. 用 testparm 查阅 smb.conf 的语法设定正确性

3.建立目录

`#mkdir /home/project`

`#chmod 2770 /home/project`

最后改变目录用户或者组属性

4.设定可使用 Samba 的用户账号与密码

`#useradd -M -s /sbin/nologin -d /dev/null -G user1 share`

使用 pdbedit 指令功能

`# pdbedit -L [-vw]        <==单纯的察看帐户信息`

`# pdbedit -a'-r'-x -u 账号       <==新增/修改/删除账号`

`# pdbedit -a -m -u 机器账号       <==与 PDC 有关的机器码`

选项与参数:

-L :列出目前在数据库当中的账号与 UID 等相关信息;

-v :需要搭配 -L 来执行,可列出更多的讯息,包括家目录等数据;

-w :需要搭配 -L 来执行,使用旧版的 smbpasswd 格式来显示数据;

-a :新增一个可使用 Samba 的账号,后面的账号需要在 /etc/passwd 内存在者;

-r :修改一个账号的相关信息,需搭配很多特殊参数,请 man pdbedit;

-x :删除一个可使用 Samba 的账号,可先用 -L 找到账号后再删除;

-m :后面接的是机器的代码 (machine account),与 domain model 有关!

`# pdbedit -a -u share`

修改密码:

`#smbpasswd share`

5.查看分享资源

`#smbclient -L [//主机或 IP] [-U 使用者账号]`

6.挂载

`#mount -t cifs //127.0.0.1/share /mnt -o username=share,password=123456`

7.smbstatus:观察 SAMBA 的状态

`# smbstatus [-pS] [-u username]`

选项与参数:

-p :列出已经使用 SAMBA 联机的程序 PID ;

-S :列出已经被使用的资源共享状态;

-u :只列出某个用户相关的分享数据

8.SELinux设置

`#允许共享Home目录`

`setsebool -P samba_enable_home_dirs on`

#更改SELinux用户

`chcon -t samba_share_t /path/to/directory`