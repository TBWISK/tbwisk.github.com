---
layout: post
category : read
tagline: "Toast的使用"
tags : [读书笔记,android]
date: 2015-02-11 15:01:45
title: Android Hacks 16 android Toast 位置改变
---


> ### Toast的使用   

Toast的使用是十分的简单的。    
```java
Toast.makeText(this, "Hi!", Toast.LENGTH_SHORT).show();
```
有时候我们需要Toast的弹出的位置刚好当前控件的位置，那么如何去控制呢？  
这个时候涉及到    

```java
public void setGravity(int gravity, int xOffset, int yOffset);   
```

下面是界面的代码    
```xml
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent" >

    <Button
        android:id="@+id/button1"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_alignParentLeft="true"
        android:layout_alignParentTop="true"
        android:text="Button" />

    <Button
        android:id="@+id/button2"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_alignParentRight="true"
        android:layout_alignParentTop="true"
        android:text="Button" />

    <Button
        android:id="@+id/button3"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_alignParentBottom="true"
        android:layout_alignParentLeft="true"
        android:text="Button" />

    <Button
        android:id="@+id/button4"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_alignParentBottom="true"
        android:layout_alignParentRight="true"
        android:text="Button" />

</RelativeLayout>

```

下面是Activity的代码  

```java
public class Test7 extends Activity {
	@Override
	protected void onCreate(Bundle savedInstanceState) {
		// TODO Auto-generated method stub
		super.onCreate(savedInstanceState);
		setContentView(R.layout.test7);
		Button button1 = (Button) findViewById(R.id.button1);
		Button button2 = (Button) findViewById(R.id.button2);
		Button button3 = (Button) findViewById(R.id.button3);
		Button button4 = (Button) findViewById(R.id.button4);
		button1.setOnClickListener(onClickListener);
		button2.setOnClickListener(onClickListener);
		button3.setOnClickListener(onClickListener);
		button4.setOnClickListener(onClickListener);
	}

	private OnClickListener onClickListener = new OnClickListener() {

		@Override
		public void onClick(View v) {

			switch (v.getId()) {

			case R.id.button1:
				Toast toast1 = Toast.makeText(getApplicationContext(),
						"Bottom button1!", Toast.LENGTH_SHORT);
				toast1.setGravity(Gravity.TOP | Gravity.LEFT, 0, 0);
				toast1.show();
				break;
			case R.id.button2:
				Toast toast2 = Toast.makeText(getApplicationContext(),
						"Bottom button2!", Toast.LENGTH_SHORT);
				toast2.setGravity(Gravity.TOP | Gravity.RIGHT, 0, 0);
				toast2.show();
				break;
			case R.id.button3:
				Toast toast3 = Toast.makeText(getApplicationContext(),
						"Bottom button3!", Toast.LENGTH_SHORT);
				toast3.setGravity(Gravity.BOTTOM | Gravity.LEFT, 0, 0);
				toast3.show();
				break;
			case R.id.button4:
				Toast toast4 = Toast.makeText(getApplicationContext(),
						"Bottom button4!", Toast.LENGTH_SHORT);
				toast4.setGravity(Gravity.BOTTOM | Gravity.RIGHT, 0, 0);
				toast4.show();
				break;

			default:
				break;
			}

		}
	};
}
```

#### 知识点    

关键的核心 是 
toast.setGravity(Gravity.BOTTOM | Gravity.RIGHT, 0, 0);  


预览：  
<img src="/assets/picture/20150210170817.png">

预览：  
<img src="/assets/picture/20150210170840.png">

预览：  
<img src="/assets/picture/20150210170854.png">

预览：  
<img src="/assets/picture/20150210170930.png">

### Tag  
注重某些类的内部结构，学会查看源码。  

  