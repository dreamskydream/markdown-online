adduser&addgroup添加用户&用户组
==

* 添加一个普通用户
`# adduser [--home 主目录] [--shell SHELL] [--no-create-home] [--uid ID] [--firstuid ID] [--lastuid ID] [--gecos GECOS] [--ingroup 用户组 | --gid ID][--disabled-password] [--disabled-login] [--add_extra_groups] 用户名`
* 添加一个系统用户
`# adduser --system [--home 主目录] [--shell SHELL] [--no-create-home] [--uid ID] [--gecos GECOS] [--group | --ingroup 用户组 | --gid ID] [--disabled-password] [--disabled-login] [--add_extra_groups] 用户名`
* 添加一个用户组
`# adduser --group [--gid ID] 用户组名`
`# addgroup [--gid ID] 用户组名`
* 添加一个系统用户组
`# addgroup --system [--gid ID] 用户组名`
* 将一个已存在的用户添加至一个已存在的用户组
`# adduser 用户名 用户组名`
* 常规设置：
```
--quiet | -q          不在标准输出中给出进度信息
  --force-badname       允许用户名不匹配：
                        NAME_REGEX[_SYSTEM] 配置变量
  --help | -h           给出本命令用法
  --version | -v        版本号和版权信息
  --conf | -c 文件      使用文件中的配置