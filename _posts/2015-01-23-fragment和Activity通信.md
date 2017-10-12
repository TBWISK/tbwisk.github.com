---
layout: post
category : lessons
title : fragment和Activity通信
tagline: "Fragment和Activity的通信"
tags : [android]
date: 2015-01-23 15:01:45
---


### Activity的生命周期
<img src="/assets/picture/20150124.png">    
从这个图中可以知道Activity 的生命周期    


### Fragment 生命周期
> Lifecycle  
> Though a Fragment's lifecycle is tied to its owning activity, it has its own wrinkle on the standard activity lifecycle. It includes basic activity lifecycle methods such as onResume(), but also important are methods related to interactions with the activity and UI generation.   

> The core series of lifecycle methods that are called to bring a fragment up to resumed state (interacting with the user) are: 

* 1.onAttach(android.app.Activity) called once the fragment is associated with its activity. 
* 2.onCreate(android.os.Bundle) called to do initial creation of the fragment. 
* 3.onCreateView(android.view.LayoutInflater, android.view.ViewGroup, android.os.Bundle) creates and returns the view hierarchy associated with the fragment. 
* 4.onActivityCreated(android.os.Bundle) tells the fragment that its activity has completed its own Activity.onCreate(). 
* 5.onViewStateRestored(android.os.Bundle) tells the fragment that all of the saved state of its view hierarchy has been restored. 
* 6 .onStart() makes the fragment visible to the user (based on its containing activity being started). 
* 7 .onResume() makes the fragment interacting with the user (based on its containing activity being resumed).   

> As a fragment is no longer being used, it goes through a reverse series of callbacks:   

* 1.onPause() fragment is no longer interacting with the user either because its activity is being paused or a fragment operation is modifying it in the activity. 
* 2.onStop() fragment is no longer visible to the user either because its activity is being stopped or a fragment operation is modifying it in the activity. 
* 3.onDestroyView() allows the fragment to clean up resources associated with its View. 
* 4.onDestroy() called to do final cleanup of the fragment's state. 
* 5.onDetach() called immediately prior to the fragment no longer being associated with its activity. 

#### Fragment的使用
fragment的使用有俩种方式  
1： 直接在空布局上添加
2： xml 中声明 fragment 布局

### fragment 和activity 如何通信
fragment 向activity 传递信息 是通过回调的方法    
activity 向 fragment 传递 信息 是通过setArguments(Bundle bundle)传递。Bundle 可用储存传递的数据结构类型。  

回调的使用：
```java
public class Dialog1 extends DialogFragment {
	public interface OnitemClick {
		public void setEdittext(View view, int id, String string);
	}
	private OnitemClick onitemClick;//回调的东西
	......
	onitemClick.setEdittext(null, R.id.edittext_sex, sex);//传递数据
	......
		@Override
	public void onAttach(Activity activity) {
		super.onAttach(activity);
		try {
			onitemClick = (OnitemClick) activity;
			//对implements interface OnitemClick
			//进行绑定，如果调用 Dialog1 的 Activity 没有 声明接口，那么会报错
		} catch (Exception e) {
			throw new ClassCastException(activity.toString()
					+ "must implement OnitemClick");
		}
	}
}


  
public class GetThePatientHardwareList extends BaseActivity implements
		 OnitemClick {
	@Override
	public void setEdittext(View view, int id, String string) {//数据接口
	// 对方法的实现
	
	}
}
```
Activity 向fragment 传递数据
```java
Dialog1 dia = new Dialog1();
Bundle bundle = new Bundle();
bundle.putInt("Type", 3);
bundle.putInt("position", position);
dia.setArguments(bundle);//bundle 就是传递的数据
getSupportFragmentManager().beginTransaction().add(dia, "").commit();

Fragment 如何接受数据呢    
直接简单粗暴
int type = getArguments().getInt("Type");
//在对type的接受 可实现 Fragment 的多态性和复用性
```
#### 总结
总结，想要实现。首先知道的是fragment 的生命周期，在fragment创建的时候会先绑定当前的activity，这个是 onAttach(Activity activity)。实现接口的回调方法和创建。
