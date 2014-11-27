---
layout: post
title: "Ubuntu php nginx 总结"
date: 2014-11-26 08:10
comments: false
---

前提是nginx已经安装好了
如果没有还没有安装的话， 可以参考[https://www.digitalocean.com/community/tutorials/how-to-install-linux-nginx-mysql-php-lemp-stack-on-ubuntu-12-04](https://www.digitalocean.com/community/tutorials/how-to-install-linux-nginx-mysql-php-lemp-stack-on-ubuntu-12-04)

{% highlight c linenos %}
echo "deb http://ppa.launchpad.net/nginx/stable/ubuntu $(lsb_release -sc) main" | sudo tee /etc/apt/sources.list.d/nginx-stable.list
sudo apt-key adv --keyserver keyserver.ubuntu.com --recv-keys C300EE8C
sudo apt-get update
sudo apt-get install nginx
{% endhighlight %}

1 安装php

```
sudo apt-get install php5-fpm
```

2 config php

```
sudo apt-get install php5-fpm
```
默认情况下 cgi.fix_pathinfo=1 需要把1 变成 0
cgi.fix_pathinfo=0 

3 配置php-fpm
如果是 5.5之后的版本，可以跳过这一步

sudo nano /etc/php5/fpm/pool.d/www.conf

```
listen = /var/run/php5-fpm.sock
```

4 重启 php fpm

```
sudo service php5-fpm start
```

这一步，可能会遇到无法启动的情况。遇到这种情况，首先我们要确认现在 php的运行状况

```
sudo service php5-fpm status
```

正常的情况会看到下面的结果

```
php5-fpm start/running, process 5755
```

不正常的情况是下面

```
php5-fpm stop/waiting
```

如果是不正常的情况，需要手动杀掉这个进程。
查看进程的方法

```
ps -ef | grep "php"
```

根据查到pid号

```
sudo kill pid-No.
```
5 设置nginx
下面是我使用的配置，仅供参考

```
user root;
#user  nobody;
worker_processes  1;

#error_log  logs/error.log;
#error_log  logs/error.log  notice;
#error_log  logs/error.log  info;

#pid        logs/nginx.pid;

events {
    worker_connections  1024;
}


http {
    passenger_root /home/alpha/.rbenv/versions/1.8.7-p374/lib/ruby/gems/1.8/gems/passenger-4.0.52;
    passenger_ruby /home/alpha/.rbenv/versions/1.8.7-p374/bin/ruby;

    include       mime.types;
    default_type  application/octet-stream;

    sendfile        on;
    keepalive_timeout  65;
    client_max_body_size 50M;
    fastcgi_intercept_errors on;

   server {
        listen   80;

        #root /usr/share/nginx/www;
        root /home/alpha/workspace/php/demo_latest;
        index index.php index.html index.htm;

        server_name ez-vc.local.com;
        fastcgi_keep_conn on;
        location / {
            try_files $uri $uri/ /index.html;
        }

        error_page 404 /404.html;

        error_page 500 502 503 504 /50x.html;
        location = /50x.html {
              root /home/alpha/workspace/php/demo_latest;
        }

        # pass the PHP scripts to FastCGI server listening on the php-fpm socket
        location ~ \.php$ {
                try_files $uri =404;
                fastcgi_split_path_info ^(.+\.php)(/.+)$;
                # NOTE: You should have "cgi.fix_pathinfo = 0;" in php.ini

                # With php5-cgi alone:
                #fastcgi_pass 127.0.0.1:9000;

                fastcgi_pass unix:/var/run/php5-fpm.sock;               
                fastcgi_index index.php;
                fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
                include fastcgi_params;  
        }
}

}
```

关于上面的配置，有两点需要提醒
1） 第一行 user, 这个地方要根据自己的nginx安装的角色来决定
我安装的时候是用 root 安装的，所以这个地方就设定为 root
如果不设定，会出现 php5-fpm没有权限访问的问题，下面是错误信息

```
unix:/var/run/php5-fpm.sock failed (13: Permission denied) while connecting to upstream, client: 192.168.56.1, server: ez-vc.local.com, request: "GET / HTTP/1.1", upstream: "fastcgi://unix:/var/run/php5-fpm.sock:", host: "ez-vc.local.com"
```

为了这个地方我还修改了 用户的工作组group，但是这个地方要不要改我也不是很确定
如果按上面的操作之后，还是不行，可以尝试修改一下 用户组别
方法
a 查看当前用户的信息

```
grep alpha /etc/group
```

b 修改用户的组别
不存在的用户，用下面的

```
useradd -G {group-name} username
```
例如： useradd -G www alpha

存在的用户用

```
usermod -g www alpah
```

2) restart nginx

```
sudo service nginx restart
```