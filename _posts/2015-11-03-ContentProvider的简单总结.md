---
author: JackRyannn
comments: true
date: 2015-11-03 10:18:06 -0700
layout: post
title: ContentProvider的简单总结
tags:
- Android

---
content |ˈkɒntent| 原来总是读成肯ten特 ~。~
Content Provider 说是Android的四大组件之一，但是自己本身用的并不多。因为这个组件可能更多用于多个app之间的信息传递。

说是单单一个Content Provider，其实它还有一个对应的Content Resolver，用于读取和操作数据。

首先，我们要有一个提供数据的content provider，才能被别人调用。我们可以继承ContentProvider，里面需要重写六个方法，分别是：

1.insert()
2.delete()
3.query()
4.update()
5.onCreate()
6.getType()

前四个不用说，添删查改。
第五个onCreate()里我们可以进行一些初始化操作，比如实例化SQLiteOpenHelper.
重点是getType(),