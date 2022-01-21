filebrowser的安装和使用
==
编译安装
--
可以自己编译，必须安装golang v1.16以上和nodejs，最好是最新版本，否则会出现一些问题。
```
$ git clone https://github.com/filebrowser/filebrowser
```
注：如果有gitee的可以改，速度快

```
$ make help
```
注：编译帮助

```
$ make build
```
注：开始编译，完成后在当前目录下filebrowser


使用简介：
--
直接输入命令：
`./filebrowser`
会在当前目录下生成数据库文件，并开启服务

关闭服务后，输入以下命令进行设置：

`./filebrowser config cat`查看配置文件，这个配置是写入数据库里的

`./filebrowser config set --address 0.0.0.0`打开所有端口

这里的`config set --address`可以从配置文件里看，想设置那个就把address换成那个

`./filebrowser  config set --port 80`设置端口

`./filebrowser  config set --locale zh-cn`设置语言

`./filebrowser  config set --log /your/path/filebrowser.log`设置日志

`./filebrowser  config set --root /your/path/`设置根目录

`./filebrowser  users add root password --perm.admin`这个是添加用户，其中的root和password分别是用户名和密码

`./filebrowser -d /you/path/filebrowser.db config init`这里的-d是指定数据库文件，config init是初始化，这个命令应该一开始就执行，但是什么参数都不带的话，会自己生成。

设置https访问：

`./filebrowser  config set --port 443`设置https端口

`./filebrowser/filebrowser config set -t "/path/to/***.pem"`设置证书

`./filebrowser/filebrowser config set -k "/path/to/***.key"`设置key

设置系统服务：位置`/lib/systemd/system/filebrowser.service`
```[Unit]
Description=File Browser
After=network.target

[Service]
ExecStart=/usr/local/bin/filebrowser -d /your/path/filebrowser.db

[Install]
WantedBy=multi-user.target```