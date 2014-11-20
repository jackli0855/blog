---
layout: post
title: "iOS UIProgressView"
date: 2014-10-31 08:10
comments: false
---

直接上代码
============

{% highlight objc linenos %}
//UIProgressView的使用 常用于歌曲的和下载的进度条
UIProgressView *oneProgressView = [[UIProgressView alloc] init];
oneProgressView.frame = CGRectMake(0, 30, 320, 30); // 设置UIProgressView的位置和大小
oneProgressView.backgroundColor = [UIColor clearColor]; // 设置背景色
oneProgressView.alpha = 1.0; // 设置透明度 范围在0.0-1.0之间 0.0为全透明

oneProgressView.progressTintColor = [UIColor yellowColor]; // 设置已过进度部分的颜色
oneProgressView.trackTintColor = [UIColor blackColor]; // 设置未过进度部分的颜色
oneProgressView.progress = 0.2; // 设置初始值，范围在0.0-1.0之间，默认是0.0
// [oneProgressView setProgress:0.8 animated:YES]; // 设置初始值，可以看到动画效果

[oneProgressView setProgressViewStyle:UIProgressViewStyleDefault]; // 设置显示的样式
// 添加到View上，并释放内存
[self.view addSubview:oneProgressView];
[oneProgressView release], oneProgressView = nil;
{% endhighlight %}