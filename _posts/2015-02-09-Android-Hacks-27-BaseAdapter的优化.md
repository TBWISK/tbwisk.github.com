---
layout: post
category : lessons
tagline: "如何使用BaseAdapter"
title : Android Hacks 27 BaseAdapter的优化
tags : [android,读书笔记]
date: 2015-02-09 15:01:45
---

## BaseAdapter  
某些需要明确的是BaseAdapter 是什么，是适配器，在什么场合可能会用到。例如listview，gridview的使用。那么继续baseadapter是基础的适配器，其他的适配器都是某这个衍生而来。那么可以肯定的说，这个BaseAdapter 可以很好的扩展自己需要的东西    
那么现在说的是，如何去优化一个BaseAdapter，什么？为什么要优化，节约资源，循环再用。

```java
/*
 * AuditReport 适配器
 */
public class AuditReportAdapter extends BaseAdapter {
	private LayoutInflater inflater;
	public AuditReportAdapter(Context context){
		super();
		inflater = LayoutInflater.from(context);
	}
	@Override
	public int getCount() {
		return 0;
	}

	@Override
	public Object getItem(int arg0) {
		return null;
	}

	@Override
	public long getItemId(int arg0) {
		return 0;
	}

	@Override
	public View getView(int arg0, View arg1, ViewGroup arg2) {
		Hoolder hoolder;//用于标志视图的东西
		if(arg1 == null){
			arg1 = inflater.inflate(R.layout.auditreport_layout_item, null);
			hoolder = new Hoolder();
			hoolder.imageView = (ImageView)arg1.findViewById(R.id.imageView);
			hoolder.nameTextView = (TextView)arg1.findViewById(R.id.textview_name);
			hoolder.dateTextView = (TextView)arg1.findViewById(R.id.textview_date);
			arg1.setTag(hoolder);//设置tag
		}else {
			hoolder = (Hoolder) arg1.getTag();//从tag中获取 hoolder
		}
		
		//Todo 
//		hoolder.imageView 
//		hoolder.nameTextView 
//		hoolder.dateTextView 
		
		
		return arg1;
	}

	private class Hoolder {//标志类
		ImageView imageView;
		TextView nameTextView;
		TextView dateTextView;
	}
}
```
#### Look 
从上面的代码中，可以知道的是，我们需要一个类来保存你想多个显示相同的东西。然后当当前视图不再缓存存在的时候，我们会新建一个，如果存在缓存中。那么我们会从缓存中获取数据。

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical" >

    <ImageView
        android:id="@+id/imageView"
        android:layout_width="100dp"
        android:layout_height="100dp"
        android:background="#cccccc" />

    <LinearLayout
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:orientation="horizontal" 
       >

        <TextView
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="上传者：" />
         <TextView
             android:id="@+id/textview_name"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="我是人" />
    </LinearLayout>

    <TextView
        android:id="@+id/textview_date"
        android:layout_width="100dp"
        android:layout_height="wrap_content"
        android:gravity="center"
        android:text="2014年" />

</LinearLayout>
```

#### Tag
多点学习，多点阅读