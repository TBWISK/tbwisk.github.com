---
layout: post
category : read
tagline: "FooterView and HeaderView"
tags : [读书笔记,android]
date: 2015-02-14 15:01:45
title: listview addheaderView 的用法
---


> ### 我们想要一个精彩的头部么，我们想要一个可以滑动的头部么   
通常而言，使用Gallery 和 ListView 布局可形成一个我们想要的。
但我们想要的是，listview滑动到底部的时候，Gallery会消失，那么就知道。我们应该如何去控制呢？   

这个时候我们可以查看ListView类中有函数 addHeaderView(View v)   
没错这个就是我们想要的。可以实现我们很多的东西。

Test9代码：
```java
public class Test9 extends Activity {

	@Override
	protected void onCreate(Bundle savedInstanceState) {
		// TODO Auto-generated method stub
		super.onCreate(savedInstanceState);
		setContentView(R.layout.test9);
		final String[] adapterData = new String[] { "AAA", "AAA", "AAA", "AAA",
				"AAA", "AAA", "AAA", "AAA", "AAA", "AAA", "AAA", "AAA", "AAA",
				"AAA", "AAA", "AAA", "AAA", "AAA", "AAA", "AAA", "AAA", "AAA",

				"AAA", "AAA" };
		ListView listView = (ListView) findViewById(R.id.listview);
		LayoutInflater inflater = LayoutInflater.from(this);
		ListView.LayoutParams params = new ListView.LayoutParams(
				ListView.LayoutParams.MATCH_PARENT,
				ListView.LayoutParams.WRAP_CONTENT);
		View v = inflater.inflate(R.layout.test9_1, listView, false);
		Gallery gallery = (Gallery) v.findViewById(R.id.gallery);
		v.setLayoutParams(params);

		gallery.setAdapter(new ImageAdapter(this));
		listView.addHeaderView(v, null, false);
		listView.addFooterView(v);
		listView.setAdapter(new ArrayAdapter<String>(this,
				android.R.layout.simple_list_item_1, adapterData));

	}

	class ImageAdapter extends BaseAdapter {
		private Context context;

		public ImageAdapter(Context context) {
			this.context = context;
		}

		@Override
		public int getCount() {
			return 10;
		}

		@Override
		public Object getItem(int position) {
			return position;
		}

		@Override
		public long getItemId(int position) {
			return position;
		}

		@Override
		public View getView(int position, View convertView, ViewGroup parent) {
			ImageView imageView = new ImageView(context);
			imageView.setImageResource(R.drawable.ic_launcher);
			imageView.setPadding(10, 10, 10, 10);
			return imageView;
		}

	}
}
```

test9.xml 代码：
```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical" >

    <ListView
        android:id="@+id/listview"
        android:layout_width="match_parent"
        android:layout_height="wrap_content" >
    </ListView>

</LinearLayout>
```

test9_1.xml代码
```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical" >

    <Gallery
        android:id="@+id/gallery"
        android:layout_width="match_parent"
        android:layout_height="wrap_content" />

</LinearLayout>
```
####知识点    

* BaseAdapter 的使用。  
* 了解构建布局的先后顺序。  
* 在listview setadapter之前实现 addHeader。  
* 了解ArrayAdapter 的用法 。  
* 了解LayoutInflater inflater = LayoutInflater.from(this); 的用法  

预览：  
<img src="/assets/picture/20150212173016.png">
   
<img src="/assets/picture/20150212173016.gif">
### Tag  
多点阅读。看点留意源码。

  