---
layout: post
title: "android app 多国语言对应"
date: 2015-01-14 18:00
comments: false
---

开发环境：
============

	android studio 


①如果生成多国语言对应的文件夹（如下图）
============

![android studio]({{ site.baseurl }}/images/20150116_173729.png)

②生成相应语言文件夹
============

![language]({{ site.baseurl }}/images/20150331_115601.png)

③变换项目文件的查看方式就可以看到生成的对应语言的文件夹
============

![project type]({{ site.baseurl }}/images/20150331_120034.png)

测试
============

把原始的values下面的资源文件，copy到生成的上一步生成的文件夹下面
把文件里面的内容修改成你想要对应的语言
然后测试的时候，把手机的语言设置成对应的语言