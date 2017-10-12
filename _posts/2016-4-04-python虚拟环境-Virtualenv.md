---
layout: post
category : lessons
tagline: "虚拟环境"
tags : [python]
date: 2016-04-04 15:01:45
title:   python虚拟环境安装  
---

### 1：背景  
在我们使用python的时候，是不是觉得要pip安装的东西特别多呢？然后有时候，又因为升级某个套件而导致一些错误。那么如何去解决这个问题
这个时候我们需要使用到python的虚拟环境 Virtualenv   

### 2：Virtualenv  
Virtualenv用于创建独立的python环境，多个python相互独立，互不影响。   
它能够：  
a：在没有权限的情况下安装新套件  
b：不同应用可以使用不同的套件版本   
c：套件升级不影响其他应用   

#### 安装  
sudo apt-get install python-virtualenv   

#### 使用方法   
virtualenv [虚拟环境名称]  
如果创建 一个名字叫 env 的虚拟环境  
那么命令行使用是 virtualenv env   
默认情况下，虚拟环境会依赖系统环境中的site packages，就是说系统中已经安装好的第三方package也会默认安装在虚拟环境中，如果不想依赖这些package，那么可以加上参数--no-site-packages建立虚拟环境   
virtualenv --no-site-packages [虚拟环境名称]   

#### 启动虚拟环境  
ls  当使用ls命令的时候，你会发现有个env的文件夹   
这个时候使用命令  
cd env   
source ./bin/activite  
通常服务器的虚拟环境是跟服务器的生代码产环境同一个父目录。因为可以方便找到所需的虚拟环境和管理。   
当使用完命令后，会看见命令行会多一个 (env) ,env为虚拟环境的名称，接下来所有模块都只会安装到该目录中去。

#### 退出虚拟环境  
deactivate  

#### 在虚拟环境中安装python套件  
virtualenv 中的env文件夹中面可以看到有附带的pip安装工具，因此需要安装的套件可以直接运行：   
 
pip install [套件名称]  
  

如果没有启动虚拟环境，系统也安装了pip工具，那么套件将被安装在系统环境中，为了避免发生此事，可以在~/.bashrc 的文件中加上：
export PIP_REQUIRE_VIRTUALENV=true  
或者让在执行pip的时候让系统自动开启虚拟环境：   
export PIP_RESPECT_VIRTUALENV=true  

---

### 3：Virtualenvwrapper   
Virtualenvwrapper 是virtualenv 的扩展包，用于更方便管理虚拟环境，它可以做：   
a：将所有虚拟环境整合在一个目录下  
b：管理（新增，删除，复制）虚拟环境  
c：切换虚拟环境等操作   

#### 安装  
sudo pip install virtualenvwrapper   
当成功后还不能使用virtualenvwrapper,默认的virtualenvwrapper安装在/usr/local/bin 下面.这个时候我们需要下面的操作：   
1：创建目录用来厨房虚拟环境   
mkdir $HOME/.virtualenvs   
2：在~./bashrc 中添加行： export WORKON_HOME=$HOME/.virtualenvs   
3：在~/.bashrc中添加行：source /usr/local/bin/virtualenvwrapper.sh   
4：运行：source ~/.bashrc   

此时virtualenvwrapper 就可以使用啦   
1：列出虚拟环境列表  workon   或者 lsvirtualenv   
2：新建虚拟环境  mkvirtualenv [虚拟环境名称]  
3：启动、切换虚拟环境  workon [虚拟环境名称]   
4：删除虚拟环境  rmvirtualenv [虚拟环境名称]
5：离开虚拟环境  deactivate

#### 工欲善其事必先利其器   
工具很重要，学会工具的使用也十分重要。   
[博客地址](http://tbwisk.github.com)
