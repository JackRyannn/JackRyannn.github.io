---
author: JackRyannn
comments: true
date: 2015-11-25 10:55:00 -0700
layout: post
title:Android系统中百度OCR的使用说明
tags:
- Andriod

---    

##百度OCR
　　百度OCR是百度今年六月份公布的，分企业版和个人版，用法都差不多。大家可以先看一下他的官方网页介绍  [百度OCR文字识别个人版](http://apistore.baidu.com/apiworks/servicedetail/146.html)。  
　　他是通过post方法来调用api的，主要有四部分参数：  
1.url地址  
2.你的apikey  
3.header中的参数  
4.body中的请求参数  


我用的网络框架是okhttp，其他框架也可以，只是我比较喜欢这个框架。需要注意的是，图片上传的格式是string，用base64处理，android里有`android.util.Base64`这个库，可以方便的将字符串进行base64处理：`string = Base64.encodeToString(bytes, Base64.NO_WRAP);`但需要注意的是一定要用`Base64.NO_WRAP`这个flag，我们可以看一下下面这个解释，这个flag转换后会删去换行符，让输出只有一行，这个符合百度OCR处理的规则。

    /**
     * Encoder flag bit to omit all line terminators (i.e., the output
     * will be on one long line).
     */
    public static final int NO_WRAP = 2;  
  
他的官方网页上说在base64处理后还要用URLEncoder处理，但是我加上这一步就会报错，不加反而正常运行，不知道是不是框架里自动处理了，我也没有功夫细细研究，所以如果你有`－1`的错误，可以尝试修改这部分。  
  
注意：手机的api貌似有一些限制，比如大小，好像是300k，而且识别率也不如他给的demo高，同样一张照片，而且两个效果都一般般。  
  
![手机api测试](https://ooo.0o0.ooo/2015/11/24/565523015b6f7.jpg)  
![image](https://ooo.0o0.ooo/2015/11/24/56552301922a0.png)
  
其实也没什么好说的，下面放源码（我用了拍照和相册两个获取图片的方法）：      

	package com.jackryannn.graduation;
	import android.app.Activity;
	import android.content.Intent;
	import android.graphics.Bitmap;
	import android.net.Uri;
	import android.os.AsyncTask;
	import android.os.Bundle;
	import android.os.Handler;
	import android.os.Message;
	import android.provider.MediaStore;
	import android.util.Base64;
	import android.widget.ImageView;
	import android.widget.TextView;
	
	import com.squareup.okhttp.Callback;
	import com.squareup.okhttp.FormEncodingBuilder;
	import com.squareup.okhttp.OkHttpClient;
	import com.squareup.okhttp.Request;
	import com.squareup.okhttp.RequestBody;
	import com.squareup.okhttp.Response;
	
	import org.json.JSONArray;
	import org.json.JSONObject;
	
	import java.io.ByteArrayOutputStream;
	import java.io.IOException;
	
	import butterknife.Bind;
	import butterknife.ButterKnife;
	import butterknife.OnClick;
	
	
	public class Ocr2Activity extends Activity {
	    String url = "http://apis.baidu.com/apistore/idlocr/ocr";
	    Handler handler;
	    @OnClick(R.id.takepic)
	    void takepic() {
	        Intent intent = new Intent(MediaStore.ACTION_IMAGE_CAPTURE);
	        startActivityForResult(intent, 0);
	    }

    @OnClick(R.id.pickpic)
    void pickpic() {
        Intent intent = new Intent(Intent.ACTION_PICK);
        intent.setType("image/*");
        startActivityForResult(intent, 1);
    }

    @Bind(R.id.tessResults)
    TextView tessResults;
    @Bind(R.id.imageView)
    ImageView imageView;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_orc);
        ButterKnife.bind(this);
        handler = new Handler(){
            @Override
            public void handleMessage(Message msg) {
                super.handleMessage(msg);
                tessResults.setText((String)msg.obj);
            }
        };
    }

    @Override
    protected void onActivityResult(int requestCode, int resultCode, Intent data) {
        Bitmap bitmap = null;
        if (requestCode == 1) {
            Uri uri = data.getData();
            try {
                bitmap = MediaStore.Images.Media.getBitmap(this.getContentResolver(), uri);
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
        if (requestCode == 0) {
            Bundle bundle = data.getExtras();
            bitmap = (Bitmap) bundle.get("data");

        }
        String string = null;
        ByteArrayOutputStream bStream = new ByteArrayOutputStream();
        bitmap.compress(Bitmap.CompressFormat.JPEG, 0, bStream);
        byte[] bytes = bStream.toByteArray();
        string = Base64.encodeToString(bytes, Base64.NO_WRAP);
        final String str = string;
	//        try {
	//            string = URLEncoder.encode(string,"UTF-8");
	//        }catch (Exception e){
	//            e.printStackTrace();
	//        }
        imageView.setImageBitmap(bitmap);
        new Thread(new Runnable() {
            @Override
            public void run() {
                OkHttpClient okHttpClient = new OkHttpClient();
                RequestBody requestBody = new FormEncodingBuilder()
                        .add("fromdevice", "android")
                        .add("clientip", "10.10.10.0")
                        .add("detecttype", "LocateRecognize")
                        .add("languagetype", "CHN_ENG")
                        .add("imagetype", "1")
                        .add("image", str)
                        .build();
                final Request request = new Request.Builder()
                //填写自己的apikey
                        .addHeader("apikey", "a8e0da3977de6d259e082a802035b93c")
                        .url(url)
                        .post(requestBody)
                        .build();
                okHttpClient.newCall(request).enqueue(new Callback() {
                    @Override
                    public void onFailure(Request request, IOException e) {

                    }

                    @Override
                    public void onResponse(Response response) throws IOException {
                        String ret = response.body().string();
                        String retStr = "";
                        try {
                            JSONObject jsonObject = new JSONObject(ret);
                            JSONArray jsonArray = jsonObject.getJSONArray("retData");
                            for (int i = 0; i < jsonArray.length(); i++) {
                                System.out.println(i);
                                retStr += ((JSONObject) (jsonArray.get(i))).getString("word");
                            }
                        } catch (Exception e) {
                            e.printStackTrace();
                        }
                        Message message = Message.obtain();
                        message.obj = retStr;
                        handler.sendMessage(message);
                        System.out.println("ok");
                    }
                });
            }
        }).start();
    }

	}    
  
xml文档：  
  
	<?xml version="1.0" encoding="utf-8"?>
	<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
	    xmlns:tools="http://schemas.android.com/tools"
	    android:layout_width="match_parent"
	    android:layout_height="match_parent"
	    tools:context=".OcrActivity">

    <ImageView
        android:id="@+id/imageView"
        android:layout_width="match_parent"
        android:layout_height="200dp"
        android:layout_below="@+id/takepic"
        android:scaleType="center" />

    <TextView
        android:id="@+id/tessResults"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:layout_alignParentBottom="true"
        android:layout_below="@id/imageView"
        android:text="null" />

    <Button
        android:id="@+id/takepic"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="拍照"
        android:layout_alignParentTop="true"
        android:layout_toStartOf="@+id/pickpic"
        android:layout_marginEnd="58dp" />

    <Button
        android:id="@+id/pickpic"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="相册"
        android:layout_alignParentTop="true"
        android:layout_alignParentEnd="true"
        android:layout_marginEnd="73dp" />


	</RelativeLayout>



