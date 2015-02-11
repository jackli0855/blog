---
layout: post
title: "Centos 追加自启动脚本"
date: 2015-02-11 09:00
comments: false
---

以下是我的脚本， /etc/init.d/rails-start
============

```
#!/bin/bash

#chkconfig:   - 57 75
#description: the description of the rails_start shell
#processname: rails_start

start() {
echo "Starting rails_start ..."
echo $PWD
cd /home/ec2-user/wasabi&&./update.sh
sudo /etc/init.d/nginx start
echo "end rails_start ..."
}

stop() {
sudo /etc/init.d/nginx stop
}

# See how we were called.
case "$1" in
  start)
	start
	;;
  stop)
    stop
	;;
  restart|force-reload)
	stop
	start
	;;
  *)
	echo $"Usage: $0 {start|stop|restart|force-reload}"
	exit 2
esac
```

追加到chkconfig中
============

```
sudo chkconfig --add /etc/init.d/rails_start
sudo chkconfig --level 345 rails_start on
```