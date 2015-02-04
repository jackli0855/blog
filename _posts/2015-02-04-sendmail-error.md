---
layout: post
title: "Sendmail: unqualified hostname unknown; sleeping for retry unqualified hostname"
date: 2015-02-04 12:00
comments: false
---

Sendmail error logs
============

```
Feb  4 17:49:00 alpha-VirtualBox sendmail[11814]: My unqualified host name (alpha-VirtualBox) unknown; sleeping for retry
Feb  4 17:50:00 alpha-VirtualBox sendmail[11814]: unable to qualify my own domain name (alpha-VirtualBox) -- using short name
```

Solution
============

FROM:

```
127.0.0.1       localhost
127.0.1.1       alpha-VirtualBox
```

TO:

```
127.0.0.1       localhost.localdomain localhost alpha-VirtualBox
127.0.1.1       alpha-VirtualBox
```