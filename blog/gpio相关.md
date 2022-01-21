GPIO相关
==
linux查看gpio状态
--
`cat /sys/kernel/debug/gpio`

有个blog里有这个方法，去试试：
```
mkdir /tmp/debug
mount -t debugfs debugfs /tmp/debug
cat /tmp/debug/gpio
```

GPIO操作的一些说明
--
- 首先，看看系统中有没有“/sys/class/gpio”这个文件夹。如果没有请在编译内核的时候加入 

Device Drivers-> GPIO Support ->/sys/class/gpio/… (sysfs interface)

- /sys/class/gpio 的使用说明：

gpio_operation 通过/sys/文件接口操作IO端口 GPIO到文件系统的映射

◇  控制GPIO的目录位于/sys/class/gpio

◇  /sys/class/gpio/export文件用于通知系统需要导出控制的GPIO引脚编号

◇  /sys/class/gpio/unexport 用于通知系统取消导出

◇  /sys/class/gpio/gpiochipX目录保存系统中GPIO寄存器的信息，包括每个寄存器控制引脚的起始编号base，寄存器名称，引脚总数 导出一个引脚的操作步骤

◇  首先计算此引脚编号，引脚编号 = 控制引脚的寄存器基数 + 控制引脚寄存器位数

◇  向/sys/class/gpio/export写入此编号，比如12号引脚，在shell中可以通过以下命令实现，`echo 12 > export`命令成功后生成/sys/class/gpio/gpio12目录，如果没有出现相应的目录，说明此引脚不可导出

◇  direction文件，定义输入输入方向，可以通过下面命令定义为输出。direction接受的参数：in, out, high, low。high/low同时设置方向为输出，并将value设置为相应的1/0

◇  value文件是端口的数值，为1或0

例子：
--
进入/sys/class/gpio/文件夹：

1. 将gpio7重定向用户定义设备，生成gpio7目录

`# echo 7 > export`

2. 进入gpio7目录并查看文件

`# cd gpio7`

`# ls`

direction设置引脚方向，输入还是输出

value设置引脚状态，高电平还是低电平

3. 输入状态

- 设置引脚状态为输入状态

`# echo in > direction`

- 查看引脚高低电平

`# cat value `

4. 输出状态
 
- 设置引脚状态为输出状态

`# echo out > direction`

- 设置输出高电平

`# echo 1 > value`

- 设置输出低电平

`# echo 0 > value`

5. 注销

测试完毕之后返回/sys/class/gpio/目录，并将gpio注销

`# cd /sys/class/gpio/`

`# echo 7 > /sys/class/gpio/unexport`

这篇博客很不错：https://zhuanlan.zhihu.com/p/66660750
https://blog.csdn.net/k1ang/article/details/107117077