---
layout: post
title: "mysql remote access"
date: 2014-10-16 09:17
comments: false
---

1 创建mysql用户
============

{% highlight sql linenos %}
$ CREATE USER myuser IDENTIFIED BY 'password';
{% endhighlight %}

2 分配权限
=======

{% highlight ruby linenos %}
$ grant all privileges on mydb.* to myuser@'%' identified by 'mypasswd';
{% endhighlight %}

**mydb**:DB name
**myuser**: mysql user name
**mypassword**: password

eg. assign all DB privileges to all remote user

{% highlight ruby linenos %}
$ grant all PRIVILEGES on *.* to root@'%' identified by '123456';
{% endhighlight %}

**不要忘记flush修改的内容**

{% highlight ruby linenos %}
$ FLUSH PRIVILEGES;
{% endhighlight %}

3 ubuntu 开放 3306端口，远程访问
============

* 使用nestat命令查看3306端口状态

{% highlight ruby linenos %}
$ netstat -an | grep 3306
{% endhighlight %}

tcp        0      0 127.0.0.1:3306          0.0.0.0:*               LISTEN

如果是上面的结果，说明 3306端口只是在**IP 127.0.0.1**上监听，所以拒绝了其他IP的访问。

* 解决方法：修改/etc/mysql/my.cnf文件。打开文件，找到下面内容：

{% highlight ruby linenos %}
# Instead of skip-networking the default is now to listen only on
# localhost which is more compatible and is not less secure.
bind-address  = 127.0.0.1
{% endhighlight %}

把上面这一行注释掉或者把127.0.0.1换成合适的IP，建议注释掉。

* 重新启动

{% highlight ruby linenos %}
$ sudo /etc/init.d/mysql restart
{% endhighlight %}

c 重新使用netstat检测：

{% highlight ruby linenos %}
$ netstat -an | grep 3306
{% endhighlight %}

tcp        0      0 0.0.0.0:3306            0.0.0.0:*               LISTEN