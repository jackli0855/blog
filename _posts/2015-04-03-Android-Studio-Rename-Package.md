---
layout: post
title: "Android Studio Rename Package"
date: 2015-04-03 16:17 -0700
comments: false
---


In Android Studio, you can do this:

For example, if you want to change `com.example.app` to `my.awesome.game`, then:

1. In your `Project pane`, click on the little gear icon ( ![Gears icon](http://i.stack.imgur.com/lkezT.png) )

2. Uncheck / De-select the `Compact Empty Middle Packages` option

![Compact Empty Middle Packages](http://i.imgur.com/3j5pzNa.png)

3. Your package directory will now be broken up in individual directories

4. Individually select each directory you want to rename, and:

* Right-click it
* Select `Refactor`
* Click on `Rename`
* In the Pop-up dialog, click on `Rename Package` instead of Rename Directory
* Enter the new name and hit Refactor
* Allow a minute to let Android Studio update all changes
* Note: When renaming `com` in Android Studio, it might give a warning. In such case, select Rename All

![4](http://i.imgur.com/PW9oZll.png)

5. Now open your `Gradle Build File` (`build.gradle` - Usually `app` or `mobile`). Update the `applicationId` to your new Package Name and Sync Gradle, if it hasn't already been updated automatically:

![5](http://i.imgur.com/hMx08b7.png)

6. `Done!` Anyways, Android Studio needs to make this process a little simpler.