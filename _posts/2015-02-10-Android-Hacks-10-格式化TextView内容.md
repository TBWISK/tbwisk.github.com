---
layout: post
category : read
tagline: "小技巧"
tags : [读书笔记,android]
date: 2015-02-10 15:01:45
title: Android Hacker 10 格式化TextView内容
---


> ### 想要一个特别点文本么，想要一个引人注目的内容么   
有什么，我们在使用一个TextView的时候，想要某些内容特别的引人注目，想要一些字体特别一点。有什么方法呢？  

layout布局文件  
```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical" >

    <TextView
        android:id="@+id/textview1"
        android:layout_width="match_parent"
        android:layout_height="wrap_content" />


    <TextView
        android:id="@+id/textview2"
        android:layout_width="match_parent"
        android:layout_height="wrap_content" />

</LinearLayout>
```

实现代码
```java
public class Test6 extends Activity {
	@Override
	protected void onCreate(Bundle savedInstanceState) {
		// TODO Auto-generated method stub
		super.onCreate(savedInstanceState);
		setContentView(R.layout.test6);

		TextView mTextView1 = (TextView) findViewById(R.id.textview1);
		String text = "Visit <a href=\"http://www.it-ebooks.info/\">It ebooks info</a><font color=\"red\">This is some text!</font>";
		mTextView1.setText(Html.fromHtml(text));
		mTextView1.setMovementMethod(LinkMovementMethod.getInstance());//涉及文本特定内容点击跳转

		TextView mTextView2 = (TextView) findViewById(R.id.textview2);
		mTextView2.setText("zaqweertyuiopsdfglxcvnmn");
		Spannable sText = new SpannableString(mTextView2.getText());
		sText.setSpan(new BackgroundColorSpan(Color.RED), 1, 4, 0);
		sText.setSpan(new ForegroundColorSpan(Color.BLUE), 5, 9, 0);
		mTextView2.setText(sText);

	}
}
```
#### 注意点
字符的转换，因为 "在java中需要用转换字符    
SpannableString 的使用的时候注意范围，因为有些范围是一定的，有些的范围是不明确的。如果越界会报错。
 TextView.setMovementMethod  
      
预览：     
<img src="/assets/picture/20150210102812.png">



预览：  
<img src="/assets/picture/20150210102847.png">

### Tag  
文本的使用，利用Html.fromHtml()，其实这些部分内容也可以弄个webview 加载本地的html，但相对于友好性而言，见人而异吧，具体事情具体分析。 

  