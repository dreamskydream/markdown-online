FTP服务器架设（vsftpd）
==
安装：
--
`# dnf install vsftpd`

**FTP主动模式：客户端从一个任意的非特权端口N（N>1024）连接到FTP服务器的port 21命令端口。然后客户端开始监听端口N+1，**并发送FTP命令"port N+1"到FTP服务器。接着服务器会从它自己的数据端口（20）连接到客户端指定的数据端口（N+1）。

**FTP被动模式：客户端从一个任意的非特权端口N（N>1024）连接到FTP服务器的port 21命令端口。然后客户端开始监听端口N+1，**同时客户端提交 PASV命令。服务器会开启一个任意的非特权端口（P >1024），并发送PORT P命令给客户端。然后客户端发起从本地端口N+1到服务器的端口P的连接用来传送数据。

端口：

主动模式：TCP 21（指令），20（数据）端口

被动模式：TCP 21（指令），大于1024端口传输数据（可在配置文件中指定范围）

生成证书：
<pre>#openssl req -x509 -nodes -days 3650 -newkey rsa:2048 -keyout ftp.pem -out ftp.pem</pre>
配置文件：

/etc/vsftpd/vsftpd.conf

严格来说,整个 vsftpd 的配置文件就只有这个档案!这个档案的设定是以 bash的变量设定相同的方式来处理的, 也就是『参数=设定值』来设定的,注意, 等号两边不能有空白喔!至于详细的 vsftpd.conf 可以使用 『 man 5 vsftpd.conf 』来详查。

/etc/pam.d/vsftpd

这个是 vsftpd 使用 PAM 模块时的相关配置文件。主要用来作为身份认证之用,还有一些用户身份的抵挡功能, 也是透过这个档案来达成的。

/etc/vsftpd/ftpusers

与上一个档案有关系,也就是 PAM 模块 (/etc/pam.d/vsftpd) 所指定的那个无法登入的用户配置文件啊! 这个档案的设定很简单,你只要将『不想让他登入FTP 的账号』写入这个档案即可。

/etc/vsftpd/user_list

这个档案是否能够生效与 vsftpd.conf 内的两个参数有关,分别是『 userlist_enable, userlist_deny 』。 如果说 /etc/vsftpd/ftpusers 是

PAM 模块的抵挡设定项目,那么这个 /etc/vsftpd/user_list 则是 vsftpd 自定义的抵挡项目。事实上这个档案与 /etc/vsftpd/ftpusers 几乎一模一样, 在预设的情况下,你可以将不希望可登入 vsftpd 的账号写入这里。不过这个档案的功能会依据 vsftpd.conf 配置文件内的 serlist_deny={YES/NO} 而不同。

/etc/vsftpd/chroot_list

这个档案预设是不存在的,所以你必须要手动自行建立。这个档案的主要功能是可以将某些账号的使用者 chroot 在他们的家目录下!但这个档案要生效与vsftpd.conf 内的『 chroot_list_enable, chroot_list_file 』两个参数有关。如果你想要将某些实体用户限制在他们的家目录下而不许到其他目录去,可以启动这个设定项目。

/usr/sbin/vsftpd

这就是 vsftpd 的主要执行档，vsftpd 只有这一个执行档。

/var/ftp/

这个是 vsftpd 的预设匿名者登入的根目录，其实与 ftp 这个账号的家目录有关。

!!服务器环境设定
--
### 使用本地时间

use_localtime=yes

dirmessage_enable=yes

xferlog_enable=yes

connect_from_port_20=yes

xferlog_std_format=yes

listen=yes

pam_service_name=vsftpd

tcp_wrappers=yes

### 欢迎信息

banner_file=/etc/vsftpd/welcome.txt

### 限制带宽单位Bytes/秒

local_max_rate=100000000

### 限制最大同时在线人数

max_clients=100

max_per_ip=100

### 数据流传输10分钟停止传输

data_connection_timeout=600

### 发呆超过 10 分钟就断线

idle_session_timeout=600

write_enable=yes

userlist_enable=yes

userlist_deny=no

### user_list文件必须建立

userlist_file=/etc/vsftpd/user_list

### 为了避免一个安全漏洞，从 vsftpd 2.3.5 开始，chroot 目录必须不可写。

chroot_local_user=yes

chroot_list_enable=yes

### chroot_list必须建立，空文件都可以

chroot_list_file=/etc/vsftpd/chroot_list

### 被动式端口范围设定

pasv_min_port=65500

pasv_max_port=65535

### 设定上传文件权限

local_umask=002

### anonymous设定，设定上传目录拥有者为ftp

anonymous_enable=yes

no_anon_password=yes

anon_max_rate=100000000

anon_other_write_enable=yes

anon_mkdir_write_enable=yes

anon_upload_enable=yes

anon_root=/var/vsftpd/

### 下两行的作用是修改anonymous上传文件的拥有者为daemon,所以anonymous上传的文件是不能下载的，只有修改权限后才能下载

