Linux开机启动的方法：
==

一、配置rc.local文件实现开机启动：
--

首先，建立一个文件：

` $ sudo touch /etc/rc.local `

给予可执行权限：

` $ sudo chmod +x /etc/rc.local `

在文件写入以下内容：
```
#!/bin/sh

/path/to/gohttpserver -r /path/to/ --port 8080 --upload
```

以上两个/path/to/，一个是文件所在目录，一个是工作目录，都使用绝对路径。
完成！！！

二、第二种方法以后有时间再补充。
--
