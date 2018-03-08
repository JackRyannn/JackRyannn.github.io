---
author: JackRyannn
comments: true
date: 2016-12-17 15:10:00 -0700
layout: post
title: Android Studio中进行ndk的开发
tags:
- Android

---

此篇文章讲述如何在Android Studio中进行ndk的开发以及遇到的问题。  
  
我已经配置好ndk相关文件了，在你的Project Structure里可以看到你的NDK路径。  
  
首先创建一个module，在你的MainActivity里添加一个native方法，如下：   
 
	{% highlight ruby %}
	package com.jackryannn.ndktest;
	import android.support.v7.app.AppCompatActivity;
	import android.os.Bundle;
	
	public class MainActivity extends AppCompatActivity {


    public static native String getStringFromC();
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
    }
	}
	{% endhighlight %}
  
然后再Build-Make Project一下，好生成class文件  

完成后我们要生成头文件，首先要进入终端，然后进入我们的工程文件路径，最后的路径是`src/main/`,这里要创建一个jni文件夹，如图所示：    

![image](https://ooo.0o0.ooo/2015/12/17/567262c099933.png)  
  
用到的方法是`javah`，通常ndk教程里都会告诉你需要sdk里的一个叫做`android.jar`的文件，把这个文件添加到环境变量里最好，不然会有一长串，我的路径为`/Users/pro/Development/adt-bundle-mac-x86_64-20131030/sdk/platforms/android-23/android.jar`，在最后的路径里有android－23这个文件夹，其实不一定非要用android-23，android－多少，都是可以的。添加环境变量的方法再说一遍，`vim ~/.bash_profile`。然后运行命令`javah -d jni ../../build/intermediates/classes/debug/ com.jackryannn.ndktest.MainActivity`。这里都是什么意思呢？  
  
`-d jni`是在当前目录下生成jni的头文件  
`..`是上一级，看总体的目录就明白了，就是要找到生成的class文件，  
  
![image](https://ooo.0o0.ooo/2015/12/17/567263c28fc6a.png)  
  
但是奇怪的是我仍然报错了，提示的错误是缺少v7的一些文件，所以也要把这些东西也导进去，他的路径是`/Users/pro/Development/adt-bundle-mac-x86_64-20131030/sdk/extras/android/support/v7/appcompat/libs/`,添加到环境变量即可。然后再运行，没有报错就成功了。  
  
到这里，我们生成了头文件，接下来还要写具体的函数内容。  
在头文件里，我们可以找到刚才写的那个方法`getStringFromC()`  
他的样子变成了`JNIEXPORT jstring JNICALL Java_com_jackryannn_ndktest_MainActivity_getStringFromC
  (JNIEnv *, jclass);
`  
然后我们在jni文件夹中建立一个`hello.c`文件，把include和函数都写上:
  
	   {% highlight ruby %}
		#include <stdio.h>
		#include "com_jackryannn_ndktest_MainActivity.h"
		JNIEXPORT jstring JNICALL 	Java_com_jackryannn_ndktest_MainActivity_getStringFromC
	        (JNIEnv * env, jclass jclass){
	    return (*env)->NewStringUTF(env,"hello from ndk");
		}
		{% endhighlight %}
  
这时c文件也写好了，现在需要一个Android.mk文件来说明生成的so文件的名字是什么，用那个文件生成。我们可以从ndk的sample里面找一个Android.mk文件：  

	{% highlight ruby %}
	# Copyright (C) 2009 The Android Open Source Project
	#
	# Licensed under the Apache License, Version 2.0 (the "License");
	# you may not use this file except in compliance with the License.
	# You may obtain a copy of the License at
	#
	#      http://www.apache.org/licenses/LICENSE-2.0
	#
	# Unless required by applicable law or agreed to in writing, software
	# distributed under the License is distributed on an "AS IS" BASIS,
	# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or 	implied.	
	# See the License for the specific language governing permissions and
	# limitations under the License.
	#
	LOCAL_PATH := $(call my-dir)
	
	include $(CLEAR_VARS)
	
	LOCAL_MODULE    := hello 
	LOCAL_SRC_FILES := hello.c
	
	include $(BUILD_SHARED_LIBRARY)
	{% endhighlight %}
  
最关键的就是LOCAL_MODULE    := hello 和 LOCAL_SRC_FILES := hello.c，他们分别是ndk-build后的library名称和通过hello.c文件进行生成（或者叫源文件）  
  
到这里所有的文件就建好了，我们在终端的main／目录下通过ndk-build(加入环境变量哦！)可以生成libs文件夹，如图：    

![image](https://ooo.0o0.ooo/2015/12/17/567285a103c97.png)  
  
按道理到这一步就可以编译运行了，不过我却报了一个`UnsatisfiedLinkError`错误。这个错误困扰了我足足半个小时，他说找不到这个库文件，可是明明都在这里啊，路径我也看不懂。。。是不是cpu架构不同呢？arm还是x86还是mip。。。晕了。  
解决方式：我生成的文件在main路径下，但是libs应该放在和app／路径下，所以剪切过去就行了。  
  
最后的成果：  
  
![image](https://ooo.0o0.ooo/2015/12/17/56728717e6e24.png)
