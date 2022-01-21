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