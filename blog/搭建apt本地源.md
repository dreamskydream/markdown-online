搭建apt本地源
==
## 一、安装apt-mirror命令
`# apt install apt-mirror`
## 二、 修改apt-mirror配置文件
首先， 确定自己系统的版本 ，root权限执行：

`# lsb_release -a`

我的系统是ubuntu的bionic，从阿里云下载源，一般有5个目录，前三个常用，后两个是预发布软件，不常用，我一般用前三个

```
deb http://mirrors.aliyun.com/ubuntu/ bionic main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ bionic-security main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ bionic-updates main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ bionic-proposed main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ bionic-backports main restricted universe multiverse
```
然后，编辑apt-mirror的配置文件/etc/apt/mirror.list

### 配置镜像下载目录
`set base_path /var/spool/apt-mirror`
### 架构配置，i386/amd64，默认的话会下载跟本机相同的架构的源
`set defaultarch amd64`
### 下载线程数
`set nthreads 20`
`set _tilde 0`
### 阿里云的源（这里没有添加deb-src的源码源）
```
deb http://mirrors.aliyun.com/ubuntu/ xenial main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ xenial-security main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ xenial-updates main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ xenial-proposed main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ xenial-backports main restricted universe multiverse
```
### 后面还可以添加其它版本的源

### 清理不使用的软件包
clean http://mirrors.aliyun.com/ubuntu
## 三、开始同步
`# apt-mirror`
注意：同步的文件必须在Linux文件系统下，所有人为_apt；否则出错。

## 四、 客户端配置
编辑/etc/apt/source.list
```
deb [arch=amd64] http://[host]:[port]/ubuntu/ bionic main restricted universe multiverse 
deb [arch=amd64] http://[host]:[port]/ubuntu/ bionic-security main restricted universe multiverse 
deb [arch=amd64] http://[host]:[port]/ubuntu/ bionic-updates main restricted universe multiverse 
deb [arch=amd64] http://[host]:[port]/ubuntu/ bionic-proposed main restricted universe multiverse 
deb [arch=amd64] http://[host]:[port]/ubuntu/ bionic-backports main restricted universe multiverse
或

deb [arch=amd64] file:///你的目录/ubuntu/ bionic main restricted universe multiverse 
deb [arch=amd64] file:///你的目录/ubuntu/ bionic-security main restricted universe multiverse 
deb [arch=amd64] file:///你的目录/ubuntu/ bionic-updates main restricted universe multiverse 
deb [arch=amd64] file:///你的目录/ubuntu/ bionic-proposed main restricted universe multiverse 
deb [arch=amd64] file:///你的目录/ubuntu/ bionic-backports main restricted universe multiverse
```