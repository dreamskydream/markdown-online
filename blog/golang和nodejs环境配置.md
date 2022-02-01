golang和nodejs环境配置
==
1. 配置golang
* 下载文件：[golang官网下载](https://golang.google.cn/dl/ "golang官网下载")
* 解压文件至/usr/local/目录下：
`# rm -rf /usr/local/go && tar -C /usr/local -xzf go1.17.6.linux-amd64.tar.gz`（第一个命令是删除golang的残余文件）
* 建立软链接至/usr/bin/目录下：
`# ln -s /usr/local/go/bin/go /usr/bin/`
`# ln -s /usr/local/go/bin/gofmt /usr/bin/`
* 修改为国内源
`# go env -w GO111MODULE=auto `
`# go env -w GOPROXY=https://goproxy.cn,direct`
* 查看配置
`# go env`

2. 配置nodejs
* 下载文件：[nodejs官网](https://nodejs.org/zh-cn/ "nodejs官网")
* 解压文件至/usr/local/目录下：
`# rm -rf /usr/local/go && tar -C /usr/local -xzf go1.17.6.linux-amd64.tar.gz`（第一个命令是删除nodejs的残余文件）
`# npm install yarn`
* 建立软链接至/usr/bin/目录下：
`# ln -s /usr/local/node-v16.13.2-linux-arm64/bin/node /usr/bin/`
`# ln -s /usr/local/node-v16.13.2-linux-arm64/bin/npm /usr/bin/`
`# ln -s /usr/local/node-v16.13.2-linux-arm64/bin/npx /usr/bin/`
* 修改为国内源
`# npm config set registry https://registry.npm.taobao.org`
* 查看配置
`# npm config get`