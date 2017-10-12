---
layout: post
category : lessons
tagline: "Http服务"
tags : [android]
date: 2015-05-28 15:01:45
title: android-网络http简单实用
---


### 主题   
在这个文本编辑中，主要是讲解一下android中http的基本使用。   
   
好吧，一开始就是使用第三库，基本上对于原生的http的请求不怎么熟悉。蜡烛  
文章中主要是讲解了http的一些简单的使用，例如获取网页信息，模拟登录，下载网络上的图片。  

切记，使用网络服务的时候在 AndroidManifest.xml 中声明权限……   
还有就是……记得 new Thread ……不然会报错   【蠢哭的我，在调了一个早上后发现了这样的事实   

---

网络权限
```xml
 <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>
```

---
 
在主线程中需要控制一个handler，处理从 Thread 网络请求中返回的数据  

handler代码如下：

```java
    private Handler handler = new Handler(){
        @Override
        public void handleMessage(Message msg) {
            super.handleMessage(msg);
            switch (msg.what){
                case 1:
                    Toast.makeText(context,"成功登录",Toast.LENGTH_SHORT).show();
                    break;
                case 2:
                    Toast.makeText(context,"密码错误",Toast.LENGTH_SHORT).show();
                    break;

                case 3:
                    Toast.makeText(context,"系统出错",Toast.LENGTH_SHORT).show();
                    break;
                case 4:
                    Log.w(TAG,"handler 4");
                    imageView.setImageBitmap((Bitmap) msg.obj);
                    break;
            }
        }
    };
```

handler处理的是主线程的界面数据，从Thread中接受返回的数据，但记得……hander可能会造成内存泄漏，那么如何去优化呢？   

优化  
```java
    private class  Myhandler extends Handler{
        private final WeakReference<MainActivity> mActivity;

        private Myhandler(WeakReference<MainActivity> mActivity) {
            this.mActivity = mActivity;
        }

        @Override
        public void handleMessage(Message msg) {
            super.handleMessage(msg);
        }
    }
```
弱引用去优化，那么当activity调用ondestory，也能顺利的回收Activity,不会造成内存泄漏问题。

---
当请求网络的时候的数据返回类型是
返回的数据类型    
1是登录正常   
2是密码错误   
```json
{"version":"1"}
{"version":"2"}
```

---

android 的Http原生请求  HttpURLConnection   
```java
    private void getnet(String id, String password) {

        try {
            String url = "http://192.168.1.130:1010/login?name=" + id + "&password=" + password;
//            String url = "http://www.baidu.com";
            URL url1 = new URL(url);
            HttpURLConnection httpURLConnection = (HttpURLConnection) url1.openConnection();
            httpURLConnection.setRequestProperty("Content-Type",
                    "application/x-www-form-urlencoded");
            httpURLConnection.setDoInput(true);
            httpURLConnection.setConnectTimeout(5000);

            httpURLConnection.setRequestMethod("GET");
            httpURLConnection.setDoOutput(true);
            httpURLConnection.setUseCaches(false);
            httpURLConnection.connect();

           if (httpURLConnection.getResponseCode()==200){
               InputStream inputStream = httpURLConnection.getInputStream();
               int i = -1;
               byte [] bytes = new byte[1024];
//            BufferedInputStream bufferedInputStream = new BufferedInputStream(inputStream);
//            while((i= inputStream.read(bytes))!=-1){
//                Log.w("inputStream","read "+i);
//            }
               BufferedReader bufferedReader = new BufferedReader(new InputStreamReader(inputStream));

               String result ="";
               String inputstring = "";
               while ( (inputstring=bufferedReader.readLine())!=null ){
                   result = result+inputstring;
               }
               Log.w("TAG",result);
               try {
                   JSONObject jsonObject = new JSONObject(result);
                   int  version =  jsonObject.getInt("version");
//                    Message message = Message.obtain();
//                   message.what = version;
                   handler.sendEmptyMessage(version);
               } catch (JSONException e) {
                   e.printStackTrace();
                   handler.sendEmptyMessage(3);
               }
//            OutputStream outputStream = httpURLConnection.getOutputStream();
//            ObjectOutputStream objectOutputStream = new ObjectOutputStream(outputStream);
//            objectOutputStream.write("Hello world".getBytes());
//            objectOutputStream.flush();
//            objectOutputStream.close();
           }else {
               Log.w(TAG,""+httpURLConnection.getResponseCode()+httpURLConnection.getResponseMessage());
           }
        } catch (MalformedURLException e) {
            e.printStackTrace();
            handler.sendEmptyMessage(3);
        } catch (IOException e) {
            e.printStackTrace();
            handler.sendEmptyMessage(3);
        }finally {

        }
    }
```
基本上android本身自带的就是这样，不过需要注意的是，是先读数据还是先写数据，http请求，是请求那么先write数据，然后才能获取数据。如果是先读取数据，那么就不能写了。   

---

apache的http请求   
对于获得的数据都要注意的是 获取getStatusCode http的状态，只有code==200的时候才是正常的。  

```java
    private void httpget(String string){

        try {
            HttpGet httpGet = new HttpGet(string);
            HttpClient httpClient = new DefaultHttpClient();
            HttpResponse httpResponse = httpClient.execute(httpGet);
            if (httpResponse.getStatusLine().getStatusCode()==200){
                String strResult = EntityUtils.toString(httpResponse.getEntity());
                JSONObject jsonObject = new JSONObject(strResult);
             int version =   jsonObject.optInt("version");
                handler.sendEmptyMessage(version);
            }
        } catch (IOException e) {
            e.printStackTrace();
            handler.sendEmptyMessage(3);
         } catch (JSONException e) {
            e.printStackTrace();
            handler.sendEmptyMessage(3);
        }
    }
```
当你在android上使用 apache的http请求的时候，你会发现……android的平台是不推荐使用的。  

---

下面的是介绍如何下载一个图片和在imageview上使用显示从网上获取的图片   

```java
private void  getImageBitmap(String url){
       try {
           Log.w(TAG,"getImageBitmap start");
           URL url1 = new URL(url);
         HttpURLConnection httpURLConnection = (HttpURLConnection) url1.openConnection();
           httpURLConnection.setDoInput(true);
           httpURLConnection.connect();
           InputStream inputStream = httpURLConnection.getInputStream();
            Bitmap bitmap =   BitmapFactory.decodeStream(inputStream);
            inputStream.close();
           String s = Environment.getExternalStorageDirectory().toString() +"/image.jpg";

           File file = new File(s);

           ByteArrayOutputStream outputStream = new ByteArrayOutputStream();
           bitmap.compress(Bitmap.CompressFormat.JPEG,100,outputStream);
           FileOutputStream fileOutputStream = new FileOutputStream(file);
           fileOutputStream.write(outputStream.toByteArray());
           fileOutputStream.close();

           Log.w(TAG,"bitmap get off :" + bitmap.getRowBytes());
           Message message = Message.obtain();
           message.obj = bitmap;
           message.what = 4;
           handler.sendMessage(message);
//        return bitmap;
       } catch (MalformedURLException e) {
           e.printStackTrace();
           handler.sendEmptyMessage(3);
       }
        catch (IOException e) {
           e.printStackTrace();
            handler.sendEmptyMessage(3);
       }
   }
```

这个时候肯定会说，就那么简单……没错，真的就是那么简单的从网上获取图片。   
其他的详细的细节可以以后慢慢谈   

>  需要注意的是  

>  线程 线程 还是线程 
  
  