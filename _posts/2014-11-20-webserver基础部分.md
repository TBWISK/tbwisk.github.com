---
layout: post
category : lessons
title : 2014/11/20/webserver基础部分
tagline: "关于WSDL"
tags : [java]
date: 2014-11-20 15:01:45
title: webserver基础部分
---

## webserver，看起来一个高端的东西  
有时候，我们在用浏览器的时候，看网页上的东西，是不是觉得信息量很大呢？然后觉得互联网的世界是丰富多彩的。好吧，废话说多了。   
WSDL，网络服务描述语言是一个用来描述Web服务和说明如何与Web服务通信的XM语言。为用户提供详细的接口说明书。    
那么如何去阅读这样的一个语言呢？就需要看WSDL 的文档结构了    
在WSDL的文档结构中    

	<service> 整个webservice的服务视图，包括了所有的服务端点。
	<binding> 为每个端口定义消息格式和协议细节。
	<portType> 描述webservice可被执行的操作，以及相关的消息，通过binding指向portType
	<message> 定义一个操作（方法）的数据参数（可有多个参数）  
	<types> 定义webservice使用的全部数据类型。

通常，我们阅读的是wdsl文档来获取信息，和如何去使用。当然，在编程上面，我们需要使用wsimport 工具（此工具jdk包中自带，在windows中cmd可直接使用）  
使用方法： wsimport [options] <WSDL_URI>
详细的方法可查看 wsimport -help

![photo]({{site.url}}/assets/picture/20141020_wsdlhelp.jpg)  

在通常的服务器通信中，webserver 和client的通信中，简单的数据传递可以采用socket通信，在通信的过程中需要选择好 输入流和输入流，因为在某些的数据中有可能会出现乱码的情况。InputStream和OutputStream中传递的是二进制数据，二进制数据需要转换成ASCII 码或者UTF-8等，这个转码的需求通常看服务器和客户端的协商。有些人推荐使用DataInputStream和DataOutputStream。

测试：{{picture_url}}{picture_url} {site.url}
{{site.url}}






