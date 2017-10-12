---
layout: post
category : read
tagline: "很多情况都适用"
tags : [读书笔记,android]
date: 2015-02-12 15:01:45
title: Android-Hacks 24 处理空的ListView
---


> ### 如何处理listView是空的情况  

在这种的情况下，有什么是我们需要注意的呢?   
首先，我们用到的方法是 listView.setEmptyView(View v)   

    从类的继承角度而言 ListView -> AbsListView -> AdapterView<T> 

setEmptyView(View emptyView) 在 AdapterView类中找到   

使用的时候，需要定义在同一布局内，代码新建的控件是无法在在setEmptyView中使用。  

从集成的方法而言，只要是父类 是 AdapterView 都可以使用 setEmptyView 这个布局为空的函数。

下面是布局文件
```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical" >

    <ListView
        android:id="@+id/listview"
        android:layout_width="wrap_content"
        android:layout_height="155dp" >
    </ListView>

    <ImageView
        android:id="@+id/imageview"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:background="@drawable/ic_launcher" />

</LinearLayout>
```

下面的是代码
```java
public class Test8 extends Activity {
	private ListView listView;

	@Override
	protected void onCreate(Bundle savedInstanceState) {
		// TODO Auto-generated method stub
		super.onCreate(savedInstanceState);
		setContentView(R.layout.test8);
		listView = (ListView) findViewById(R.id.listview);
		listView.setEmptyView(findViewById(R.id.imageview));
		List<String> list = new ArrayList<String>();
//		list.add("1");
//		list.add("2");
//		list.add("3");
//		list.add("4");
//		list.add("5");
//		list.add("6");
//		list.add("7");
		listView.setAdapter(new Test8_Adapter(list, getApplicationContext()));
		
	}
}
```

#### 知识点   
适配器为空的情况。   
预览：  
<img src="/assets/picture/20150211163651.png">  



这个是listview的适配器不为空的情况
预览：  
<img src="/assets/picture/20150211164137.png">

### Tag  
多点阅读，多点了解。
从上面的情况可以知道，甚至可以拓展，当读取网络数据显示在listview的时候，如果找不到数据，可以用这个来显示，提高界面的友好性。  
  