---
author: JackRyannn
comments: true
date: 2015-11-11 11:16:00 -0700
layout: post
title: BroadCastReceiver的深入学习
tags:
- Andriod

---
##BroadCast Receiver##


　　今天是11月11日，曾经的光棍节，现在的双十一，对我来说有很多难忘记忆的日子。然而随着时间，它的意义也越来越淡了。

　　今天学习一下broadcast receiver，相比来说我觉得这个比content provider要更简单一点。他可以自动检查系统广播，如果符合，执行里面的onReceiver（）方法。


　　如何判断是否符合呢？这时候就需要给broadcast receiver注册广播（它能够接收的广播）。有两种方法：  
　　1.静态注册：在manifest里注册这个receiver,其中action对应的值就是要注册的广播  

       <receiver android:name=".MyBroadCast"
            >
            <intent-filter>
                <action android:name="com.vipui.broadcast1">
                </action>
            </intent-filter>
        </receiver>
　　  
　　2.动态注册：写在activity里
 
 	    MyBroadCast myBroadCast = new MyBroadCast();
        IntentFilter intentFilter = new IntentFilter();
        intentFilter.addAction("com.vipui.broadcast1");
        registerReceiver(myBroadCast,intentFilter);

二者的区别:

生命周期不同，静态注册是不管你的程序是否处于活动状态，只要安装到手机上，就可以监听得到，然后执行onReceiver()；而动态注册随着程序的结束就销毁了，不能够继续监听。


  

　　
  
  
