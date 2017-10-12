---
layout: post
category : lessons
title : Android开发 shape selector layer-list介绍
tagline: "自定义控件background"
tags : [android]
date: 2015-01-14 15:01:45
---


### Android开发：shape和selector和layer-list 的简单说明和使用
>  shape 和  selector 在 Android UI设计中经常用到。比如我们定义一个圆角Button，点击Button有些效果的变化，就要用到  shape  和  selector 。    
可以说 shape  和 selector 在美化控件中十分重要。  

> ### Shape
  
> 简介  
  
>     作用：XML中定义的几何形状  
>     位置：res/drawable/文件的名称.xml  
  
> 使用的方法：  

> 	Java代码中：R.drawable.文件的名称  
> 	XML中：Android:background="@drawable/文件的名称" 

> 属性：

```xml
<shape>  Android:shape=["rectangle" | "oval" | "line" | "ring"]   
	其中rectagle矩形，oval椭圆，line水平直线，ring环形  
<shape>中子节点的常用属性：  
<gradient>  渐变  
	Android:startColor    
		起始颜色  
	Android:endColor    
		结束颜色               
	Android:angle    //有问题
		渐变角度，0从左到右，90表示从下到上，数值为45的整数倍，默认为0；  
	Android:type    
		渐变的样式 liner线性渐变 radial环形渐变 sweep  
<solid >  填充  
	Android:color    
		填充的颜色  
<stroke >描边  
	Android:width   
		描边的宽度  
	Android:color   
		描边的颜色  
	Android:dashWidth  
		表示'-'横线的宽度  
	Android:dashGap   
		表示'-'横线之间的距离  
<corners >圆角  
	Android:radius    
		圆角的半径 值越大角越圆  
	Android:topRightRadius     
		右上圆角半径  
	Android:bottomLeftRadius   
		右下圆角角半径  
	Android:topLeftRadius   
		左上圆角半径  
	Android:bottomRightRadius   
		左下圆角半径  
<padding >填充  
	android:bottom="1.0dip"   
		底部填充  
	android:left="1.0dip"   
		左边填充  
	android:right="1.0dip"   
		右边填充  
	android:top="0.0dip"   
		上面填充  
```
- - -

### Selector    

> 简介   

> 	根据不同的选定状态来定义不同的现实效果   

> 	分为四大属性：  

	android:state_selected 是选中  
	android:state_focused 是获得焦点  
	android:state_pressed 是点击  
	android:state_enabled 是设置是否响应事件,指所有事件  
另：  
	android:state_window_focused 默认时的背景图片  
	引用位置：res/drawable/文件的名称.xml  

> 使用的方法：  

	Java代码中：R.drawable.文件的名称  
	XML中：Android:background="@drawable/文件的名称"  
```xml
<?xml version="1.0" encoding="utf-8" ?>       
<selector xmlns:Android="http://schemas.android.com/apk/res/android">     
<!-- 默认时的背景图片-->      
<item Android:drawable="@drawable/pic1" />        
<!-- 没有焦点时的背景图片 -->      
<item   
   Android:state_window_focused="false"        
   android:drawable="@drawable/pic_blue"   
   />       
<!-- 非触摸模式下获得焦点并单击时的背景图片 -->      
<item   
   Android:state_focused="true"   
   android:state_pressed="true"     
   android:drawable= "@drawable/pic_red"   
   />     
<!-- 触摸模式下单击时的背景图片-->      
<item   
   Android:state_focused="false"   
   Android:state_pressed="true"     
   Android:drawable="@drawable/pic_pink"   
   />      
<!--选中时的图片背景-->      
<item   
   Android:state_selected="true"   
   android:drawable="@drawable/pic_orange"   
   />       
<!--获得焦点时的图片背景-->      
<item   
   Android:state_focused="true"   
   Android:drawable="@drawable/pic_green"   
   />       
</selector>   
```

### layer-list   

> 简介：  
> 	将多个图片或上面两种效果按照顺序层叠起来   
例子：  
```xml
<?xml version="1.0" encoding="utf-8"?>
<layer-list xmlns:android="http://schemas.android.com/apk/res/android" >
     <item>  
      <bitmap android:src="@drawable/ic_launcher"  
        android:gravity="center" />  
    </item>  
    <item android:top="20dp" android:left="20dp">  
      <bitmap android:src="@drawable/ic_launcher"  
        android:gravity="center" />  
    </item>  
    <item android:top="40dp" android:left="40dp">  
      <bitmap android:src="@drawable/ic_launcher"  
        android:gravity="center" />  
    </item>  
</layer-list> 
```

```xml
<ImageView  
    android:layout_height="wrap_content"  
    android:layout_width="wrap_content"  
    android:src="@drawable/layers" />  
```

> 效果：  
<img src="/assets/picture/201501141.png">

### 整合
首先 确定的是，shape最底层的，然后 layer-list可以把shape凑合在一起。  
然后selector代表了控件有多种状态
> 例如android:state_selected 是选中     
> android:state_focused 是获得焦点    
> android:state_pressed 是点击    
> android:state_enabled 是设置是否响应事件,指所有事件   

在嵌套的时候用 item 

例如


    .
    |-- _selector
    |   |-- _item
    |   |   |-- _layer-list
    |   |   |    |-- _item
    |   |   |    |   |-- shape
    |   |   |    |-- _item
    |   |   |        |-- shape
    |   |-- _item
    |   |-- _item


