---
layout: post
category : lessons
tagline: "新控件RecyclerView"
tags : [android]
date: 2015-07-13 15:01:45
title: RecyclerView的简单实现
---


### RecyclerView 的类似ListView的简单使用   
首先，我们要了解什么是RecyclerView，RecyclerView是v7包中的一个新控件。主要是为了替代listview和gridview的使用，可以很好的提高开发的效率和提升内存优化效率。   
那么下面介绍的是类似listview多布局的使用，在listview中，我们可以使用多个布局文件item，让布局看起来更加多样化。   

<!-- more -->
那么RecyclerView是如何使用的呢？首先明确的是在eclipse中，有时候使用jar包的代码自动补全方面不怎么完善，这是个深坑。   

---

主要代码如下……
```java
public class TestActivity extends Activity {

	@Override
	protected void onCreate(Bundle arg0) {
		super.onCreate(arg0);
		setContentView(R.layout.activity_test_main);
		RecyclerView lRecyclerView = (RecyclerView) findViewById(R.id.recylerView);

		LinearLayoutManager layoutManager = new LinearLayoutManager(this);//RecyclerView的布局类型
		lRecyclerView.setLayoutManager(layoutManager);

		ArrayList<ItemType> one = new ArrayList<ItemType>();//数据源
		one.add(new ItemType(1,"name1"));
		one.add(new ItemType(1,"name2"));
		one.add(new ItemType(1,"name3"));
		one.add(new ItemType(1,"name4"));
		one.add(new ItemType(1,"name5"));
		one.add(new ItemType(1,"name6"));
		one.add(new ItemType(2,"name7"));
		one.add(new ItemType(2,"name8"));
		one.add(new ItemType(2,"name9"));
		one.add(new ItemType(2,"name10"));
		one.add(new ItemType(2,"name11"));
		one.add(new ItemType(3,"name12"));
		one.add(new ItemType(3,"name13"));
		one.add(new ItemType(3,"name14"));
		one.add(new ItemType(3,"name15"));
		one.add(new ItemType(3,"name16"));
		one.add(new ItemType(3,"name17"));
		one.add(new ItemType(3,"name18"));

		RecyAdapter adapter = new RecyAdapter(one, this);//适配器使用的
		lRecyclerView.setAdapter(adapter);
	}

	class RecyAdapter extends Adapter<RecyViewHolder> {
		private List<ItemType> lists;
		private Context context;
		private int TYPE1 = 1;
		private int TYPE2 = 2;
		private int TYPE3 = 3;
		private LayoutInflater inflater;

		public RecyAdapter(List<ItemType> lists, Context context) {
			this.lists = lists;
			this.context = context;
			inflater = LayoutInflater.from(context);
		}

		@Override
		public int getItemCount() {//总共有多少个item数目
			return lists.size();
		}

		@Override
		public int getItemViewType(int position) {//item的布局类型
			if (lists.get(position).type == TYPE1) {
				return TYPE1;
			} else if (lists.get(position).type  == TYPE2) {
				return TYPE2;
			} else {
				return TYPE3;
			}
		}

		@Override
		public void onBindViewHolder(RecyViewHolder arg0, int position) {//绑定数据源 注意坑就在这里，int类型是代表的是第几个……
				if (getItemViewType(position)==TYPE1) {
					arg0.tv_1.setText(""+lists.get(position).name);
				}else if (getItemViewType(position)==TYPE2) {
					arg0.tv_name.setText(""+lists.get(position).name);
					arg0.iv_2.setBackgroundColor(Color.RED);
				}else {
					arg0.iv_3.setBackgroundColor(Color.GREEN);
				}
		}

		@Override
		public RecyViewHolder onCreateViewHolder(ViewGroup arg0, int types) {//绑定布局 这里也坑，types的类型是代表什么类型的布局
			
			if (types==TYPE1) {
				View view = inflater.inflate(R.layout.activity_test_mian_item1, arg0, false);
				return new RecyViewHolder(view,types);
			}else if (types==TYPE2) {
				View view = inflater.inflate(R.layout.activity_test_mian_item2, arg0, false);
				return new RecyViewHolder(view,types);
			}else {
				View view = inflater.inflate(R.layout.activity_test_mian_item3, arg0, false);
				return new RecyViewHolder(view,types);
			}
		}

	}

	class RecyViewHolder extends ViewHolder {//ViewHolder 好熟悉吧，没错就是缓存用的
		public TextView tv_1;
		public ImageView iv_2;
		public TextView tv_name;
		public ImageView iv_3;
		private View view;
		public RecyViewHolder(View itemView,int type) {
			super(itemView);
			view = itemView;
			if (type==1) {
				tv_1 = (TextView) view.findViewById(R.id.tv_1);
			}else if (type==2) {
				iv_2 = (ImageView) view.findViewById(R.id.iv_2);
				tv_name = (TextView) view.findViewById(R.id.tv_name);
			}else {
				iv_3 = (ImageView) view.findViewById(R.id.iv_3);
			}
		}

	}
public class ItemType {//bean数据结构类型
	public ItemType(int type,String name){
		this.type = type;
		this.name = name;
	}
	public int type;
	public String name;
}
}
```

---


主要的布局文件如何下：   
R.layout.activity_test_main.xml布局  
```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical" >
     <android.support.v7.widget.RecyclerView
        android:id="@+id/recylerView"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        />

</LinearLayout>
```

---

 item布局文件如下
R.layout.activity_test_mian_item1.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical" >
    <TextView android:id="@+id/tv_1"
        android:layout_width="match_parent"
        android:layout_height="48dp"/>

</LinearLayout>
```

R.layout.activity_test_mian_item2.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical" >

    <ImageView
        android:id="@+id/iv_2"
        android:layout_width="match_parent"
        android:layout_height="20dp"
        android:background="#cccccc" />

    <TextView
        android:id="@+id/tv_name"
        android:layout_width="match_parent"
        android:layout_height="wrap_content" />

</LinearLayout>
```

R.layout.activity_test_mian_item3.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical" >

    <ImageView
        android:id="@+id/iv_3"
        android:layout_width="match_parent"
        android:layout_height="20dp"
        android:layout_margin="10dp"
        android:background="#cccccccc" />

</LinearLayout>
```

---

效果图：   
<img src="/assets/picture/20150713001525.png"> 

跟listview的使用一样，不过就是viewholder变成强制使用了。   

