---
layout: post
category : lessons
tagline: "golang"
tags : [golang]
date: 2015-08-16 15:01:45
title:   mac下如何配置golang环境
---

### 首先，我们需要知道的是golang是什么？   
golang是由google在09年开出的语言？   
目的是系统级别的架构   
那么现在告诉你如何安装golang，进入golang 的世界。   

 在这里的主题是mac的安装环境，虽然有很多种配置，但我会按照我个人的配置方法去写。

 我不知道为什么mac被程序员所推荐使用，认为是程序员开发的利器，因为我刚刚到手不到一个星期，没错，我是个新手，在很多方面都是个新手。但要学会去努力，学会去学习，那么肯定会有所悟。    

### 开始配置golang   

 我们使用的是brew去下载配置golang。首先，进入   
 `http://brew.sh/index_zh-cn.html`   
 查看脚本命令   


   ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"   


其实mac自带了ruby和python这俩门语言。在终端 terminal.app 中输入上面的命令后，在等安装完brew后，我们可以输入   

__brew install go__

这个简单的命令后的我们就会自动安装go在brew仓库中的正式版。当然，我们也可以指定版本安装哦~brew 是个有趣的东西，很多人用来管理包的依赖问题。    

在install go 成功后，我们这个时候可以输入 go env 是看下golang 的配置问题。这个时候你会发现goroot的路径已经有，那么差什么呢？   
输入 go get XXXX XXXX是随意github上go的第三方库，你会看见缺乏GOPATH路径，那么这个时候怎么办呢？ 
在某个文件夹下面新建一个GOPATH文件，然后根据_ go help path _ 这个命令返回的内容趣新建一个src、bin、pkg的文件夹。好了，最后的重头戏来了，这个时候如何配置go 的gopath到这个目录下呢？    
   
   常人的思维应该是直接点   
   `export GOPATH=/Users/youyou/codingspace/gopath`


在输入之后你继续运行 go env 咦，还真的好像配置好环境了，这个时候开心的配置IDE去了。然后不小心关闭终端，重新打开……怎么又不行了，go env 怎么GOPATH 为空 。一开始以为权限不够，那么就提升权限……后来发现还是不行。
  
那么最后的办法就是 
  

      cd  GOPATH=/Users/youyou/codingspace/gopath  
      vim ~/.bash_profile
      加入
      export GOPATH=/Users/youyou/codingspace/gopath  
      export PATH=$PATH:/Users/youyou/codingspace/gopath/bin    
      保存   
      在终端输入 source ~/.bash_profile    


好了，走到这一步，再也不用担心环境的问题了，至于  `bash_profile`的问题，它是一个最重要的配置文件，在用户每次登陆的时候都会被读取。  

### 选择IDE   
IED是很重要的选择，当然你也可以选择用文本去慢慢的敲打代码，但好的IDE可以让你的效率提高很多哦。   
有很多种方法例如liteIde，vim，sublime，我个人使用的是 LiteIDE ，因为这个简单操作容易配置。什么？LiteIDE还要配置？没错……是需要配置的，因为这个下载的时候的默认路径是不同的，那么先下载LiteIDE先，从哪里下载呢？   
   
   [golangtc下载](http://www.golangtc.com/download/liteide)   
   [sourceforge 的下载](http://sourceforge.net/projects/liteide/)

 当我们下载完后，把LiteIDE拉取到应用里面。打开LiteID，点击查看--编译当前环境 
<img src="/assets/picture/BE33CA46-23F0-4FF2-AC76-BE858736C423.png">    
在这里我们需要做的时什么？修改的时GOROOT的路径还有GOBIN的路径，还有GOPATH的路径   
那么说到这，未来要好好加油哦，这个世界很大。知识是十分丰富的。



