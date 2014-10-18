---
layout: post
title: "start faye"
date: 2014-10-16 11:17
comments: false
---

I wrote this shell script in config/thin_example.sh
============

{% highlight c linenos %}
#!/bin/sh

set -e

# Feel free to change any of the following variables for your app:
TIMEOUT=${TIMEOUT-60}
APP_ROOT=/home/deployer/apps/example/current
PID=$APP_ROOT/tmp/pids/thin.pid
CMD="cd $APP_ROOT; bundle exec rackup -D -P $PID $APP_ROOT/config/faye.ru -s thin -E     production"
AS_USER=deployer
set -u

startme() {
    run "$CMD"
}

stopme() {
    run "pkill -f $PID"
}

run () {
  if [ "$(id -un)" = "$AS_USER" ]; then
    eval $1
  else
    su -c "$1" - $AS_USER
  fi
}

case "$1" in
    start)   startme ;;
    stop)    stopme ;;    
    restart) stopme; startme ;;
    *) echo "usage: $0 start|stop|restart" >&2
       exit 1
       ;;
esac
{% endhighlight %}

Loosely modified from the unicorn scripts that Ryan Bates used in his VPS deployment railscast (pro only).

Make it executable
============

{% highlight c linenos %}
$ chmod +x config/thin_example.sh
{% endhighlight %}

You'll need to symlink it to init.d (after chmod +x 'ing to make it executable)

{% highlight c linenos %}
$ sudo ln -nfs /home/deployer/apps/example/current/config/thin_example.sh /etc/init.d/thin_example
{% endhighlight %}

Then if you want it to startup with the server
============

 sudo update-rc.d thin_example defaults
Otherwise you should just be able to /etc/init.d/thin_example [start|stop|restart]. An important point to note is that I'm telling rackup to start in daemon mode (-D) and explicitly setting the PID so I can kill it later.