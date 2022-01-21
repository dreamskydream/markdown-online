iptables设置sh
==
```
    #!/bin/bash

    PATH=/sbin:/bin:/usr/sbin:/usr/bin

    #设置网卡
    wk="eth1"

    # 1. 清除规则
    iptables -F
    iptables -X
    iptables -Z

    # 2. 设定政策
    iptables -P INPUT DROP
    iptables -P OUTPUT ACCEPT
    iptables -P FORWARD ACCEPT

    # 3~5. 制订各项规则
    iptables -A INPUT -i lo -j ACCEPT
    iptables -A INPUT -i $wk -m state --state RELATED,ESTABLISHED -j ACCEPT

    #samba服务
    iptables -A INPUT -i $wk -p tcp --dport 139 -j ACCEPT
    iptables -A INPUT -i $wk -p tcp --dport 445 -j ACCEPT
    iptables -A INPUT -i $wk -p udp --dport 137:138 -j ACCEPT

    #vsftp服务
    iptables -A INPUT -i $wk -p tcp --dport 21 -j ACCEPT
    iptables -A INPUT -i $wk -p tcp --dport 1024:65535 -j ACCEPT
    #iptables -A INPUT -i $wk -s 192.168.1.0/24 -j ACCEPT
```
**1.查看防火墙规则**

`# iptables [-t tables] [-L] [-nv] `
选项与参数：
 -t ：后面接table ，例如nat或filter，若省略此项目，则使用预设的filter
 -L ：列出目前的table的规则
 -n ：不进行IP 与HOSTNAME 的反查，显示速度会快很多！
 -v ：列出更多的相关信息</pre>

**2.清除规则**
`# iptables [-t tables] [-FXZ] `
选项与参数：
-F ：清除所有的已订定的规则；
-X ：杀掉所有使用者"自订" 的chain；
-Z ：将所有的chain 的计数与流量统计都归零</pre>

**3.定义预设政策(policy)**
`# iptables [-t nat] -P [INPUT,OUTPUT,FORWARD] [ACCEPT,DROP] `
选项与参数：
-P ：定义政策( Policy )。注意，这个P为大写啊！
ACCEPT ：该封包可接受
DROP ：该封包直接丢弃，不会让client 端知道为何被丢弃。

范例：将本机的INPUT设定为DROP ，其他设定为ACCEPT 
```
# iptables -P INPUT DROP 
# iptables -P OUTPUT ACCEPT 
# iptables -P FORWARD ACCEPT 
# iptables -t nat -P PREROUTING ACCEPT
# iptables -t nat -P POSTROUTING ACCEPT
# iptables -t nat -P INPUT ACCEPT
# iptables -t nat -P OUTPUT ACCEPT
```
**nat表不用于过滤，所以不能设置为DROP**
**4.定义规则**
`# iptables [-AI链名] [-io网络界面] [-p协定] [-s来源IP/网域] [-sport 端口范围] [-d目标IP/网域] [-dport 端口范围] -j [ACCEPT'DROP'REJECT'LOG] `
 选项与参数：
 ```
 -AI链名：规则的"插入"或"增加"
 -A：新增加一条规则，该规则增加在原本规则的最后面。例如原本已经有四条规则，使用-A就可以加上第五条规则！
 -I：插入一条规则。如果没有指定此规则的顺序，预设是插入变成第一条规则。
 例如原本有四条规则，使用-I则该规则变成第一条，而原本四条变成2~5号
 链：有INPUT，OUTPUT，FORWARD等，此链名称又与-io有关，请看底下。
 -io网卡：设定封包进出的规范
 -i：封包所进入的那个网卡，例如eth0，lo等网卡。需与INPUT链配合；
 -o：封包所传出的那个网卡，需与OUTPUT链配合；
 -p协定：设定此规则适用于哪种封包格式
 主要的封包格式有：tcp，udp，icmp及all。
 -s来源IP/网域：设定此规则之封包的来源项目，可指定单纯的IP或包括网域，例如：
 IP :192.168.0.100
 网域：192.168.0.0/24，192.168.0.0/255.255.255.0均可。
 若规范为『不许』时，则加上！即可，例如：
 -s！192.168.100.0/24表示不许192.168.100.0/24封包来源；
 -d目标IP/网域：同-s，只不过这里指的是目标的IP或网域。
 --sport 来源端口范围。例如 1024:65535 
 --dport 目标端口范围
 -j：后面接动作，主要的动作有接受（ACCEPT）、丢弃（DROP）、拒绝（REJECT）及记录（LOG） 
 ```

`# iptables -A INPUT [-m state] [--state状态]  `
选项与参数：  
```
-m：一些iptables的外挂模块，主要常见的有：  
          state：状态模块  
          mac：网络卡硬件位址（hardware address）  
--state：一些封包的状态，主要有：  
         INVALID：无效的封包，例如数据破损的封包状态  
        ESTABLISHED：已经联机成功的联机状态；  
        NEW：想要新建立联机的封包状态；  
       RELATED：这个最常用！表示这个封包是与我们主机发送出去的封包有关  
 ```
例：只要已建立或相关封包就予以通过，只要是不合法封包就丢弃    
`# iptables -A INPUT -m state --state RELATED,ESTABLISHED -j ACCEPT  `
`# iptables -A INPUT -m state --state INVALID -j DROP`
对MAC地址为aa:bb:cc:dd:ee:ff主机开放其连线 
`# iptables -A INPUT -m mac --mac-source aa:bb:cc:dd :ee:ff -j ACCEPT `
选项与参数： 
```
--mac-source ：就是来源主机的MAC
```
**ICMP 封包规则：针对是否回应ping 来设计**

通常我们会把ICMP type 8 (echo request)拿掉而已，让远端主机不知道我们是否存在，也不会接受ping的回应。
`# iptables -A INPUT [-p icmp] [--icmp-type类型] -j ACCEPT `
选项与参数：
```
--icmp-type ：后面必须要接ICMP 的封包类型，也可以使用代号，
              例如8 代表echo request 的意思。
```
例：让0,3,4,11,12,14,16,18的ICMP type可以进入本机： 
```
#!/bin/bash 
icmp_type="0 3 4 11 12 14 16 18" 
for typeicmp in $icmp_type 
do 
    iptables -A INPUT -i eth0 -p icmp --icmp-type $typeicmp -j ACCEPT 
done
```