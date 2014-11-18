---
layout: post
title: "ios imageview corner radius"
date: 2014-11-18 18:40
comments: false
---

1) 在使用的文件中引入下面的文件
============

{% highlight c linenos %}
 #import <QuartzCore/QuartzCore.h>
{% endhighlight %}

2）追加设定的代码
============

imageView是UIImageView类型

{% highlight c linenos %}
imageView.layer.cornerRadius=10;
imageView.layer.masksToBounds = YES;
{% endhighlight %}

