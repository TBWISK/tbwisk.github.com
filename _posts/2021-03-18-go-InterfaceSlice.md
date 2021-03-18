---
layout: post
title: 'go InterfaceSlice'
date: 2021-03-18
author: tbwisk
tags: golang
---

# 背景  
interface slice 的一些 介绍  [来源](https://github.com/golang/go/wiki/InterfaceSlice) 

## 介绍  
人们通常会尝试下面的代码,将任意类型的变量赋值给interface{}.  
```go
var dataSlice []int = foo()
var interfaceSlice []interface{} = dataSlice
```
这会给出一个报错
```
cannot use dataSlice (type []int) as type []interface { } in assignment
```
接下来的问题是，既然可以给 __interface{}__ 赋值任何类型，为什么不能给 __[]interface{}__ 赋值任何片?

### 为什么  
这主要有两个原因 
第一个是类型为 __[]interface{}__ 的变量不是接口!它是一个元素类型恰好是 __interface{}__ 的片。但即使这样，你也可以说意思很清楚。
第二个是 类型为 __[]interface{}__ 的变量具有特定的内存布局，在编译时已知。
每个 __interface{}__ 占用两个字节(一个字节表示所包含的类型，另一个字节表示所包含的数据或指向该数据的指针)。因此，一个长度为N且类型为 __[]interface{}__ 的切片由一个 __N*2__ 个字节长的数据块支持。
这与支持类型为 __[]MyType__ 且长度相同的片的数据块不同。它的数据块将是 __N*sizeof(MyType)__ 的字节大小。 
结果是，你不能快速地将 __[]MyType__ 类型的对象分配给 __[]interface{}__ 类型的对象; 
从代码上看，这俩者看起来是相似的，但内存的占用情况决定了这俩者是不同的，背后的内存使用方式不一致。 

### 那有什么好办法处理呢？  
这取决于你一开始想做什么。 
如果您想为任意数组类型创建一个容器，并且计划在执行任何索引操作之前更改回原始类型，那么您可以使用 __interface{}__ 。代码将是泛型的(如果不是编译时类型安全的)和快速的。 
如果你真的想要一个 __[]interface{}__ ，因为你要在转换回之前做索引，或者你正在使用一个特定的接口类型并且你想要使用它的方法，你就必须复制一个切片。分配好 __[]interface{}__ 的内存，然后针对单个 __interface{}__ 赋值。  
```go 
var dataSlice []int = foo()
var interfaceSlice []interface{} = make([]interface{}, len(dataSlice))
for i, d := range dataSlice {
	interfaceSlice[i] = d
}
```

### 总结  
这块主要是理解 __interface{}__ 和 __[]interface{}__ 在使用上面的区别。以及导致使用这种方式的原因是什么。   