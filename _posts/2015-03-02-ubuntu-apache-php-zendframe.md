---
layout: post
title: "Ubuntu php apache zendframe配置过程出现的问题"
date: 2015-02-04 12:00
comments: false
---

1 apache 配置.htaccess文件之非法命令RewriteEngine
error message ：
.htaccess: Invalid command 'RewriteEngine', perhaps misspelled or defined by a module not included in the server configuration.

```
cd /etc/apache2/mods-enabled
sudo ln -s ../mods-available/rewrite.load rewrite.load
sudo /etc/init.d/apache2 restart
```

2 错误2
apache AH01630: client denied by server configuration
这个问题主要是Apache版本升级为2.4之后，出现的问题
apache-2.4.x把NameVirtualHost给取消,现在配置虚拟主机不需要再配置NameVirtualHost了.
删除了 Order deny,allow 和 Order allow,deny
把 Deny from all 替换成了 Require all denied
把Allow from all 替换成了 Require all granted

修改之后完整的配置如下

```
<VirtualHost 80>
directoryIndex  index.html index.php index.htm index.shtml login.php
ServerName local.com
DocumentRoot /var/www/vhosts/wwwroot
	<Directory "/var/www/vhosts/wwwroot">
		Options -Indexes
		AllowOverride All
		Require all granted
	</Directory>
</VirtualHost>
```