chown_uploads=yes

chown_username=daemon

### 针对实体账号的设定

local_enable=yes

### 针对 SSL 所加入的特别参数。

### 启动 SSL 的支持

ssl_enable=YES

### 但是不允许匿名者使用 SSL 喔

allow_anon_ssl=NO

### 强制实体用户数据传输加密

force_local_data_ssl=YES

### 登入时的帐密也加密

force_local_logins_ssl=YES

### 支持 TLS 方式即可,底下不用启动

ssl_tlsv1=YES

ssl_sslv2=NO

ssl_sslv3=NO

### 预设 RSA 加密的凭证档案所在

rsa_cert_file=/etc/vsftpd/vsftpd.pem



网上一个详细的教程，值得参考
==

一、安装vsftpd及相关组件：
yum -y install vsftpd* pam* db4*

二、修改FTP相关帐户：

1、vsftpd服务的宿主用户
useradd vsftpd -s /sbin/nologin
默认的vsftpd的服务宿主用户是root，但是这不符合安全性的需要。这里建立名字为vsftpd的用户，用他来作为支持vsftpd的服务宿主用户。由于该用户仅用来支持vsftpd服务用，因此没有许可他登陆系统的必要，并设定他为不能登陆系统的用户。

2、vsftpd的虚拟宿主用户

useradd virtual -d /var/www/html/ -s /sbin/nologin
chown -R virtual:virtual /var/www/html/

vsftpd的虚拟用户并不是系统用户，也就是说这些FTP的用户在系统中是不存在的。他们的总体权限其实是集中寄托在一个在系统中的某一个用户身上的，所谓vsftpd的虚拟宿主用户，就是这样一个支持着所有虚拟用户的宿主用户。由于他支撑了FTP的所有虚拟的用户，那么他本身的权限将会影响着这些虚拟的用户，因此，处于安全性的考虑，也要非分注意对该用户的权限的控制，该用户也绝对没有登陆系统的必要，这里也设定他为不能登陆系统的用户。

三、vsftpd.conf基本配置：
vim /etc/vsftpd/vsftpd.conf


# Example config file /etc/vsftpd/vsftpd.conf
#
# The default compiled in settings are fairly paranoid. This sample file
# loosens things up a bit, to make the ftp daemon more usable.
# Please see vsftpd.conf.5 for all compiled in defaults.
#
# READ THIS: This example file is NOT an exhaustive list of vsftpd options.
# Please read the vsftpd.conf.5 manual page to get a full idea of vsftpd's
# capabilities.
#
# Allow anonymous FTP? (Beware - allowed by default if you comment this out).
anonymous_enable=NO
#
# Uncomment this to allow local users to log in.
local_enable=YES
#
# Uncomment this to enable any form of FTP write command.
write_enable=YES
#
# Default umask for local users is 077. You may wish to change this to 022,
# if your users expect that (022 is used by most other ftpd's)
local_umask=022
#
# Uncomment this to allow the anonymous FTP user to upload files. This only
# has an effect if the above global write enable is activated. Also, you will
# obviously need to create a directory writable by the FTP user.
#anon_upload_enable=YES
#
# Uncomment this if you want the anonymous FTP user to be able to create
# new directories.
#anon_mkdir_write_enable=YES
#
# Activate directory messages - messages given to remote users when they
# go into a certain directory.
dirmessage_enable=YES
#
# The target log file can be vsftpd_log_file or xferlog_file.
# This depends on setting xferlog_std_format parameter
xferlog_enable=YES
#
# Make sure PORT transfer connections originate from port 20 (ftp-data).
connect_from_port_20=YES
#
# If you want, you can arrange for uploaded anonymous files to be owned by
# a different user. Note! Using "root" for uploaded files is not
# recommended!
#chown_uploads=YES
#chown_username=whoever
#
# The name of log file when xferlog_enable=YES and xferlog_std_format=YES
# WARNING - changing this filename affects /etc/logrotate.d/vsftpd.log
#xferlog_file=/var/log/xferlog
#
# Switches between logging into vsftpd_log_file and xferlog_file files.
# NO writes to vsftpd_log_file, YES to xferlog_file
xferlog_std_format=YES
#
# You may change the default value for timing out an idle session.
#idle_session_timeout=600
#
# You may change the default value for timing out a data connection.
#data_connection_timeout=120
#
# It is recommended that you define on your system a unique user which the
# ftp server can use as a totally isolated and unprivileged user.
#nopriv_user=ftpsecure
#
# Enable this and the server will recognise asynchronous ABOR requests. Not
# recommended for security (the code is non-trivial). Not enabling it,
# however, may confuse older FTP clients.
#async_abor_enable=YES
#
# By default the server will pretend to allow ASCII mode but in fact ignore
# the request. Turn on the below options to have the server actually do ASCII
# mangling on files when in ASCII mode.
# Beware that on some FTP servers, ASCII support allows a denial of service
# attack (DoS) via the command "SIZE /big/file" in ASCII mode. vsftpd
# predicted this attack and has always been safe, reporting the size of the
# raw file.
# ASCII mangling is a horrible feature of the protocol.
#ascii_upload_enable=YES
#ascii_download_enable=YES
#
# You may fully customise the login banner string:
#ftpd_banner=Welcome to blah FTP service.
#
# You may specify a file of disallowed anonymous e-mail addresses. Apparently
# useful for combatting certain DoS attacks.
#deny_email_enable=YES
# (default follows)
#banned_email_file=/etc/vsftpd/banned_emails
#
# You may specify an explicit list of local users to chroot() to their home
# directory. If chroot_local_user is YES, then this list becomes a list of
# users to NOT chroot().
#chroot_list_enable=YES
# (default follows)
#chroot_list_file=/etc/vsftpd/chroot_list
#
# You may activate the "-R" option to the builtin ls. This is disabled by
# default to avoid remote users being able to cause excessive I/O on large
# sites. However, some broken FTP clients such as "ncftp" and "mirror" assume
# the presence of the "-R" option, so there is a strong case for enabling it.
#ls_recurse_enable=YES
#
# When "listen" directive is enabled, vsftpd runs in standalone mode and
# listens on IPv4 sockets. This directive cannot be used in conjunction
# with the listen_ipv6 directive.
listen=YES
listen_port=56880
pasv_min_port=30000
pasv_max_port=35000

