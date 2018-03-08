---
author: JackRyannn
comments: true
date: 2016-11-03 10:18:06 -0700
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
重点是getType(),他会根据uri来返回一个 **MIME** 类型，什么是MIME:

Multipurpose Internet Mail Extensions
<多功能Internet邮件扩充服务>

不过随着时间的推进，它不仅仅用于邮件领域了，其他数据类型也可以用它表示，比如图片image/jpg。

那么MIME怎么用，有什么用呢？

1.
在manifest.xml里的<intent-filter>里的< data android:mimeType = "image/jpg">这可以用到。用来表示这个activity或者别的组件可以打开的数据类型，这个例子中的activity应该是可以打开jpg文件的。

2.
通过provider获取的数据不单单是数据库里的文本，也可能是图片、音频、视频，所以如果一个content provider提供了很多数据，如何区分这些数据，需要一个判断，根据uri的不同，gettype返回不同的mime值，就可以区分不同的数据类型了，然后不同的数据类型要用不同的activity（组件）处理。


流程是：
intent（带uri）--> content provider 的 getType()返回mimeType --> 从manifest里用mimeType进行匹配 --> 匹配成功启动activity



