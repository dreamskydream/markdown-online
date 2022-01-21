build命令
==

`go build`，是我们非常常用的命令，它可以启动编译，把我们的包和相关的依赖编译成一个可执行的文件。

`usage: go build [-o output] [-i] [build flags] [packages]`

`go build`的使用比较简洁，所有的参数都可以忽略，直到只有`go build`，这个时候意味着使用当前目录进行编译，下面的几条命令是等价的：
```
go build

go build .

go build hello.go
```
以上这三种写法，都是使用当前目录编译的意思。因为我们忽略了`packages`,所以自然就使用当前目录进行编译了。从这里我们也可以推测出，`go build`本质上需要的是一个路径，让编译器可以找到哪些需要编译的go文件。`packages`其实是一个相对路径，是相对于我们定义的`GOROOT`和`GOPATH`这两个环境变量的，所以有了`packages`这个参数后，`go build`就可以知道哪些需要编译的go文件了。

`go build flysnow.org/tools`

这种方式是指定包的方式，这样会明确地编译我们这个包。当然我们也可以使用通配符

`go build flysnow.org/tools/...`

** 3个点表示匹配所有字符串，这样`go build`就会编译tools目录下的所有包。**

讲到`go build`编译，不能不提跨平台编译，Go提供了编译链工具，可以让我们在任何一个开发平台上，编译出其他平台的可执行文件。

默认情况下，都是根据我们当前的机器生成的可执行文件，比如你的是Linux 64位，就会生成Linux 64位下的可执行文件，比如我的Mac；可以使用go env查看编译环境,以下截取重要的部分。
```
go env
GOARCH="amd64"
GOEXE=""
GOHOSTARCH="amd64"
GOHOSTOS="darwin"
GOOS="darwin"
GOROOT="/usr/local/go"
GOTOOLDIR="/usr/local/go/pkg/tool/darwin_amd64"
```
** 注意里面两个重要的环境变量GOOS和GOARCH,其中GOOS指的是目标操作系统，它的可用值为：**
1.  darwin
2.  freebsd
3.  linux
4.  windows
5.  android
6.  dragonfly
7.  netbsd
8.  openbsd
9.  plan9
10.  solaris

** 一共支持10种操作系统。GOARCH指的是目标处理器的架构，目前支持的有：**

1.  arm
2.  arm64
3.  386
4.  amd64
5.  ppc64
6.  ppc64le
7.  mips64
8.  mips64le
9.  s390x

一共支持9种处理器的架构

如果我们要生成不同平台架构的可执行程序，只要改变这两个环境变量就可以了，比如要生成Linux 64位的程序，命令如下：

`GOOS=linux GOARCH=amd64 go build flysnow.org/hello`

前面两个赋值，是更改环境变量，这样的好处是只针对本次运行有效，不会更改我们默认的配置。

`go help build`
