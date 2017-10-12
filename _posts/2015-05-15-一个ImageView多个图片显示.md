---
layout: post
category : lessons
tagline: "Canvas 的简单使用"
tags : [android]
date: 2015-05-15 15:01:45
title: 一个ImageView多个图片显示
---

### 目的   
首先是，有时候我们看见别人的一个ImageView控件里面显示多个图片。   
那么，这个是如何实现的呢？
这个就是我们的目的。   
如下图：
<img src="/assets/picture/20150515195228.png"> 


### 思考  
如果是你，你会如何去实现呢？   
那么思考……数据源是什么？   
数据源是要显示的多个图片……   
重新写一个Imageview布局？  
然后在 Imageview里面实现逻辑？   


效果图：   
<img src="/assets/picture/20150515195618.png"> 

---

一开始我的思路是使用drawable 或者 R.drawable 资源，然后拼接图片  
   
后来，我的实现是利用canvas去画图……把多个bitmap拼接起来   

首先create 一个bitmap作为画布使用  
Canvas canvas = new Canvas(bitmap)   
在这个时候，bitmap需要先定义好bitmap 的宽度和长度哦~~   
最后使用    
canvas.drawBitmap(Bitmap bitmap1, Rect src, Rect dst, Paint paint) 
就可以把bitmap1画进新的bitmap中    
__重点来了__   
Rect src  这个是原来图片的取值范围   
Rect dst  这个是bitmap1 在新画图中的位置   


### 那么代码来了……  
下面的代码中处理的是1个~3个的bitmap   当然，没有的话会直接报错哦。
```java
public class DrawPicture{
    private Bitmap bitmap1;
    private Bitmap bitmap2;
    private Bitmap bitmap3;
    private int number;

    /**
     * 类似微信朋友圈显示图片
     * 一个imageview 显示多个图片
     * 参数 bitmap>=1
     * 本质上是返回bitmap 把多个bitmap重新画成一个bitmap
     * @param bitmaps
     */
    public DrawPicture(Bitmap...bitmaps){
        number = bitmaps.length;
        if (number==1){
            bitmap1 = bitmaps[0];

        }else if (number==2){
            bitmap1 = bitmaps[0];
            bitmap2 = bitmaps[1];
        }else if (number>=3){
            bitmap1 = bitmaps[0];
            bitmap2 = bitmaps[1];
            bitmap3 = bitmaps[2];
        }else {
           throw new IllegalArgumentException("没有初始化bitmap");
        }
    }
    public Bitmap getBitmap(){
       if (number==1){
           return bitmap1;
       }else if (number==2){
           Bitmap bitmap = Bitmap.createBitmap(bitmap1.getWidth(),bitmap1.getHeight(),bitmap1.getConfig());
           Canvas canvas = new Canvas(bitmap);

           Rect rect = new Rect(bitmap1.getWidth()/2-bitmap1.getWidth()/4,0,bitmap1.getWidth()/2+bitmap1.getWidth()/4,bitmap1.getHeight());
           Rect rect1 = new Rect(0,0,bitmap.getWidth()/2-2,bitmap.getHeight());
           canvas.drawBitmap(bitmap1,rect,rect1,null);

           Rect rect2 = new Rect(bitmap2.getWidth()/2-bitmap2.getWidth()/4,0,bitmap2.getWidth()/2+bitmap2.getWidth()/4,bitmap2.getHeight());
           Rect rect3 = new Rect(bitmap.getWidth()/2+2,0,bitmap.getWidth(),bitmap.getHeight());
           canvas.drawBitmap(bitmap2,rect2,rect3,null);
           return bitmap;

       }else if (number>=3){
           Bitmap bitmap = Bitmap.createBitmap(bitmap1.getWidth(),bitmap1.getHeight(),bitmap1.getConfig());
           Canvas canvas = new Canvas(bitmap);

           Rect rect = new Rect(subaverage(bitmap1.getWidth()),0,addaverage(bitmap1.getWidth()),bitmap1.getHeight());
           Rect rect1 = new Rect(0,0,bitmap.getWidth()/2-2,bitmap.getHeight());
           canvas.drawBitmap(bitmap1,rect,rect1,null);

           Rect rect2 = new Rect(subaverage(bitmap2.getWidth()),subaverage(bitmap2.getHeight()),addaverage(bitmap2.getWidth()),addaverage(bitmap2.getHeight()));
           Rect rect3 = new Rect(bitmap.getWidth()/2+2,0,bitmap.getWidth(),bitmap.getHeight()/2-2);
           canvas.drawBitmap(bitmap2,rect2,rect3,null);

           Rect rect4 = new Rect(subaverage(bitmap3.getWidth()),subaverage(bitmap3.getHeight()),addaverage(bitmap3.getWidth()),addaverage(bitmap3.getHeight()));

           Rect rect5 = new Rect(bitmap.getWidth()/2+2,bitmap.getHeight()/2+2,bitmap.getWidth(),bitmap.getHeight());
           canvas.drawBitmap(bitmap3,rect4,rect5,null);
            return bitmap;
       }

        return null;
    }
    private int addaverage(int number) {
        return  number/2+number/4;
    }
    private int subaverage(int number) {
        return  number/2-number/4;
    }
}
```

####使用方法：   
```java
ImageView imageView = findViewById(R.id.imageView)
imageView.setImageBitmap(new DrawPicture(bitmap1,bitmap2,bitmap3));

```

### 小结   
从层面上而言，是几个bitmap合并成一个bitmap。从实际开发上，或许可以这样加载本地图片。但从网络上加载就比较麻烦，因为没有写好的缓存机制，例如内存缓存，硬盘缓存。     
从技术层面上，就是就是讲解了canvas.drawBitmap 的使用。
