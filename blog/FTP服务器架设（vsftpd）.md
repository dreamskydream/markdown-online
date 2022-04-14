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

这个档案是否能够生效与 vsftpd.conf 内的两个参数有关,分别是『 userlist_enable, userlist_deny 』。 如果说 /etc/vsftpd/ftpusers 是PAM 模块的抵挡设定项目,那么这个 /etc/vsftpd/user_list 则是 vsftpd 自定义的抵挡项目。事实上这个档案与 /etc/vsftpd/ftpusers 几乎一模一样, 在预设的情况下,你可以将不希望可登入 vsftpd 的账号写入这里。不过这个档案的功能会依据 vsftpd.conf 配置文件内的 serlist_deny={YES/NO} 而不同。

/etc/vsftpd/chroot_list

这个档案预设是不存在的,所以你必须要手动自行建立。这个档案的主要功能是可以将某些账号的使用者 chroot 在他们的家目录下!但这个档案要生效与vsftpd.conf 内的『 chroot_list_enable, chroot_list_file 』两个参数有关。如果你想要将某些实体用户限制在他们的家目录下而不许到其他目录去,可以启动这个设定项目。

/usr/sbin/vsftpd

这就是 vsftpd 的主要执行档，vsftpd 只有这一个执行档。

/var/ftp/

这个是 vsftpd 的预设匿名者登入的根目录，其实与 ftp 这个账号的家目录有关。

!!服务器环境设定
```
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
```


网上一个详细的教程，值得参考
==

### 一、安装vsftpd及相关组件：
`# yum -y install vsftpd db5.3-util`
注意：这里的db5.3在系统更新的时候可以会变名字，所以用`apt list db*`查找下再安装。

### 二、修改FTP相关帐户：

1. vsftpd服务的宿主用户
`# useradd vsftpd -s /sbin/nologin`
默认的vsftpd的服务宿主用户是root，但是这不符合安全性的需要。这里建立名字为vsftpd的用户，用他来作为支持vsftpd的服务宿主用户。由于该用户仅用来支持vsftpd服务用，因此没有许可他登陆系统的必要，并设定他为不能登陆系统的用户。
这一步现在可能不需要了，先放着。

1. vsftpd的虚拟宿主用户
`# useradd ftp -d /var/www/html/ -s /sbin/nologin`
`# chown -R ftp:ftp /var/www/html/`
vsftpd的虚拟用户并不是系统用户，也就是说这些FTP的用户在系统中是不存在的。他们的总体权限其实是集中寄托在一个在系统中的某一个用户身上的，所谓vsftpd的虚拟宿主用户，就是这样一个支持着所有虚拟用户的宿主用户。由于他支撑了FTP的所有虚拟的用户，那么他本身的权限将会影响着这些虚拟的用户，因此，处于安全性的考虑，也要非分注意对该用户的权限的控制，该用户也绝对没有登陆系统的必要，这里也设定他为不能登陆系统的用户。ftp用户在安装vsftpd的时候就已经添加了，我们只需要将它

三、vsftpd.conf基本配置：
配置 /etc/vsftpd/vsftpd.conf文件，这里我们只写有变动的地方，其它的保持默认。

```
#ssl加密传输
rsa_cert_file=/etc/ssl/certs/ssl-cert-snakeoil.pem
rsa_private_key_file=/etc/ssl/private/ssl-cert-snakeoil.key
ssl_enable=YES

#被动ftp
#listen_port=56880
#pasv_min_port=30000
#pasv_max_port=35000

#主动ftp，这个只需要打开20和21两个端口，在负载比较小的时候，不想开多个端口的时候，可以使用
pasv_enable=NO

#这个地方一定要注意，被卡这儿好久
userlist_enable=YES
userlist_deny=NO
userlist_file=/etc/vsftpd/user_list

tcp_wrappers=YES

chroot_local_user=YES
chroot_list_file=/etc/vsftpd/chroot_list

#虚拟用户
guest_enable=YES
guest_username=ftp
virtual_use_local_privs=YES
pam_service_name=vsftpd

#独立用户设置
user_config_dir=/etc/vsftpd/vsftpd_user_conf

```
### 四、生成vsftpd虚拟用户数据库文件：

1. 建立虚拟用户名单文件：
`# vim /etc/vsftpd/ftpuser.txt`
内容如下：
ftp1
1234
ftp2
5678
格式很简单：“一行用户名，一行密码！”。

