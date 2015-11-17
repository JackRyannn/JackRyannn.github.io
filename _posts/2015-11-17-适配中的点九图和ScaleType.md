---
author: JackRyannn
comments: true
date: 2015-11-17 10:13:00 -0700
layout: post
title: 适配中的点九图和ScaleType
tags:
- Andriod

---
##点九图



　　点九图是一种android特有的图像格式，他的后缀名为 **.9.png**。

　　它的好处是可以更智能的拉伸图片，而不是简单的横纵放大的拉伸，效果如下：
　　
　　![image](https://ooo.0o0.ooo/2015/11/17/564ac3ddf0052.jpg)
　　

　　我们可以通过点九图来指定一个图片的拉伸区域和非拉伸区域，在Java的sdk的tool里有用来制作点九图的工具，我们可以在终端里打开:/Users/pro/Development/adt-bundle-mac-x86_64-20131030/sdk/tools里的draw9patch
　　
　　![image](https://ooo.0o0.ooo/2015/11/17/564ac306a5fa1.png)　
　　
　　
　　然后拖动你要处理的图片到这里,就可以进行处理了。
　　![image](https://ooo.0o0.ooo/2015/11/17/564ac309764f0.png)

　　

　　点九图其实就是在原图的基础上，在四边各添加一个像素的边界，它是透明的，可以在这个边界上画黑色的点，四个边上点的含义是不同的。

1.左边黑色条位置向下覆盖的区域表示图片横向拉伸时，只拉伸该区域 

2.上边黑色条位置向右覆盖的区域表示图片纵向拉伸时，只拉伸该区域   

3.右边黑色条位置向左覆盖的区域表示图片纵向显示内容的区域 （我没有点，因为我的这张图片在使用时不需要添加文字等。这里的**显示内容的区域**指的是比如我的图片要添加文字，那么这个文字的区域是由点九图的右边和下边决定的）

4.下边黑色条位置向上覆盖的区域表示图片横向显示内容的区域

5.没有黑色条的位置覆盖的区域是图片拉伸时保持不变（比如，如果图片的四角为弧形的时候，当图片 被任意拉伸时，四角的弧形都不会发生改变）

他的使用效果（横纵向）

![image](https://ooo.0o0.ooo/2015/11/17/564ac30775abd.png)　

![image](https://ooo.0o0.ooo/2015/11/17/564ac30f96279.png)


可以看到这个图片会自动进行缩放，而且保留细节。

##ScaleType  


效果图：(和下面介绍顺序一致，第一行从左到右，然后第二行)

![image](https://ooo.0o0.ooo/2015/11/17/564ac308aa7b2.png)  



CENTER_INSIDE / centerInside（会进行缩放），将图片的内容完整居中显示，通过按比例缩小或原来的size使得图片长/宽等于或小于View的长/宽  

CENTER_CROP / centerCrop（会进行缩放）  按比例扩大图片的size居中显示，使得图片长(宽)等于或大于View的长(宽)
  
CENTER /center（不会进行缩放）  按图片的原来size居中显示，当图片长/宽超过View的长/宽，则截取图片的居中
部分显示

FIT_CENTER / fitCenter（会进行缩放）  把图片按比例扩大/缩小到View的宽度，居中显示（展示有错误，这个图和center_inside的效果应该是一样，因为这个imageview被挤的小了点。）

FIT_START / fitStart（会进行缩放）  把图片按比例扩大/缩小到View的宽度，紧贴imageview的顶部  

FIT_END / fitEnd（会进行缩放）   把图片按比例扩大/缩小到View的宽度，紧贴imageview的底边  

FIT_XY / fitXY（会进行缩放，但不是等比例缩放）  把图片不按比例扩大/缩小到View的大小显示
　　		　　		　　	
最后我想说，通过ctrl＋command＋space可以在markdown里面打出emoji表情，哈哈哈，还是蛮开心的！

🇨🇳🇨🇳🇨🇳🇨🇳🇨🇳🇨🇳🇨🇳🇨🇳🇨🇳🇨🇳
  

　　
  
  
