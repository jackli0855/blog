---
layout: post
title: "How to get UITableView from UITableViewCell"
date: 2015-01-20 15:17 -0700
comments: false
---

In ios7 , UITableViewWrapperView is the superview of UITableViewCell. Also UITableView is the superview of UITableViewWrapperView. 
So for ios7 the solution is :

```objective-c
UITableView *tableView = (UITableView *)cell.superview.superview
```objective-c

cell is object of UITableViewCell .

If ios6, the solution is :

```objective-c
UITableView *tableView = (UITableView *)cell.superview
```objective-c


