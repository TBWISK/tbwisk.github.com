---
layout: post
category : read
title : Android Hacks 1 weights的使用
tagline: "数据标准"
tags : [读书笔记,android]
date: 2015-02-07 15:01:45
---


> ### 有人问，如何让一个 Button 是父布局的 50% width。  

这个看起来是个简单的问题，但 实际上，你会如何去思考这个问题呢？

有一个简单的方法

让LinearLayout 使用 weightSum 属性 ，它的子控件 使用 layout_weight 属性
例如  
```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="horizontal"
    android:gravity="center"
    android:weightSum="1" >
    <Button 
        android:layout_width="0dp"
        android:layout_height="wrap_content"
        android:text="On clikc"
        android:layout_weight="0.5"/>
</LinearLayout>
```
预览：  
<img src="/assets/picture/20150207123958.png">

第二个布局

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:gravity="center"
    android:orientation="horizontal"
    android:weightSum="1" >

    <Button
        android:layout_width="0dp"
        android:layout_height="wrap_content"
        android:layout_weight="0.5"
        android:text="On clikc" />

    <LinearLayout
        android:layout_width="0dp"
        android:layout_height="match_parent"
        android:layout_weight="0.5"
        android:background="#cccccc" >

        <Button
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:text="On clikc" />
    </LinearLayout>

</LinearLayout>
```

预览：  
<img src="/assets/picture/20150207124529.png">

### Tag  
从这个中获得了什么，首先，这是个简单的布局。然后你可以拓展到ImageView在Lanearlayout的使用，甚至Fragments 在布局中占的比例。控制好布局，在适应屏幕上更好。
  