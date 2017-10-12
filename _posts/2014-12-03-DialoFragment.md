---
layout: post
category : lessons
title : 2014/12/03/DialogFragment的使用
tagline: "简单的使用"
tags : [android]
date: 2014-12-03 15:01:45
title: DialoFragment
---

## 首先需要了解的是，什么是Fragment
Fragment是碎片化之类的东西，生命周期和Activity类似，但却依赖Activity才能创建。下面是如何使用DialogFragment    

--------------------------------------------API文档介绍
public class FragmentActivity    
extends Activity  

Base class for activities that want to use the support-based Fragment and Loader APIs.     

When using this class as opposed to new platform's built-in fragment and loader support, you must use the getSupportFragmentManager() and getSupportLoaderManager() methods respectively to access those features.     

Known limitations:    

*  When using the <fragment> tag, this implementation can not use the parent view's ID as the new fragment's ID. You must explicitly specify an ID (or tag) in the <fragment>.    

*  Prior to Honeycomb (3.0), an activity's state was saved before pausing. Fragments are a significant amount of new state, and dynamic enough that one often wants them to change between pausing and stopping. These classes throw an exception if you try to change the fragment state after it has been saved, to avoid accidental loss of UI state. However this is too restrictive prior to Honeycomb, where the state is saved before pausing. To address this, when running on platforms prior to Honeycomb an exception will not be thrown if you change fragments between the state save and the activity being stopped. This means that in some cases if the activity is restored from its last saved state, this may be a snapshot slightly before what the user last saw.    

----------------------------------------------
MainActivity代码，R.layout.activity_main 中就只有一个Button
```java
package com.example.ff;

import android.annotation.SuppressLint;
import android.annotation.TargetApi;
import android.os.Build;
import android.os.Bundle;
import android.support.v4.app.Fragment;
import android.support.v4.app.FragmentActivity;
import android.support.v4.app.FragmentManager;
import android.support.v4.app.FragmentTransaction;
import android.support.v7.app.ActionBarActivity;
import android.util.Log;
import android.view.Menu;
import android.view.MenuItem;
import android.view.View;
import android.view.View.OnClickListener;
import android.widget.Button;
public class MainActivity extends FragmentActivity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        
        Button button = (Button)findViewById(R.id.button);
        button.setOnClickListener(new OnClickListener() {
			
			@TargetApi(Build.VERSION_CODES.HONEYCOMB) @SuppressLint("NewApi") @Override
			public void onClick(View arg0) {
				// TODO Auto-generated method stub
//				  Fragment1 dialogFragment = Fragment1.newInstance(
//			                "Are you sure you want to do this?");
//			        dialogFragment.show(getSupportFragmentManager(), "hello");
//				
				 android.support.v4.app.Fragment gr = Fragment2.newInstance();
//			        gr.show(getSupportFragmentManager(), "dialog");
				 android.support.v4.app.FragmentManager fr = getSupportFragmentManager();
				 android.support.v4.app.FragmentTransaction	fra= fr.beginTransaction();
				 fra.add(gr, "dd");
				 fra.commit();
				 /*
				 *存在俩种实现方式，一个是 getSupportFragmentManager().beginTransaction().commit();
				 *一个是  fra.add(Fragment, "TAG");
				 */
			}
		});
    }
}

```

弹窗的部分DialogFragment
```java
package com.example.ff;

import android.app.Fragment;
import android.os.Bundle;
import android.support.annotation.Nullable;
import android.support.v4.app.DialogFragment;
import android.view.LayoutInflater;
import android.view.View;
import android.view.ViewGroup;
import android.widget.ImageView;
import android.widget.TextView;

public class Fragment2 extends DialogFragment{
	
	static  Fragment2 newInstance(){
		return new Fragment2();
	}
	
	@Override
	public View onCreateView(LayoutInflater inflater,
			@Nullable ViewGroup container, @Nullable Bundle savedInstanceState) {
		
		View v = inflater.inflate(R.layout.test01, container, false);  
        View tv = v.findViewById(R.id.test);  
        ((TextView)tv).setText("This is an instance of MyDialogFragment"); 
        ImageView image = (ImageView)v.findViewById(R.id.image);
        image.setImageResource(R.drawable.ic_launcher);
        return v;  
	}

}

```

弹窗的layout代码
```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    android:id="@+id/lay_out" >
    <TextView 
        android:id="@+id/test"
        android:text="oooooo"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"/>
    <ImageView 
        android:id="@+id/image"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"/>
</LinearLayout>
```


效果图:![photo]({{site.url}}/assets/picture/DialogFragment.jpg)


