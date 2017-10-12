---
layout: post
category : lessons
tagline: "adb的使用"
tags : [android]
date: 2015-11-24 15:01:45
title:   android-adb命令行使用
---


### a:背景   
温故而知信，在我们开发android的时候，我们很多时候都是不使用adb的，因为很少涉及到直接用命令行交互的情况。但有时候adb缺是不得不用的，这个可以很方便我们去调试应用以及传递某些数据。    

---   

### 1：adb   
一开始使用adb的时候，输入adb，回车。那么可以看到很多的命令列表。这个可以参照这个列表去学习运行adb

---  

### 2：显示链接列表   
使用这个命令可以显示链接到电脑设备上的手机设备和手机设备id   
__adb devices__   

```
List of devices attached
7C5A07B48684	device
```
如果多个设备一起连接，可以使用__adb -s DEVICE_ID__ 来指定一个特别的设备。  

---   

### 3：安装一个apk   
使用命令 __install__ 命令来安装apk，可选 __-r__参数 如果程序已经安装，那么将会重新安装并保持任何的数据不变。   

```
adb install -r APK_FILE
example
adb install -r ~/application.apk
```

---

### 4：卸载一个应用程序    

```
adb uninstall PACKAGE_NAME  
example  
adb uninstall com.tencent.mm
```

---

### 5:开始一个activty    
     
   
```
adb shell am start PACKAGE_NAME/ACTIVITY_IN_PACKAGE   
adb shell am start PACKAGE_NAME/FULL_QUALIFIED_ACTIVITY   

example   
adb shell am start -n com.tencent.mm/ui.LauncherUI   
adb shell am start -n com.tencent.mm/com.tencent.mm.ui.LauncherUI   
```
 
---

### 6:进入设备的shell命令行   
     

```
adb shell
```
 
---

### 6:屏幕截图   
这个命令用于把手机屏幕截图然后保存到当前终端的目录下面。
```
adb shell screencap -p | perl -pe 's/\x0D\x0A/\x0A/g' > screen.png
```

---

### 7：屏幕开关   
这个命令是用于发送事件给设备，然后开关屏幕。
```
adb shell input keyevent 26
```

---

### 8：解锁屏幕    

这个命令是用于解锁屏幕，可和上面的屏幕电源开关一起使用。  

```
adb shell input keyevent 82
```
---


### 9：打印手机上安装的包   
   

```
adb shell pm list packages -f
```

---

### 10:清空应用程序的数据    
    
    
```
adb shell pm clear PACKAGE_NAME

//example
adb shell pm clear com.tencent.mm
```
  
---

### 11:打印日志信息    
显示log 的输入流在我们的屏幕上
    

```
根据 tagname拦截 
adb logcat -s TAG_NAME
adb logcat -s TAG_NAME_1 TAG_NAME_2

//example
adb logcat -s TEST
adb logcat -s TEST MYAPP

》》》》》

根据等级拦截   
adb logcat "*:PRIORITY"

//example
adb logcat "*:W"

这里是一些安全等级
V - Verbose (lowest priority)
D - Debug
I - Info
W - Warning
E - Error
F - Fatal
S - Silent (highest priority, on which nothing is ever printed)

》》》》》
综合上面的一起拦截
adb logcat -s TAG_NAME:PRIORITY
adb logcat -s TAG_NAME_1:PRIORITY TAG_NAME_2:PRIORITY

//example
adb logcat -s TEST: W

》》》》》
善于使用 grep 截取重要信息 

adb logcat | grep "SEARCH_TERM"
adb logcat | grep "SEARCH_TERM_1\|SEARCH_TERM_2"

//example
adb logcat | grep "Exception"
adb logcat | grep "Exception\|Error"


》》》》》
清理lagcat的缓存区
adb logcat -c 
```



---


### END   

更多有趣的阅读欢迎上官网[链接店址][adb-url]    



[adb-url]: http://developer.android.com/tools/help/adb.html