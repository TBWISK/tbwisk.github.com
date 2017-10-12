---
layout: post
category : read
tagline: "模拟时钟显示"
tags : [读书笔记,android]
date: 2015-02-10 15:01:45
title: Android Hacks 11 android 模拟液晶数字时钟 TextView
---

> ### 用TextView模拟 液晶上面的时间
首先，我们需要知道的是，液晶上面的数字和平常的数字有什么区别。  
液晶上的数字都是正正方方的，通常我们的数字都是软润的时候。这个时候需要使用到TTF字体或者TTC字体。


[demo中的TTF下载]({{ site.url }}/assets/ttf/Text.TTF) 右键下载哦   


```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical" >

    <RelativeLayout
        android:layout_width="wrap_content"
        android:layout_height="wrap_content" >

        <com.example.view.LedTextView
            android:id="@+id/textview3"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_centerInParent="true"
            android:shadowDx="0"
            android:shadowDy="0"
            android:text="88:88:88"
            android:textColor="#3300FF00"
            android:textSize="80sp" />

        <com.example.view.LedTextView
            android:id="@+id/main_clock_time"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_centerInParent="true"
            android:shadowColor="#00FF00"
            android:shadowRadius="10"
            android:text="08:43:02"
            android:textColor="#00FF00"
            android:textSize="80sp" />
    </RelativeLayout>

</LinearLayout>
```
上面的是布局文件
下面的是自定义TextView控件，先继承TextView，然后从asset
```java
public class LedTextView extends TextView {

	public LedTextView(Context context, AttributeSet attrs) {
		super(context, attrs);
		AssetManager assets = context.getAssets();
		final Typeface font = Typeface.createFromAsset(assets,
				"Text.TTF");
		setTypeface(font);
	}

}
```

#### 知识点    
学会集成TextView，找到字体资源。加载字体Typeface。   
在布局页面中预览会报错。运行程序是可以的。  
预览    
<img src="/assets/picture/20150210121454.png">  
预览：  
<img src="/assets/picture/20150210121709.png">  

### Tag  
在RelativeLayout中，俩个TextView，默认下面控件的会在上面的上面。

  