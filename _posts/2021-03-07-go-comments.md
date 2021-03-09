---
layout: post
title: 'go comments'
date: 2021-03-07
author: tbwisk
tags: golang
---

# 背景  
go comments 注释，能让代码可阅读性更强。  

### Comments  
每个包都应该有一个包注释。  
它应该在包中的一个文件中的包语句的前面。(它只需要出现在一个文件中。)  
它应该以 __Package packagename__ 开头的单句开头，并对包的功能进行简明的总结。  
这个介绍性的句子将在 __godoc__ 的所有软件包列表中使用。  
后续的内容会给出更多的细节  
```go
// Package superman implements methods for saving the world.
//
// Experience has shown that a small number of procedures can prove
// helpful when attempting to save the world.
package superman
```
几乎每个顶级类型，const、var和func都应该有注释。  
这部分的注释应该是这样的:注释在代码的上面。对应对象的描述第一个字母不应该大写，除非在代码中它是大写的。  
```go 
// enterOrbit causes Superman to fly into low Earth orbit, a position
// that presents several possibilities for planet salvation.
func enterOrbit() os.Error {
  ...
}
```
在注释中缩进的所有文本，godoc将呈现为预先格式化的块。这会让代码看起来更简洁。  
```go 
// fight can be used on any enemy and returns whether Superman won.
//
// Examples:
//
//  fight("a random potato")
//  fight(LexLuthor{})
//
func fight(enemy interface{}) bool {
  ...
}
```

### 总结 
上述比较简单的说一下注释的作用，以及如何做注释。  
在类似Java的项目中，函数的传值和返回值也是需要通过注释来描述对应的数据。
方便其他人看你的代码，以及方便后续的维护性。避免每次调用的时候，都得重新看代码，才能知道对应函数的作用。  