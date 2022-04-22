ssh加密算法的启用禁用方法
==
* “手机ES文件浏览器”不能连接sftp的问题解决方法
首先，说说为什么学习这个东西，在使用“手机ES文件浏览器”这个软件的时候，出现了不能连接的情况，在网上查询后发现是KexAlgorithms 密钥交换算法的问题，在/etc/ssh/sshd_config后添加:
`KexAlgorithms +diffie-hellman-group14-sha1`
后问题解决。
* 出现no matching host key type found.Their offer: ssh-rsa,ssh-dss这个问题时,在/etc/ssh/sshd_config后添加：
`HostKeyAlgorithms ssh-rsa`

配置位置：/etc/ssh/sshd_config
--
* Ciphers
Ciphers 指定 ssh 使用的加密算法。多个加密算法之间使用逗号分隔。当 Ciphers 的值以 + 字符开始时，指定的加密算法将附加到默认集合，不影响默认集合中的其它算法。当 Ciphers 的值以 ‘-’ 字符开始时，指定的加密算法将会从默认集合中移除，不影响默认集合中的其它项目。
比如，在 sshd_config 文件的最后添加如下行：
` Ciphers +3des-cbc,aes128-cbc `
这样就添加了两个加密算法。
` Ciphers -3des-cbc,aes128-cbc `
这样就删除了两个加密算法。

* MACs
MACs 选项指定可用的 MAC（消息认证代码）算法，用于数据完整性保护。配置方法与 Ciphers 一致。

* KexAlgorithms
KexAlgorithms 选项指定可用的密钥交换算法。配置方法与 Ciphers 一致。

* PubkeyAcceptedKeyTypes
PubkeyAcceptedKeyTypes 指定公钥认证允许的密钥类型。配置方法与 Ciphers 一致。

* 查看 ssh 支持的不同类别的加密算法
Ciphers:` ssh -Q cipher`
MACs:` ssh -Q mac`
KexAlgorithms:` ssh -Q kex`
PubkeyAcceptedKeyTypes:` ssh -Q key`