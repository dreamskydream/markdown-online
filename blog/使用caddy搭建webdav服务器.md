使用caddy搭建webdav服务器
==

1. 首先下载或者编译包含Webdav插件的Caddy
2. 生成密码（设置123456789的密码）
`# caddy hash-password --plaintext 123456789`
JDJhJDE0JHpGUDNaL1Q5UEV2dWZxd1BCYWdGUS56WkdZbHJDZG8ybURERWYyRzlzLzd0TTM2akYxNFVh
3. 编辑Caddyfile


    {
            http_port 80
            https_port 443
            order webdav before file_server
    }
    
    :6666 {
            root * /home/xjc/software/caddy/
            encode zstd gzip
            basicauth {
					#用户名   上面命令生成的密码
                    admin JDJhJDE0JHpGUDNaL1Q5UEV2dWZxd1BCYWdGUS56WkdZbHJDZG8ybURERWYyRzlzLzd0TTM2akYxNFVh
            }
            route {
                    rewrite /webdav /webdav/
                    webdav /webdav/* {
                            prefix /webdav
                    }
            }
    }
4. 运行服务
`# caddy run -config Caddyfile -watch`
或者
`# caddy start`后台运行服务
`# caddy stop`停止服务
5. 挂载webdav网盘
`# sudo mount.davfs http://127.0.0.1:6666/webdav /file/to/path/ -o uid=uos,gid=uos`