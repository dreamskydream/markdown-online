git的使用
==

添加ssh公钥：
--
查看~/.ssh/id_rsa.pub文件，如果有公钥直接添加，没有的话使用这个命令：
```
$ ssh-keygen -t ed25519 -C "xxxxx@xxxxx.com"
```
三次回车后，再查看~/.ssh/id_rsa.pub文件，添加到gitee设置。

创建版本库：
--
不建议直接生成，可以在gitee网站下建立仓库，之后clone到本地，直接使用下面的命令操作就可以了。
```
$ git init
```
这个命令可以把当前目录变成git管理的仓库。
远程库如果是空的，我们第一次推送master分支时，加上了-u参数，Git不但会把本地的master分支内容推送的远程新的master分支，还会把本地的master分支和远程的master分支关联起来，在以后的推送或者拉取时就可以简化命令。

添加远程仓库：
--
```
$ git remote add gitee git@gitee.com:wiseai/wiseai.git
$ git remote add github git@github.com:wiseai/wiseai.git
```

查看远程仓库信息：
--
```
$ git remote -v
```

删除远程仓库：
--
```
$ git remote rm github
```

推送到GitHub或Gitee:
--
```
$ git push gitee master
$ git push github master
```
该操作在后面内容同步后再推送。

设置邮件和用户名：
--
```
$ git config --global user.email "you@example.com"
$ git config --global user.name "Your Name"
```
全局设置使用--global参数，如果仅在本仓库设置身份标识，则省略 --global 参数。

查看git的修改状态：
--
```
$ git status
```

查看具体修改内容：
--
```
$ git diff readme.txt
```

添加修改文件：
--
```
$ git add readme.txt
```
也可以提交文件，但是空目录不显示

提交文件修改：
--

```
$ git commit -m "修改说明"
```
该操作提交所有add的文件和目录

丢弃没有add的修改：
--
```
$ git checkout -- readme.txt
```

丢弃已经add文件的修改：
--
```
$ git reset HEAD readme.txt
```

删除文件：
--
先从本地删除文件，之后
```
$ git rm test.txt
$ git commit -m "修改说明"
```
文件已删除。恢复使用：
```
$ git checkout -- test.txt
```

将远程仓库克隆到本地：
--
```
$ git clone git@gitee.com:wiseai/wiseai.git
```
克隆可以使用Https或者ssh等，具体可以看网站。
使用ssh必有添加公钥才行。