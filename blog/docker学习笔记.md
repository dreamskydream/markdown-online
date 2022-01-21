docker学习笔记
==

现在docker分企业版和社区版，如果自己用的话，还是社区版吧！！！

安装：
--

centos7：

1.卸载旧版本

``` 
$ yum remove docker \
             docker-client \
             docker-client-latest \
             docker-common \
             docker-latest \
             docker-latest-logrotate \
             docker-logrotate \
             docker-selinux \
             docker-engine-selinux \
             docker-engine
 ```

2.安装依赖包：

`$ yum install -y yum-utils device-mapper-persistent-data lvm2`

3.安装docker官方库：

`$ yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo`

4.安装最新稳定版本docker：

`$ yum install docker-ce`

如果提示接受GPG密钥，请验证指纹是否匹配`060A 61C5 1B55 8A7F 742B 77AA C52F EB6B 621E 9F35`，如果匹配 ，则接受它。

ubuntu:
``` 
    $ sudo apt-get update
    $ sudo apt-get install apt-transport-https ca-certificates curl software-properties-common
    $ curl -fsSL https://download.docker.com/linux/ubuntu/gpg 'sudo pt-key add -'
    $ sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
    $ sudo apt-get update
```

其它发行版本的请参考：[官方文档](https://docs.docker.com/install/linux/docker-ce/ubuntu/#os-requirements)

配置：

1.Linux更改Docker运行根目录

Docker 的配置文件可以设置大部分的后台进程参数，在各个操作系统中的存放位置不一致:

在 ubuntu 中的位置是：/etc/default/docker

在 centos 中的位置是：/etc/sysconfig/docker

只需要更改 vim /etc/sysconfig/docker 成

为OPTIONS选项添加参数：--graph=/home/docker --icc=false --graph=/your/dir

--icc=false禁用容器内部通信，为安全建议添加，内部通信使用link

--graph=/your/dir 自定义运行目录

或者使用软件链接：

`ln -sv /home/docker/ /var/lib/docker`

搞定！

2.iptables设置：

`# iptables -A INPUT -s 172.17.0.0/16 -d 172.17.0.0/16 -j ACCEPT`

命令：

查找镜像：docker search centos

下载镜像：docker pull centos

列出本地镜像：docker images

删除镜像：docker rmi 镜像名

运行容器：
<pre>docker run -it --name 容器名 --link 链接容器名:别名 -v 本地绝对路径:容器路径  -p ip:本地端口:容器端口/udp -e --restart=always （docker服务启动后容器自动启动，no 不启动；on-failure 容器退出状态非0时重启）MYSQL_ROOT_PASSWORD<span class="hljs-subst">=</span>'passwd'(设置环境变量） --dns=自定义的DNS -d(后台运行) 镜像 运行命令</pre>

    列出运行容器：docker container ls
    列出所有容器：docker container ls -a
    运行容器：docker start 容器名（-i参数进入命令行）
    进入运行容器：docker exec -it 容器名 /bin/bash
    停止容器：docker stop 容器名
    kill容器：docker kill 容器名
    删除容器：docker rm 容器名
    查看容器信息：docker inspect 容器名
    利用已有容器修改或建立新的镜像：docker commit -m "说明信息" -a "用户信息" 容器名 centos7/nginx-php:v2
    镜像存出：docker save -o ubuntu_14.04.tar ubuntu:14.04
    镜像载入：docker load --input ubuntu_14.04.tar或docker load < ubuntu_14.04.tar

从主机复制到容器`docker cp host_path containerID:container_path`

从容器复制到主机`docker cp containerID:container_path host_path`

## docker网络设置：

docker安装后，默认会创建三种网络类型，bridge、host和none，可通过如下命令查看：
<pre>docker network ls</pre>
**bridge：网络桥接**
默认情况下启动、创建容器都是用该模式，所以每次docker容器重启时会按照顺序获取对应IP地址，这就导致容器每次重启，IP都发生变化，这种类型下无法设置固定IP
**none：无指定网络**
启动容器时，可以通过-network=none，docker容器不会分配局域网ip
**host：主机网络**
docker容器的网络会附属在主机上，两者是互通的。这种类型下可以设置固定IP

### 创建固定ip容器：

#### 1.创建自定义网络类型，并且指定网段

<pre>docker network create --subnet=192.168.168.0/30 mynet</pre>
通过docker network ls可以查看到网络类型中多了一个mynet

#### 2.使用新的网络类型创建并启动容器

<pre>docker run -it --name test --net mynet --ip 192.168.168.2 centos /bin/bash</pre>

## 存在问题：

docker-storage-setup不能启动的问题。

编辑/etc/sysconfig/docker-storage-setup文件

添加STORAGE_DRIVER="overlay"

容器启动自动运行

编辑/etc/bashrc文件

添加运行命令在文件最后