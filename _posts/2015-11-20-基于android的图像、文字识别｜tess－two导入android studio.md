---
author: JackRyannn
comments: true
date: 2015-11-20 11:31:00 -0700
layout: post
title: 基于android的图像、文字识别｜tess－two导入android studio
tags:
- Andriod

---
##关于毕设
　　没想到这么快就要做毕设了，毕设也没有想象中的那么难，好像跟平常的一次结课论文一样。
这次毕设要做的是三部分内容:  

1.将拍照，获取图片，上传图片。   
2.二维码扫描   
3.文字识别

　　前两部分没有什么难度，主要就是文字识别这部分了。  
　　毕设中对速度和准确度有一定要求，对网络反馈也有时间的要求，所以我也不知道文字识别这部分是放到手机端进行好，还是上传图片在服务器处理好，不过我只负责终端这部分，所以我就在手机这处理了。
　　
##文字识别
　　文字识别其实并不需要具体做算法这方面，只要把图片放到ocr工具里就可以了。ocr（optical character recognition｜光学字符识别）。我用到的工具是`tess－two`，它是tesseract-android-tools的一个分支，它整合了android 的api和tesseract OCR 和 一些图像处理库相关的build文件。
　　具体可以查看它的github：[tess－two](https://github.com/rmtheis/tess-two)。  
　　然而在它的github介绍里只有如何导入eclipse，而没有如何导入android studio，如果按普通的方式导入出现了各种各样的错误，我用了将近一天的时间就为了导入这个库。  
　　
##注意！这是导入eclipse的步骤，Android Studio往下拉
　　首先你要clone这个库，然后你要有以下三步：  
  
1. ndk-build  
2. android update project --path .
3. ant release


为此，你要安装ndk，android studio安装ndk特别简单，在file -> project structure      ->SDK location 里就有ndk的自动下载，我已经下载好了，所以只显示了ndk的路径。

![image](https://ooo.0o0.ooo/2015/11/20/564ecf6405087.png)   

然后你要把android sdk的tools加入到环境变量，mac的终端的环境变量添加办法网上有很多，但是都不太方便，我写一下我的方法。  
打开终端，输入`vim .bash_profile`,如果不是用户家目录，可以用`cd ~`返回至该目录。  
![image](https://ooo.0o0.ooo/2015/11/20/564ef472d529e.png)    

然后回车进入vim编辑器  
  
![image](https://ooo.0o0.ooo/2015/11/20/564ef47ebb4a8.png)  
  
　　按照export那一串的格式添加好，按esc退出编辑，按`shift＋ ：`再输入`wq`保存并退出。  
最后一步，安装ant，详见[教程](http://cache.baiducontent.com/c?m=9f65cb4a8c8507ed4fece7631046893b4c4380146d96864968d4e414c4224618143da5e067754c1980853a3c50f11e41bca770216c5d61aa9dce824fdeb8982b3bcd7a742613d60145960eafba1d798066c304b7b81996e9ac74&p=c0769a4791934eac58e8d5271b5e80&newp=9f7cdc15d9c041aa44a2c7710f5091231610db2151d4d610639b&user=baidu&fm=sc&query=brew+install+ant&qid=952a11e100002fe2&p1=2)。  
完成三步后，把tess—two文件夹当作library库导入eclipse即可。

##Android Studio如何导入TESS－TWO  
　　我翻了好多文章，google了大半天，才找到一个可行的办法。这篇文章是十月三号发布的。 
网站链接：[Android Advance Blog](http://androidadvance.com/blog/tutorial-getting-started-with-tessaract-ocr-in-android-android-studio/)    
英文一般，随便翻译一下：  

　　Tesseract是现在可用的最准确的OCR开源库，结合了Leptonic（一个类似OpenCV的数字图像处理库），它可以读取广泛的图像格式，并且将其转换成超过六十种语言的文字。在1995年UNLV准确性测试中位列前三，在1995年和2006年之间，它并没有什么突破性进展，直到被谷歌管理，有了大幅度优化，现在遵循 Apache License 2.0 协议。  
  
具体步骤：  
1.清空你的android studio，删除eclipse（不会有人再使用它了），保持什么都没安装的状态和最新的工具，否则不要抱怨当你编译时遇到什么奇怪的问题。  
我会尽力让教程的程序运行在新的版本里，在我写这篇文章的时候，我用的是Android Studio 1.4 和 android-ndk-r10e,jdk 1.8.0-45.注：我的版本是Android Studio 1.5 ，jdk1.7.0_79，ndk是andriod studio自动下载的，叫ndk－bundle，版本号不知道。。而且项目我并没有清空，这一步不是必须的。  
2.下载安装jdk、jre、添加到环境变量，ndk安装巴拉巴拉……  （ndk也要添加环境变量）
然后要把你下载的tess－two用ndk来build一下。  
3.创建一个“new world”的android studio工程，编译它，确保能够运行。注：老外好细致～  
4.创建一个叫libraries的文件夹，举个例子，你的工程叫做FirstProject，你要创建FirstProject/libraries文件夹，现在把你已经ndk－build的tess－two文件夹放进这个libraries文件夹，然后创建一个build.gradle文件放到tess－two文件夹里，（注：你可以复制别的module里的build.gradle），注意，这是最难的部分，你的gradle版本、build版本可能会更高，你要重写它和你的版本保持一致，这里提供一个build.gradle的模版：    

    buildscript {
    repositories {
        mavenCentral()
    }
    dependencies {
        classpath 'com.android.tools.build:gradle:1.3.0'
    }
    }  

    apply plugin: 'android-library'  
	android {
    compileSdkVersion 23
    buildToolsVersion "23.0.1"

    defaultConfig {
        minSdkVersion 15
        targetSdkVersion 23
    }

    sourceSets.main {
        manifest.srcFile 'AndroidManifest.xml'
        java.srcDirs = ['src']
        resources.srcDirs = ['src']
        res.srcDirs = ['res']
        jniLibs.srcDirs = ['libs']
    }
	}  
　　compileSdkVersion、buildToolsVersion、minSdkVersion、targetSdkVersion这些都和项目保持统一就行了。  
　　然后在工程的settings.gradle文件里添加`include ':libraries:tess-two'`  
5.（注，其实到这一步重新编译一下没有错误就成功了，接下来他导入了一个例子，然而这个例子时间有点久了，我们可以直接自己使用，所以这一步不解释了。）    
  
我会把我的工程上传到github，[点击此处跳转](https://github.com/JackRyannn/Tess-twoInAndroidStudio)。
  
如果有问题欢迎告知～
　　




　　



  

　　
  
  
