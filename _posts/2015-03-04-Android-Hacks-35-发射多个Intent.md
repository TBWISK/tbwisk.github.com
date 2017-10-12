---
layout: post
category : read
tagline: "发射多个意图"
tags : [读书笔记,android]
date: 2015-03-24 15:01:45
title: Android-hacks 35 发射多个Intent
---


> ###发射多个意图   

在android 开发中最有特色之一的系统是Intent。如果你想要分享一些东西给其他的应用程序，你可以使用Intent完成这个任务。如果你想要打开一个链接，也可以用Intent完成。在android中，几乎任何的事情都可以用Intent去完成。    
    
如果你使用过手机的通信app工具，whatsapp，你可能知道你可以分享图片来自 gallery或者拍照 给你的联系人。      

####拍摄照片   
```java
Intent takePhotoIntent = new Intent(
        MediaStore.ACTION_IMAGE_CAPTURE);
Intent chooserIntent = Intent.createChooser(takePhotoIntent,
						"拍摄照片");
startActivityForResult(chooserIntent, 1);
```
  
####从gallery中选择一个图片   
```java
Intent pickIntent = new Intent(Intent.ACTION_GET_CONTENT);
pickIntent.setType("image/*");
Intent chooserIntent = Intent
			.createChooser(pickIntent, "请选择照片");
startActivityForResult(chooserIntent, 2);
```
<img src="/assets/picture/20150304110856.png">   
####把上面的intent混合   
从android api 5依赖，我们可以创建一个 chooser 和 添加更多的intents 。这个意味着代替了只有一种类型的intent，我们可以用几个 intent ，例如下面的：
```java
Intent pickIntent = new Intent(Intent.ACTION_GET_CONTENT);
pickIntent.setType("image/*");

Intent takePhotoIntent = new Intent(
			MediaStore.ACTION_IMAGE_CAPTURE);

Intent chooserIntent = Intent.createChooser(pickIntent, "全部");
chooserIntent.putExtra(Intent.EXTRA_INITIAL_INTENTS,
				new Intent[] { takePhotoIntent });
startActivityForResult(chooserIntent, 0);
```
创建一个image intent   
创建一个拍照 的intent     
把 拍照的intent 用putExtra添加到其他意图上。  
<img src="/assets/picture/20150304111109.png">    
 使用前面的代码将会显示intent的处理效果。但请记住，我们需要在Activity 中重写 onActivityResult()方法，来做一些需要处理图片的事情。   

####下划线      

如果你可能理解intents 是如何工作的，这很重要。这个是android 环境中一个重要的部分。例如，如果你的app 使用一些代码，调用手机中本来有的文件管理器，这会让用户有更友好的体验。   
  