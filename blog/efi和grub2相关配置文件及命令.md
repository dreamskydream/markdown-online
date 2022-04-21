efi和grub2相关配置文件及命令
==

### 一、配置文件目录
/etc/grub2/目录下
/etc/default/grub

### 二、更新引导项目
根据配置文件目录下的顺序更新启动项，命令：

`# grub2-mkconfig -o /boot/grub2/grub.cfg`

-o 选项为输出到那个文件

efi下的/boot/efi/EFI/centos/grub.cfg文件一般调用/boot/grub2/grub.cfg这个文件

类debian发行版，一般用这个命令：
`# update-grub2`

### 三、更换默认启动项
命令：

`# grub2-set-default 2`

一般从0开始

### 四、安装
`# grub-install --efi-directory=/boot/efi/ --boot-directory=/boot/ --removable /dev/sda`
几个需要说明的地方：
> `--efi-directory=/boot/efi/`这个位置的问题，如果efi分区是单独挂载的，就直接写这个目录就可以了，不用加efi，它会在这个目录下建立EFI的目录，写入相关文件。
`--boot-directory=/boot/`这个不用加grub2，它会自己生成grub2目录，写入相关文件
`--removable`如果是移动设备，一定要加这个选项

### 五、常用命令
- 启动常用的grub命令
```
root (hd0,2)          #设置某块分区为根目录
kernel /vmlinuz       #加载linux kernel
initrd /initrd.gz     #加载驱动，或者虚拟rootfs
boot                  #启动加载的kernel
```
- 查找文件
`find --set-root --ignore-floppies --ignore-cd /usr/bin/cp   #查找/usr/bin/cp文件，并将分区设为根目录，忽略软盘和cd`
- 加载iso
```
root (hdx,x)                    #设置根目录
map --mem /xx.iso (0xff)        #映射iso文件到模拟cdrom 0xff
map --hook                      #映射钩子
chainloader (0xff)              #链接到映射的CDROM
boot                            #启动
```
grub命名规则，(hd32)为第一仿真cd/dvd设备，等同于0xa0，(hd33)为二仿真cd/dvd设备，0xff为最后一个。grub中将0xa0以上都认做cdrom。
`map --mem` 用于仿真不连续的存放的iso文件，不加则必须连续存放，碎片整理后再存放iso一般才会连续。
另外grub不支持大的iso，如果iso不支持则提示:inviladorupsupported executableformat。

### 六、添加efi启动项
在安装程序不完善的时候，有时候会存在没有启动项的问题，这个时候就要用efibootmgr管理UEFI启动项。
`# efibootmgr`
这个命令会显示所有已经添加的启动项，BootOrder后会显示启动顺序。
`# efibootmgr -c -w -L "BootOptionName" -d /dev/sda -p 1 -l \\EFI\\UOS\\grubx64.efi`
	BootOptionName是你启动项的名字，修改为自己的
	-d修改那个硬盘
	-p分区位置，默认为1
	-l是启动efi文件的路径，注意是\\而不是//
这样就添加了EFI启动项，刚添加的启动项的顺序排第一个
`# efibootmgr -b 0013 -B`
删除编号为0013的启动项
`# efibootmgr -o 0012,0010,000f`
修改启动顺序。
这个命令没有认真研究，有空再说吧。

### 七、需要注意的地方
	grub背景图片的问题，最近用gimp做了个图片设为grub2启动背景图片，grub2提示错误，最后发现是图片的问题。