1. 生成虚拟用户数据文件：
`# db_load -T -t hash -f /etc/vsftpd/ftpuser.txt /etc/vsftpd/vsftpd_login.db`
`# chmod 600 /etc/vsftpd/vsftpd_login.db`这一步可以省略，文件权限就是这个

### 五、配置PAM验证文件：
`# vim /etc/pam.d/vsftpd`
将以下内容加入到文件最前面（在后面加入无效）：
> 注意：pam_userdb.so这个文件可以搜索下位置写入，不同系统的文件位置不同

```
auth    required        /usr/lib/aarch64-linux-gnu/security/pam_userdb.so db=/etc/vsftpd/vsftpd_login
account required        /usr/lib/aarch64-linux-gnu/security/pam_userdb.so db=/etc/vsftpd/vsftpd_login
auth    required        pam_listfile.so item=user sense=deny file=/etc/vsftpd/ftpusers onerr=succeed
```
经过测试，这样写也行：
```
auth    required        pam_userdb.so db=/etc/vsftpd/vsftpd_login
account required        pam_userdb.so db=/etc/vsftpd/vsftpd_login
auth    required        pam_listfile.so item=user sense=deny file=/etc/vsftpd/ftpusers onerr=succeed
```
ftpusers的原位置在/etc/下，移动ftpusers到/etc/vsftpd/下，这个文件中的用户是不能登陆ftp的。
上一步建立的数据库 vsftpd_login 在此处被使用，建立的虚拟用户将采用PAM进行验证，这是通过/etc/vsftpd/vsftpd.conf文件中的语句pam_service_name=vsftpd来启用的。

### 六、vsftpd虚拟用户的独立配置：
`# mkdir -p /etc/vsftpd/vsftpd_user_conf`
`# vim /etc/vsftpd/vsftpd_user_conf/用户名`

配置如下：
	allow_writeable_chroot=YES
	anon_world_readable_only=NO
	write_enable=YES
	anon_upload_enable=YES
	anon_mkdir_write_enable=YES
	anon_other_write_enable=YES
	local_root=/var/ftp/

### 七、vsftpd服务器之间的站点对传：
有时候可能需要开启vsftpd服务器之间的站点对传功能，只需在主配置文件 /etc/vsftpd/vsftpd.conf 里加入如下参数即可：

	pasv_promiscuous=YES
	port_promiscuous=YES

说明：
port_promiscuous=YES|NO
默认值为NO。为YES时，取消PORT安全检查。该检查确保外出的数据只能连接到客户端上。小心打开此选项。

pasv_promiscuous=YES|NO
默认值为NO。为YES时，将关闭PASV模式的安全检查。该检查确保数据连接和控制连接是来自同一个IP地址。小心打开此选项。此选项唯一合理的用法是存在于由安全隧道方案构成的组织中。
由于取消了数据包的安全检查，允许数据流向非客户端，所以站点对传成功。

### 八、/etc/vsftpd/目录下的文件有：
chroot_list 
ftpuser.txt   
ftpusers  
user_list  
vsftpd_login.db  
vsftpd_user_conf这个是目录下面是各个用户的配置文件
### 配置修改完成后，重启vsftpd服务生效：
#结束

