---
layout: post
title: "Comparing two branches in GIT"
date: 2014-10-27 9:33
comments: false
---

Comparing two branches in GIT
============

{% highlight c linenos %}
git diff branch_1..branch_2
{% endhighlight %}
That will produce the diff between the tips of the two branches.
 If you'd prefer to find the diff from their common ancestor to test, you can use three dots instead of two.
 
{% highlight c linenos %}
git diff branch_1...branch_2
{% endhighlight %}