mariadb和mysql命令
==

错误：ERROR 1045 (28000): Access denied for user 'root'@'localhost' (using password: NO)

1.首先停掉数据库服务:
`# systemctl stop mariadb.service`
2.使用mysqld_safe来启动mysqld服务器:
`# mysqld_safe --user=mysql --skip-grant-tables --skip-networking`
3.无密码登录:
`# mysql -u root mysql`

* * *

命令行登录：
`# mysql -h 127.0.0.1 -u root -p -P 3306`
新建用户：
`# create user 'newuser'@'localhost' identified by '123456';`
或
`# insert into mysql.user(user,host,password) values('newuser','localhost',password('123456'));`
删除用户：
`# DROP USER 'newuser'@'localhost';`
授权:

注：you password获取方法：
`# select password('你的明文密码');`
所有权限
`# grant all privileges on *.* to 'newuser'@'localhost' identified by password 'you password';`
部分权限
`# grant insert,update,delete,select on *.* to 'newuser'@'localhost'`
给予test数据库所有权限：
`# grant all on test.* to 'newuser'@'localhost'`
**注意：**需要执行 **FLUSH PRIVILEGES** 语句重新载入授权表。

查看队列：
`# show processlist;`
或者
`# show full processlist;`
创建数据库：
`# CREATE DATABASE IF NOT EXISTS test DEFAULT CHARACTER SET utf8 DEFAULT COLLATE utf8_general_ci`
选择数据库：
`# USE test;`
修改数据库：
`# ALTER DATABASE test DEFAULT CHARACTER SET gb2312 DEFAULT COLLATE gb2312_chinese_ci;`
删除数据库：
`# DROP DATABASE IF EXISTS test;`
查看数据库：
`# SHOW DATABASES;`
创建表：

    SET FOREIGN_KEY_CHECKS = 0;
    DROP TABLE IF EXISTS `blog_users`;（如果存在，删除表再添加）
    CREATE TABLE `blog_users` (
    `ID` bigint(20) unsigned NOT NULL AUTO_INCREMENT,
    `user_login` varchar(60) NOT NULL DEFAULT '',
    `user_pass` varchar(255) NOT NULL DEFAULT '',
    `user_nicename` varchar(50) NOT NULL DEFAULT '',
    `user_email` varchar(100) NOT NULL DEFAULT '',
    `user_url` varchar(100) NOT NULL DEFAULT '',
    `user_registered` datetime NOT NULL DEFAULT '0000-00-00 00:00:00',
    `user_activation_key` varchar(255) NOT NULL DEFAULT '',
    `user_status` int(11) NOT NULL DEFAULT '0',
    `display_name` varchar(250) NOT NULL DEFAULT '',
    PRIMARY KEY (`ID`),
    KEY `user_login_key` (`user_login`),
    KEY `user_nicename` (`user_nicename`),
    KEY `user_email` (`user_email`)
    ) ENGINE=MyISAM AUTO_INCREMENT=2 DEFAULT CHARSET=utf8;

查看表：
`# SHOW TABLES;`
查看表结构：
`# DESC test；`
查看表如何创建：
`# show create table user \G;`
删除表：
`# DROP TABLE test;`
删除表中的所有数据并自增长数据从1开始：
`# TRUNCATE TABLE 表名;`
插入数据：
`insert into 'blog_users' ('ID', 'user_login', 'user_pass') values ('1','admin','$P$BnOWIsBwuIA7Hf3gZh9fVuY6sWkKmt1'); `
或：
`# INSERT INTO test set title = '这个是title';`
删除数据：
`# DELETE FROM test where title = '这个是title';`
修改数据或更新数据：
`# UPDATE test set title = '这个是title';`
查询：
`# SELECT id, title FROM test where title = '这个是title';`
备份数据库：
`# mysqldump -uroot -p123456 数据库 > /tmp/数据库.sql`
恢复数据库：
`# mysql -uroot -p123456 数据库 < /tmp/数据库.sql`
备份表：
`# mysqldump -uroot -p 数据库 表 > /tmp/表.sql`
恢复表：
`# mysql -uroot -p 数据库 < /tmp/表.sql`
备份所有库：
`# mysqldump -uroot -p -A > /tmp/123.sql`
只备份表结构：
`# mysqldump -uroot -p -d 数据库 > /tmp/数据库.sql`