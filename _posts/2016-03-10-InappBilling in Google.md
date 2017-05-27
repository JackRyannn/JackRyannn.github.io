---
author: JackRyannn
comments: true
date: 2016-03-10 10:33:00 -0700
layout: post
title: InappBilling in Google
tags:
- Android

---  
  
#Google的应用内支付
##准备工作  
    
1.首先从Android sdk Manager里下载安装extra里的 Google Play Billing Library  
2.这时候在你的sdk目录下的/extras/google/play_billing/就会又个以.aidl结尾的文件。    
3. 在Tools > Android > SDK Manager中的Appearance & Behavior > System Settings > Android SDK里选择sdk tools的选项卡，下载安装google play billing library，如图所示。
  
![image](https://ooo.0o0.ooo/2016/03/09/56e0df7c4a6dc.png)  
  

4.然后在 File > New > Directory 输入aidl（建立一个aidl文件夹），再点击 File > New > Package中新建一个包，叫做com.android.vending.billing，然后把**IInAppBillingService.aidl**文件拷进去，形成这样的一个结构。

![image](https://ooo.0o0.ooo/2016/03/09/56e0e0e6dfccc.png)  
5.在manifest里添加权限：**<uses-permission android:name="com.android.vending.BILLING" />** 到此准备工作基本完成。最后别忘了build一下你的module，让aidl文件生成相应的java文件，才可以使用。  
  
##建立service连接  
  
在你的MainActivity里添加这段代码，建立app和google play的连接。   
这个ServiceConnection是bindService()的一个参数，用来表明activity和service的连接状态，如果连接成功则运行onServiceConnected()方法，即给mService赋值。

	  IInAppBillingService mService;
	  
    ServiceConnection mServiceConn = new ServiceConnection() {
        @Override
        public void onServiceDisconnected(ComponentName name) {
            mService = null;
        }

        @Override
        public void onServiceConnected(ComponentName name,
                                       IBinder service) {
            mService = IInAppBillingService.Stub.asInterface(service);
        }
    };  
然后在onCreate()方法中实现绑定，来读取service中的数据或方法    
	
	Intent serviceIntent =
	        new Intent("com.android.vending.billing.InAppBillingService.BIND");
	serviceIntent.setPackage("com.android.vending");
	bindService(serviceIntent, mServiceConn, Context.BIND_AUTO_CREATE);  
  
##商品查询  
  
示例代码：  
	  
	 public void query(View view){
	//为了防止阻塞主线程，所以新建一个线程
	        new Thread(){
	            @Override
	            public void run() {
	                super.run();
	                ArrayList<String> skuList = new ArrayList<String>();
	                skuList.add("开发者中心里设置的商品id，也称sku");
	                Bundle querySkus = new Bundle();
	                querySkus.putStringArrayList("ITEM_ID_LIST", skuList);
	                try {
	                    Bundle skuDetails;
	                    //3是API的版本号，然后是包名，购买方式是应用内购买inapp，最后把刚刚输入的商品id列表传进去
	                    skuDetails = mService.getSkuDetails(3,
	                            getPackageName(), "inapp", querySkus);
	                    int response = skuDetails.getInt("RESPONSE_CODE");
	                    //如果返回值为0，则说明请求成功
	                    if (response == 0) {
	                        ArrayList<String> responseList
	                                = skuDetails.getStringArrayList("DETAILS_LIST");
	
	                        for (String thisResponse : responseList) {
	                            JSONObject object = new JSONObject(thisResponse);
	                            String sku = object.getString("productId");
	                            String price = object.getString("price");
	                            Log.v("info",sku+"-----"+price);
	                        }
	                    }
	                } catch (RemoteException e) {
	                    e.printStackTrace();
	                } catch (JSONException e) {
	                    e.printStackTrace();
	                }
	
	            }
	
	        }.run();
	    }
  
##	商品购买    

      Bundle buyIntentBundle = mService.getBuyIntent(3, getPackageName(),"商品的id", "inapp", "developerPayload（商品的附加信息，google在返回购买信息的时候会返回）");
        PendingIntent pendingIntent = buyIntentBundle.getParcelable("BUY_INTENT");
        	//这个方法和startActivityForResult类似，然而我看了半天看不懂，只知道最后会调用onActivityResult()。        startIntentSenderForResult(pendingIntent.getIntentSender(), 1001, new Intent(), Integer.valueOf(0), Integer.valueOf(0),Integer.valueOf(0));  
  
购买完成后会调用onActivityResult()，可以在这里读取数据：  
  
	 @Override
	    protected void onActivityResult(int requestCode, int resultCode, Intent data) {
	            if (requestCode == 1001) {
	                int responseCode = data.getIntExtra("RESPONSE_CODE", 0);
	                String purchaseData = data.getStringExtra("INAPP_PURCHASE_DATA");
	                String dataSignature = data.getStringExtra("INAPP_DATA_SIGNATURE");
	
	                if (resultCode == RESULT_OK) {
	                    try {
	                        JSONObject jo = new JSONObject(purchaseData);
	                        String sku = jo.getString("productId");
	                        Log.v("info_code",String.valueOf(responseCode));
	                        //这里的签名是为安全性做的，可以用公钥来验证来源是否真的是google
	                        Log.v("info_signature",String.valueOf(dataSignature));
	                        Log.v("info",jo.toString());
	                    }
	                    catch (JSONException e) {
	                        e.printStackTrace();
	                    }
	                }
	            }
	
	    }  

