使用OpenSSL生成证书
==

下载安装OpenSSL，进入/bin/下面，执行命令（把ssl目录下的openssl.cnf 拷贝到bin目录下）

1.首先要生成服务器端的私钥(key文件):

openssl genrsa -des3 -out server.key 1024

[root@airwaySSL openssl]# cd ssl/

[root@airwaySSL ssl]# pwd

/home/openssl/ssl

[root@airwaySSL ssl]# ls

certs  man  misc  openssl.cnf  private  server.csr  server.key

运行时会提示输入密码,此密码用于加密key文件(参数des3便是指加密算法,当然也可以选用其他你认为安全的算法.),以后每当需读取此文件(通过openssl提供的命令或API)都需输入口令.如果觉得不方便,也可以去除这个口令,但一定要采取其他的保护措施!

去除key文件口令的命令:

openssl rsa -in server.key -out server.key

2.openssl req -new -key server.key -out server.csr -config openssl.cnf

[root@airwaySSL bin]# openssl req -new -key server.key -out server.csr -config openssl.cnf

Enter pass phrase for server.key:12345

You are about to be asked to enter information that will be incorporated

into your certificate request.

What you are about to enter is what is called a Distinguished Name or a DN.

There are quite a few fields but you can leave some blank

For some fields there will be a default value,

If you enter '.', the field will be left blank.

-----

Country Name (2 letter code) [AU]:CN

State or Province Name (full name) [Some-State]:china

Locality Name (eg, city) []:wuhan

Organization Name (eg, company) [Internet Widgits Pty Ltd]:airway

Organizational Unit Name (eg, section) []:airway

Common Name (eg, YOUR name) []:airway

Email Address []:

Please enter the following 'extra' attributes

to be sent with your certificate request

A challenge password []:

An optional company name []:

生成Certificate Signing Request（CSR）,生成的csr文件交给CA签名后形成服务端自己的证书.屏幕上将有提示,依照其指示一步一步输入要求的个人信息即可.

3.对客户端也作同样的命令生成key及csr文件:

openssl genrsa -des3 -out client.key 1024

Generating RSA private key, 1024 bit long modulus

...........++++++

..++++++

e is 65537 (0x10001)

Enter pass phrase for client.key:12345

Verifying - Enter pass phrase for client.key:12345

openssl req -new -key client.key -out client.csr -config openssl.cnf

[root@airwaySSL bin]# openssl req -new -key client.key -out client.csr -config openssl.cnf

Enter pass phrase for client.key:12345

You are about to be asked to enter information that will be incorporated

into your certificate request.

What you are about to enter is what is called a Distinguished Name or a DN.

There are quite a few fields but you can leave some blank

For some fields there will be a default value,

If you enter '.', the field will be left blank.

-----

Country Name (2 letter code) [AU]:cn

State or Province Name (full name) [Some-State]:china

Locality Name (eg, city) []:wuhan

Organization Name (eg, company) [Internet Widgits Pty Ltd]:airway

Organizational Unit Name (eg, section) []:airway

Common Name (eg, YOUR name) []:airway

Email Address []:

Please enter the following 'extra' attributes

to be sent with your certificate request

A challenge password []:

An optional company name []:

4.CSR文件必须有CA的签名才可形成证书.可将此文件发送到verisign等地方由它验证,要交一大笔钱,何不自己做CA呢.

openssl req -new -x509 -keyout ca.key -out ca.crt -config openssl.cnf

[root@airwaySSL bin]# openssl req -new -x509 -keyout ca.key -out ca.crt -config openssl.cnf

Generating a 1024 bit RSA private key

...++++++

...................++++++

writing new private key to 'ca.key'

Enter PEM pass phrase:12345

Verifying - Enter PEM pass phrase:

-----

You are about to be asked to enter information that will be incorporated

into your certificate request.

What you are about to enter is what is called a Distinguished Name or a DN.

There are quite a few fields but you can leave some blank

For some fields there will be a default value,

If you enter '.', the field will be left blank.

-----

Country Name (2 letter code) [AU]:CN

State or Province Name (full name) [Some-State]:china

Locality Name (eg, city) []:wuhan

Organization Name (eg, company) [Internet Widgits Pty Ltd]:airway

Organizational Unit Name (eg, section) []:airway

Common Name (eg, YOUR name) []:airway

Email Address []:

在继续下面操作前，将openssl.conf文件打开，查看其dir路径将其修改为dir = /home/openssl/bin/demoCA /，否则下面的步骤会提示路径无法找到。

自己手动创建一个CA目录结构：

[weigw@TEST bin]$ mkdir ./demoCA

[weigw@TEST bin]$ mkdir