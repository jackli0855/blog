---
layout: post
title: "FMDB query or update, get error message"
date: 2014-11-18 14:40
comments: false
---

直接看下面的例子，就会明白
============

{% highlight c linenos %}
BOOL ab = [db executeUpdateWithFormat:@"insert into users(name) values(%@)", @"test"];
if (ab) {
    
}else{
    NSLog(@"#########insert error message:%@", [db lastErrorMessage]);
}
{% endhighlight %}