[![vsftpd配置](vsftpd配置 "vsftpd配置")](https://md.wiseai.cn/blog/vsftpd.png "vsftpd配置")
vsftpd配置文件详解

1.默认配置：
1>允许匿名用户和本地用户登陆。
anonymous_enable=YES
local_enable=YES
2>匿名用户使用的登陆名为ftp或anonymous，口令为空；匿名用户不能离开匿名 用户家目录/var/ftp,且只能下载不能上传。
3>本地用户的登录名为本地用户名，口令为此本地用户的口令；本地用户可以在自己家目录中进行读写操作；本地用户可以离开自家目录切换至有权限访问的其他目录，并在权限允许的情况下进行上传/下载。
write_enable=YES
4>写在文件/etc/vsftpd.ftpusers中的本地用户禁止登陆。

2.配置文件格式：
vsftpd.conf 的内容非常单纯，每一行即为一项设定。若是空白行或是开头为#的一行，将会被忽略。内容的格式只有一种，如下所示
option=value
要注意的是，等号两边不能加空白。

3.匿名用户（anonymous）设置
anonymous_enable=YES/NO（YES）
控制是否允许匿名用户登入，YES 为允许匿名登入，NO 为不允许。默认值为YES。
write_enable=YES/NO（YES）
是否允许登陆用户有写权限。属于全局设置，默认值为YES。
no_anon_password=YES/NO（NO）
若是启动这项功能，则使用匿名登入时，不会询问密码。默认值为NO。
ftp_username=ftp
定义匿名登入的使用者名称。默认值为ftp。
anon_root=/var/ftp
使用匿名登入时，所登入的目录。默认值为/var/ftp。注意ftp目录不能是777的权限属性，即匿名用户的家目录不能有777的权限。
anon_upload_enable=YES/NO（NO）
如果设为YES，则允许匿名登入者有上传文件（非目录）的权限，只有在write_enable=YES时，此项才有效。当然，匿名用户必须要有对上层目录的写入权。默认值为NO。
anon_world_readable_only=YES/NO（YES）
如果设为YES，则允许匿名登入者下载可阅读的档案（可以下载到本机阅读，不能直接在FTP服务器中打开阅读）。默认值为YES。
anon_mkdir_write_enable=YES/NO（NO）
如果设为YES，则允许匿名登入者有新增目录的权限，只有在write_enable=YES时，此项才有效。当然，匿名用户必须要有对上层目录的写入权。默认值为NO。
anon_other_write_enable=YES/NO（NO）
如 果设为YES，则允许匿名登入者更多于上传或者建立目录之外的权限，譬如删除或者重命名。（如果anon_upload_enable=NO，则匿名用户 不能上传文件，但可以删除或者重命名已经存在的文件；如果anon_mkdir_write_enable=NO，则匿名用户不能上传或者新建文件夹，但 可以删除或者重命名已经存在的文件夹。）默认值为NO。
chown_uploads=YES/NO（NO）
设置是否改变匿名用户上传文件（非目录）的属主。默认值为NO。
chown_username=username
设置匿名用户上传文件（非目录）的属主名。建议不要设置为root。
anon_umask=077
设置匿名登入者新增或上传档案时的umask 值。默认值为077，则新建档案的对应权限为700。
deny_email_enable=YES/NO（NO）
若是启动这项功能，则必须提供一个档案/etc/vsftpd/banner_emails，内容为email address。若是使用匿名登入，则会要求输入email address，若输入的email address 在此档案内，则不允许进入。默认值为NO。
banned_email_file=/etc/vsftpd/banner_emails
此文件用来输入email address，只有在deny_email_enable=YES时，才会使用到此档案。若是使用匿名登入，则会要求输入email address，若输入的email address 在此档案内，则不允许进入。

4.本地用户设置
local_enable=YES/NO（YES）
控制是否允许本地用户登入，YES 为允许本地用户登入，NO为不允许。默认值为YES。
local_root=/home/username
当本地用户登入时，将被更换到定义的目录下。默认值为各用户的家目录。
write_enable=YES/NO（YES）
是否允许登陆用户有写权限。属于全局设置，默认值为YES。
local_umask=022
本地用户新增档案时的umask 值。默认值为077。
file_open_mode=0755
本地用户上传档案后的档案权限，与chmod 所使用的数值相同。默认值为0666。

5.欢迎语设置
dirmessage_enable=YES/NO（YES）
如果启动这个选项，那么使用者第一次进入一个目录时，会检查该目录下是否有.message这个档案，如果有，则会出现此档案的内容，通常这个档案会放置欢迎话语，或是对该目录的说明。默认值为开启。
message_file=.message
设置目录消息文件，可将要显示的信息写入该文件。默认值为.message。
banner_file=/etc/vsftpd/banner
当使用者登入时，会显示此设定所在的档案内容，通常为欢迎话语或是说明。默认值为无。如果欢迎信息较多，则使用该配置项。
ftpd_banner=Welcome to BOB's FTP server
这里用来定义欢迎话语的字符串，banner_file是档案的形式，而ftpd_banner 则是字符串的形式。预设为无。

6.控制用户是否允许切换到上级目录
在默认配置下，本地用户登入FTP后可以使用cd命令切换到其他目录，这样会对系统带来安全隐患。可以通过以下三条配置文件来控制用户切换目录。
chroot_list_enable=YES/NO（NO）
设置是否启用chroot_list_file配置项指定的用户列表文件。默认值为NO。
chroot_list_file=/etc/vsftpd.chroot_list
用于指定用户列表文件，该文件用于控制哪些用户可以切换到用户家目录的上级目录。
chroot_local_user=YES/NO（NO）
用于指定用户列表文件中的用户是否允许切换到上级目录。默认值为NO。
通过搭配能实现以下几种效果：
①当chroot_list_enable=YES，chroot_local_user=YES时，在/etc/vsftpd.chroot_list文件中列出的用户，可以切换到其他目录；未在文件中列出的用户，不能切换到其他目录。
②当chroot_list_enable=YES，chroot_local_user=NO时，在/etc/vsftpd.chroot_list文件中列出的用户，不能切换到其他目录；未在文件中列出的用户，可以切换到其他目录。
③当chroot_list_enable=NO，chroot_local_user=YES时，所有的用户均不能切换到其他目录。
④当chroot_list_enable=NO，chroot_local_user=NO时，所有的用户均可以切换到其他目录。
[chroot_local_user=YES/NO ,特别注意当等于YES时，因为FTP不能切换目录，有些FTP客户端会在FTP目标目录里再新建一个目标目录，如 upload/upload  造成应用调试困扰]
7.数据传输模式设置
FTP在传输数据时，可以使用二进制方式，也可以使用ASCII模式来上传或下载数据。
ascii_upload_enable=YES/NO（NO）
设置是否启用ASCII 模式上传数据。默认值为NO。
ascii_download_enable=YES/NO（NO）
设置是否启用ASCII 模式下载数据。默认值为NO。

8.访问控制设置
两种控制方式：一种控制主机访问，另一种控制用户访问。
①控制主机访问：
tcp_wrappers=YES/NO（YES）
设 置vsftpd是否与tcp wrapper相结合来进行主机的访问控制。默认值为YES。如果启用，则vsftpd服务器会检查/etc/hosts.allow 和/etc/hosts.deny 中的设置，来决定请求连接的主机，是否允许访问该FTP服务器。这两个文件可以起到简易的防火墙功能。
比如：若要仅允许192.168.0.1—192.168.0.254的用户可以连接FTP服务器，则在/etc/hosts.allow文件中添加以下内容：
vsftpd:192.168.0. :allow
all:all :deny
②控制用户访问：
对于用户的访问控制可以通过/etc目录下的vsftpd.user_list和ftpusers文件来实现。
userlist_file=/etc/vsftpd.user_list
控制用户访问FTP的文件，里面写着用户名称。一个用户名称一行。
userlist_enable=YES/NO（NO）
是否启用vsftpd.user_list文件。
userlist_deny=YES/NO（YES）
决定vsftpd.user_list文件中的用户是否能够访问FTP服务器。若设置为YES，则vsftpd.user_list文件中的用户不允许访问FTP，若设置为NO，则只有vsftpd.user_list文件中的用户才能访问FTP。
/etc /vsftpd/ftpusers文件专门用于定义不允许访问FTP服务器的用户列表（注意:如果 userlist_enable=YES,userlist_deny=NO,此时如果在vsftpd.user_list和ftpusers中都有某个 用户时，那么这个用户是不能够访问FTP的，即ftpusers的优先级要高）。默认情况下vsftpd.user_list和ftpusers，这两个 文件已经预设置了一些不允许访问FTP服务器的系统内部账户。如果系统没有这两个文件，那么新建这两个文件，将用户添加进去即可。

9.访问速率设置
anon_max_rate=0
设置匿名登入者使用的最大传输速度，单位为B/s，0 表示不限制速度。默认值为0。
local_max_rate=0
本地用户使用的最大传输速度，单位为B/s，0 表示不限制速度。预设值为0。

10.超时时间设置
accept_timeout=60
设置建立FTP连接的超时时间，单位为秒。默认值为60。
connect_timeout=60
PORT 方式下建立数据连接的超时时间，单位为秒。默认值为60。
data_connection_timeout=120
设置建立FTP数据连接的超时时间，单位为秒。默认值为120。
idle_session_timeout=300
设置多长时间不对FTP服务器进行任何操作，则断开该FTP连接，单位为秒。默认值为300 。

11.日志文件设置
xferlog_enable= YES/NO（YES）
是否启用上传/下载日志记录。如果启用，则上传与下载的信息将被完整纪录在xferlog_file 所定义的档案中。预设为开启。
xferlog_file=/var/log/vsftpd.log
设置日志文件名和路径，默认值为/var/log/vsftpd.log。
xferlog_std_format=YES/NO（NO）
如果启用，则日志文件将会写成xferlog的标准格式，如同wu-ftpd 一般。默认值为关闭。
log_ftp_protocol=YES|NO（NO）
如果启用此选项，所有的FTP请求和响应都会被记录到日志中，默认日志文件在/var/log/vsftpd.log。启用此选项时，xferlog_std_format不能被激活。这个选项有助于调试。默认值为NO。

12.定义用户配置文件
在vsftpd中，可以通过定义用户配置文件来实现不同的用户使用不同的配置。
user_config_dir=/etc/vsftpd/userconf
设置用户配置文件所在的目录。当设置了该配置项后，用户登陆服务器后，系统就会到/etc/vsftpd/userconf目录下，读取与当前用户名相同的文件，并根据文件中的配置命令，对当前用户进行更进一步的配置。
例 如：定义user_config_dir=/etc/vsftpd/userconf，且主机上有使用者 test1,test2，那么我们就在user_config_dir 的目录新增文件名为test1和test2两个文件。若是test1 登入，则会读取user_config_dir 下的test1 这个档案内的设定。默认值为无。利用用户配置文件，可以实现对不同用户进行访问速度的控制，在各用户配置文件中定义local_max_rate=XX， 即可。

13.FTP的工作方式与端口设置
FTP有两种工作方式：PORT FTP（主动模式）和PASV FTP（被动模式）
listen_port=21
设置FTP服务器建立连接所监听的端口，默认值为21。
connect_from_port_20=YES/NO
指定FTP使用20端口进行数据传输，默认值为YES。
ftp_data_port=20
设置在PORT方式下，FTP数据连接使用的端口，默认值为20。
pasv_enable=YES/NO（YES）
若设置为YES，则使用PASV工作模式；若设置为NO，则使用PORT模式。默认值为YES，即使用PASV工作模式。
pasv_max_port=0
在PASV工作模式下，数据连接可以使用的端口范围的最大端口，0 表示任意端口。默认值为0。
pasv_min_port=0
在PASV工作模式下，数据连接可以使用的端口范围的最小端口，0 表示任意端口。默认值为0。

14.与连接相关的设置
listen=YES/NO（YES）
设 置vsftpd服务器是否以standalone模式运行。以standalone模式运行是一种较好的方式，此时listen必须设置为YES，此为默 认值。建议不要更改，有很多与服务器运行相关的配置命令，需要在此模式下才有效。若设置为NO，则vsftpd不是以独立的服务运行，要受到xinetd 服务的管控，功能上会受到限制。
max_clients=0
设置vsftpd允许的最大连接数，默认值为0，表示不受限制。若设置为100时，则同时允许有100个连接，超出的将被拒绝。只有在standalone模式运行才有效。
max_per_ip=0
设置每个IP允许与FTP服务器同时建立连接的数目。默认值为0，表示不受限制。只有在standalone模式运行才有效。
listen_address=IP地址
设置FTP服务器在指定的IP地址上侦听用户的FTP请求。若不设置，则对服务器绑定的所有IP地址进行侦听。只有在standalone模式运行才有效。
setproctitle_enable=YES/NO（NO）
设置每个与FTP服务器的连接，是否以不同的进程表现出来。默认值为NO，此时使用ps aux |grep ftp只会有一个vsftpd的进程。若设置为YES，则每个连接都会有一个vsftpd的进程。

15.虚拟用户设置
虚拟用户使用PAM认证方式。
pam_service_name=vsftpd
设置PAM使用的名称，默认值为/etc/pam.d/vsftpd。
guest_enable= YES/NO（NO）
启用虚拟用户。默认值为NO。
guest_username=ftp
这里用来映射虚拟用户。默认值为ftp。
virtual_use_local_privs=YES/NO（NO）
当该参数激活（YES）时，虚拟用户使用与本地用户相同的权限。当此参数关闭（NO）时，虚拟用户使用与匿名用户相同的权限。默认情况下此参数是关闭的（NO）。

16.其他设置
text_userdb_names= YES/NO（NO）
设置在执行ls –la之类的命令时，是显示UID、GID还是显示出具体的用户名和组名。默认值为NO，即以UID和GID方式显示。若希望显示用户名和组名，则设置为YES。
ls_recurse_enable=YES/NO（NO）
若是启用此功能，则允许登入者使用ls –R（可以查看当前目录下子目录中的文件）这个指令。默认值为NO。
hide_ids=YES/NO（NO）
如果启用此功能，所有档案的拥有者与群组都为ftp，也就是使用者登入使用ls -al之类的指令，所看到的档案拥有者跟群组均为ftp。默认值为关闭。
download_enable=YES/NO（YES）
如果设置为NO，所有的文件都不能下载到本地，文件夹不受影响。默认值为YES。