grub2相关配置文件及命令
==

### 一、配这里是列表文本置文件目录
/etc/grub2/目录下

### 二、更新引导项目
根据配置文件目录下的顺序更新启动项，命令：

`# grub2-mkconfig -o /boot/efi/EFI/centos/grub.cfg`

-o 选项为输出到那个文件

eufi输出到/boot/efi/EFI/centos/grub.cfg

### 三、更换默认启动项
命令：

`# grub2-set-default 2`

一般从0开始