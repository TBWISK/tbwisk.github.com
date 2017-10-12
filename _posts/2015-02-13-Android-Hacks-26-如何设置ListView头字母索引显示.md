---
layout: post
category : read
tagline: "ListView小技巧"
tags : [读书笔记,android]
date: 2015-02-13 15:01:45
title: Android Hackers 26 如何设置listview头字母索引显示
---


> ### listview的头部设置  

在这里，通常我们想要在listview上面设置一个标签。   
例如选择省份的时候或者微信选择朋友的时候 有个A 标签在上面。   
那么明确的是 Listview的布局中有俩个TextView   
然后，对上面的TextView进行   
setVisibility(View.GONE) 和   
setVisibility(View.VISIBLE) 操作。  


主布局文件：  
```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical" >

    <ListView
        android:id="@+id/listview"
        android:layout_width="match_parent"
        android:layout_height="match_parent" >
    </ListView>

    <ImageView
        android:id="@+id/imageview"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:background="@drawable/ic_launcher" />

</LinearLayout>
```


Activity 代码：  
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
		list.add("1");
		list.add("2aa");
		list.add("2bb");
		list.add("aaa");
		list.add("bbb");
		list.add("bbbbb");
		list.add("baaaaabbbb");
		list.add("7");
		
		listView.setAdapter(new Test8_Adapter(list, getApplicationContext()));
		
	}
}
```



BaseAdapter 适配器的代码：
```java
public class Test8_Adapter extends BaseAdapter {
	private List<String> list;
	private LayoutInflater inflater;

	public Test8_Adapter(List<String> list, Context context) {
		super();
		this.list = list;
		inflater = LayoutInflater.from(context);
	}

	@Override
	public int getCount() {
		return list.size();
	}

	@Override
	public Object getItem(int position) {
		return list.get(position);
	}

	@Override
	public long getItemId(int position) {
		return position;
	}

	@Override
	public View getView(int position, View convertView, ViewGroup parent) {
		Hoolder hoolder;

		if (convertView == null) {
			hoolder = new Hoolder();
			convertView = inflater.inflate(R.layout.test8_item, null);
			hoolder.textView = (TextView) convertView
					.findViewById(R.id.textview);
			hoolder.textViewheader = (TextView) convertView
					.findViewById(R.id.textviewheader);
			convertView.setTag(hoolder);
		} else {
			hoolder = (Hoolder) convertView.getTag();
		}

		String string = list.get(position);

		if (position == 0
				|| string.charAt(0) != list.get(position - 1).charAt(0)) {
			hoolder.textViewheader.setVisibility(View.VISIBLE);
			hoolder.textViewheader.setText("" + string.charAt(0));
		} else {
			hoolder.textViewheader.setVisibility(View.GONE);
		}

		hoolder.textView.setText(list.get(position).toString());

		return convertView;
	}

	private class Hoolder {
		public TextView textViewheader;
		public TextView textView;
	}

}
```

item布局文件
```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:orientation="vertical"
     >

    <TextView
        android:id="@+id/textviewheader"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:textSize="25dp"
        android:text="dd"
        android:gravity="center"
        android:textColor="#000000" >
    </TextView>

    <TextView
        android:id="@+id/textview"
         android:textSize="20sp"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
          android:gravity="center" 
        android:textColor="#00ffff">
    </TextView>

</LinearLayout>
```
####知识点     

View.GONE  
View.VISIBLE  
BaseAdapter的使用  

预览：  
<img src="/assets/picture/20150211173726.png">



### Tag  
学会简单的思想方式避免问题的复杂化。减少复杂代码，那么以后的问题和繁琐的过程就会少很多。


### 看起来结束了，但还有的是后续……    
在滑动的过程，是不是想要头部的标签跟随着滑动而变化呢？   
那么只需要增加个小东西就可以完成呢~    
预览：  
<img src="/assets/picture/20150211192026.png">
    
<img src="/assets/picture/20150211192026.gif">
  修改代码部分：
  Activity：   
```java
 public class Test8 extends Activity {
	private ListView listView;
	private int top = 0;
	private TextView textViewheader;
	private List<String> list;
	@Override
	protected void onCreate(Bundle savedInstanceState) {
		// TODO Auto-generated method stub
		super.onCreate(savedInstanceState);
		setContentView(R.layout.test8);
		listView = (ListView) findViewById(R.id.listview);
		listView.setEmptyView(findViewById(R.id.imageview));
		textViewheader = (TextView)findViewById(R.id.textviewheader);
		 list = new ArrayList<String>();
		list.add("1");
		list.add("2aa");
		list.add("2bb");
		list.add("aaa");
		list.add("bbb");
		list.add("bbbbb");
		list.add("baaaaabbbb");
		list.add("7");
		list.add("1");
		list.add("2aa");
		list.add("2bb");
		list.add("aaa");
		list.add("bbb");
		list.add("bbbbb");
		list.add("baaaaabbbb");
		list.add("7");
		
		listView.setAdapter(new Test8_Adapter(list, getApplicationContext()));
		listView.setOnScrollListener(new OnScrollListener() {
			
			@Override
			public void onScrollStateChanged(AbsListView view, int scrollState) {
				// Tkong
				
			}
			
			@Override
			public void onScroll(AbsListView view, int firstVisibleItem,
					int visibleItemCount, int totalItemCount) {
				if(firstVisibleItem!=top){
					top=firstVisibleItem;
					settop(firstVisibleItem);
				}
			}
		});
		settop(0);
	}
	
	private void settop(int i ){
		textViewheader.setText(""+list.get(i).charAt(0));
	}
}
```


主布局部分：  
```xml
 <?xml version="1.0" encoding="utf-8"?>
<FrameLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical" >

    <ListView
        android:id="@+id/listview"
        android:layout_width="match_parent"
        android:layout_height="match_parent" >
    </ListView>

    <TextView
        android:id="@+id/textviewheader"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:gravity="center"
        android:text="dd"
        android:textColor="#000000"
        android:background="#cccccc"
        android:textSize="25dp" >
    </TextView>

    <ImageView
        android:id="@+id/imageview"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:background="@drawable/ic_launcher" />

</FrameLayout>
```
   
#### 有什么不同呢？

 在listview增加滑动检测  
 listView.setOnScrollListener()  

public void onScrollStateChanged(AbsListView view, int scrollState)
public void onScroll(AbsListView view, int firstVisibleItem,int visibleItemCount, int totalItemCount)   

理解上面俩个接口的实现。firstVisibleItem 是界面中显示的第一个 数据id  

