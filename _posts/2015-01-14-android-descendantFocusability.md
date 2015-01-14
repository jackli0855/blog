---
layout: post
title: "Android开发中在ListView中经常有Item的点击事件及Button或ImageButton等需要被点击的事件之间的处理方法"
date: 2015-01-14 18:10
comments: false
---

Android开发中在ListView中经常有Button或ImageButton等需要被点击的控件，如果不加一些特殊的限制，有可能ListView的Item的点击事件或Button的点击事件，其中一个不能响应。我遇到的情况是ListView的Item不能响应点击事件。

        解决的办法，在ListView的Item的xml文件中添加如下属性：

                1）整个xml文件的根元素如LinearLayout中添加属性android:descendantFocusability="blocksDescendants"

                2）被点击的控件如ImageButton中添加属性android:focusable="false"和android:clickable="true"

 

android:descendantFocusability=””方法解析：
    开发中很常见的一个问题，项目中的listview不仅仅是简单的文字，常常需要自己定义listview，自己的Adapter去继承BaseAdapter，在adapter中按照需求进行编写，问题就出现了，可能会发生点击每一个item的时候没有反应，无法获取的焦点。原因多半是由于在你自己定义的Item中存在诸如ImageButton，Button，CheckBox等子控件(也可以说是Button或者Checkable的子类控件)，此时这些子控件会将焦点获取到，所以常常当点击item时变化的是子控件，item本身的点击没有响应。

    这时候就可以使用descendantFocusability来解决啦，API描述如下：

android:descendantFocusability
Defines the relationship between the ViewGroup and its descendants when looking for a View to take focus.

Must be one of the following constant values.



 

该属性是当一个为view获取焦点时，定义viewGroup和其子控件两者之间的关系。

属性的值有三种：

        beforeDescendants：viewgroup会优先其子类控件而获取到焦点

        afterDescendants：viewgroup只有当其子类控件不需要获取焦点时才获取焦点

        blocksDescendants：viewgroup会覆盖子类控件而直接获得焦点

 

通常我们用到的是第三种，即在Item布局的根布局加上android:descendantFocusability=”blocksDescendants”的属性就好了，至此listview点击的灵异事件告一段落。心得：遇到不会不懂的地方除了网上查询资料之外，也可以多多去尝试每种属性的作用，多阅读官方文档(我始终觉得还是读原文的比翻译的理解的会更好)。