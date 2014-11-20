---
layout: post
title: "Bind an event handler to the resize"
date: 2014-10-28 16:40
comments: false
---

Bind an event handler to the "resize" JavaScript event, or trigger that event on an element.
============

Put the follow source into your files

{% highlight c linenos %}
$(window).resize(function() {
    if(this.resizeTO) clearTimeout(this.resizeTO);
    this.resizeTO = setTimeout(function() {
        $(this).trigger('resizeEnd');
    }, 500);
});

$(window).bind('resizeEnd', function() {
//get the current width
console.log($(this).width());
//get the current height
console.log($(this).height());
});
{% endhighlight %}
 