#
# This directive enables listening on IPv6 sockets. To listen on IPv4 and IPv6
# sockets, you must run two copies of vsftpd whith two configuration files.
# Make sure, that one of the listen options is commented !!
#listen_ipv6=YES

pam_service_name=vsftpd.vu
#pam_service_name=vsftpd
userlist_enable=YES
tcp_wrappers=YES

chroot_local_user=YES
guest_enable=YES
guest_username=virtual

 

virtual_use_local_privs=YES
#reverse_lookup_enable=NO
user_config_dir=/etc/vsftpd/vsftpd_user_conf

四、生成vsftpd虚拟用户数据库文件：

1、建立虚拟用户名单文件：
vim /etc/vsftpd/ftpuser.txt
内容如下：
ftp1
1234
ftp2
5678
格式很简单：“一行用户名，一行密码！”。

2、生成虚拟用户数据文件：

db_load -T -t hash -f /etc/vsftpd/ftpuser.txt /etc/vsftpd/vsftpd_login.db
chmod 600 /etc/vsftpd/vsftpd_login.db

五、配置PAM验证文件：
vim /etc/pam.d/vsftpd.vu

将以下内容加入到文件最前面（在后面加入无效）：
32位系统：

auth required /lib/security/pam_userdb.so db=/etc/vsftpd/vsftpd_login
account required /lib/security/pam_userdb.so db=/etc/vsftpd/vsftpd_login

64位系统：

auth required /lib64/security/pam_userdb.so db=/etc/vsftpd/vsftpd_login
account required /lib64/security/pam_userdb.so db=/etc/vsftpd/vsftpd_login

上一步建立的数据库 vsftpd_login 在此处被使用，建立的虚拟用户将采用PAM进行验证，这是通过/etc/vsftpd/vsftpd.conf文件中的语句pam_service_name=vsftpd.vu来启用的。

六、vsftpd虚拟用户的独立配置：

mkdir -p /etc/vsftpd/vsftpd_user_conf
vim /etc/vsftpd/vsftpd_user_conf/jtxm

配置如下：

anon_world_readable_only=NO
write_enable=YES
anon_upload_enable=YES
anon_mkdir_write_enable=YES
anon_other_write_enable=YES
local_root=/var/www/html/jtxm

七、vsftpd服务器之间的站点对传：
有时候可能需要开启vsftpd服务器之间的站点对传功能，只需在主配置文件 /etc/vsftpd/vsftpd.conf 里加入如下参数即可：

pasv_promiscuous=YES
port_promiscuous=YES

说明：
port_promiscuous=YES|NO
默认值为NO。为YES时，取消PORT安全检查。该检查确保外出的数据只能连接到客户端上。小心打开此选项。

pasv_promiscuous=YES|NO
默认值为NO。为YES时，将关闭PASV模式的安全检查。该检查确保数据连接和控制连接是来自同一个IP地址。小心打开此选项。此选项唯一合理的用法是存在于由安全隧道方案构成的组织中。
由于取消了数据包的安全检查，允许数据流向非客户端，所以站点对传成功。

配置修改完成后，重启vsftpd服务生效：
/etc/init.d/vsftpd restart

总结：以上配置是经过我多次验证的，如果出现问题，请从以下几方面检查：
1、文件权限和文件属主问题；
2、防火墙iptables没开放相关的端口；
3、SELinux导致的权限问题，建议先关闭SELinux再配置ftp，之后再开启到permissive模式。或者运行这条命令：setsebool -P ftp_home_dir=1 。