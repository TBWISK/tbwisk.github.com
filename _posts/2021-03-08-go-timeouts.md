---
layout: post
title: 'go timeouts'
date: 2021-03-02
author: tbwisk
tags: golang
---

# 背景  
在使用goroutine的时候，有时候需要控制goroutine执行的时间。这时候需要使用到 一些技巧。 
这个时候可以使用 __select__ 和 __time.After__ 进行处理。  
[来源](https://github.com/golang/go/wiki/Timeouts)  
```go 
import "time"

c := make(chan error, 1)
go func() { c <- client.Call("Service.Method", args, &reply) } ()
select {
  case err := <-c:
    // use err and reply
  case <-time.After(timeoutNanoseconds):
    // call timed out
}
```

__注意__: 通道c的缓冲区大小是1。如果是非缓冲通道和客户端。调用方法所花费的时间超过了 __timeoutNanoseconds__，通道发送将永远阻塞，goroutine将永远不会被销毁。   

上述是涉及到的超时控制在后续的版本中可以使用 __context__ 进行超时控制。以及使用 __sync.WaitGroup__ 控制 goroutine的完成情况。  
