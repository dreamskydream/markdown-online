NetworkManager网络管理相关问题
==

1. 相关配置文件位置
> /etc/dbus-1/system.d/NetworkManager.conf
/etc/dbus-1/system.d/nm-avahi-autoipd.conf
/etc/dbus-1/system.d/nm-dhcp-client.conf
/etc/dbus-1/system.d/nm-dispatcher.conf
/etc/dbus-1/system.d/nm-system-settings.conf
/etc/rc.d/init.d/NetworkManager

/etc/dbus-1/system.d/nm-applet.conf

1. 不使用Network Manager 管理某些网络设备
默认情况下，NetworkManager 管理除 lo（环回）设备以外的所有网络设备，因为一些情况需要将设备设置为 unmanaged，也就是不使用NetworkManager管理这些设备。
	- 查看设备状态
`# nmcli device status`
	- 在 /etc/NetworkManager/NetworkManager.conf 配置文件的[main] 层级下启用插件 keyfile。
```
[main]
plugins=keyfile
```
这个一般都启用了。
	- 创建 /etc/NetworkManager/conf.d/unmanaged-devices.conf 配置文件（这个文件名没有要求，自己容易识别就可以了），包含以下内容：
```
[keyfile]
unmanaged-devices=interface-name:wlan*
```
> 1. 以分号隔开；
> 1. 可以使用通配符来匹配接口；
> 1. `interface-name:eth*,except:interface-name:eth0;`表示：除了 eth0，其他以 eth 开头的接口全部 unmanaged；
> 1. 可以通过 mac 地址来排除接口，`mac:66:77:88:99:00:aa`

	- 重启服务
`# systemctl restart network-manager.service`

