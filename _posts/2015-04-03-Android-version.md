---
layout: post
title: "版本号 versionCode, versionName "
date: 2015-04-03 11:17
comments: false
---

Android:versionCode是什么？
============

android:versionCode表示该软件的版本号，对用户是不可见的，它必须是一个int型的整数，它的大小代表该软件版本升级过多少次，因此每次发布新版本该值都会+1，如Android:versionCode = ”62″ ,代表此软件已经发布过62次了。
 
作用：软件的升级全靠获取服务端的versionCode进行对比，发现比现在的大就会提示用户升级
 
Android:versionName是什么？
============

android:versionName表示该软件的版本名字，但它是对用户是可见的，由三部分组成<major>.<minor>.<point>,该值是个字符串，如Android:versionName:3.4.4,表示当前的版本为3.4.4

AndroidManifest.xml
============

 <manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="*.*.*" android:versionCode="2" android:versionName="1.1.0">
 </manifest>