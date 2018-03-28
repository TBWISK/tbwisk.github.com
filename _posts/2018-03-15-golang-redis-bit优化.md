---
layout: post
title: 'golang-redis-bit优化'
date: 2018-03-15
author: tbwisk
tags: golang redis
---

涉及到redis bit 的操作以及优化

## 使用背景  

在redis 中使用 bit 来统计用户的标签信息是否存在。 
然后有些业务背景 会存在 使用redis pipeline 的方式循环调用该用户的全部标签。  
但这样会相对而言，会增加redis的cpu的使用率。 
那么有什么办法处理呢？ 其实 redis bit 的相当于 利用字节 在字符串中储存信息。
本质是 一串的bit 就是 个value 。使用 redis 的get 就可以把全部相信拿到。
假设一个用户有200个标签，pipeline 的方式就用了 200个 getbit ，然而get 就仅仅操作了一次。  
至于获取得到 的字符串，如何拼接成 可用到 bit[] ，则需要程序去处理。  

```
package main

import (
	"fmt"

	"github.com/garyburd/redigo/redis"
)

func main() {
	dial := redis.DialDatabase(3)
	con, err := redis.Dial("tcp", "127.0.0.1:6379", dial)
	if err != nil {
		fmt.Println(fmt.Errorf("error ,%v", err))
		return
	}
	for i := 0; i < 7; i++ {
		con.Send("getbit", "apple", i)
	}
	err = con.Flush()
	var results []int

	for i := 0; i < 7; i++ {
		result, _ := redis.Int(con.Receive())
		results = append(results, result)
	}
	fmt.Println(results)
	resp, err := redis.String(con.Do("get", "apple"))
	fmt.Println(resp, err)
}
//输出
//[0 1 0 0 0 1 0]
//D <nil>

```

## 分析  

直接使用get 看到的字符串居然是 D ，那么如何解决呢？才能转换为正确的模式呢？  
本质上，数据都是二进制组成的，能不能把 D 转换为 []byte形式，之后通过解析每个 byte所代表的二进制。  
这里需要理解的是，8 bit = 1byte 建议去理解下，为何会这样

```
resp, err := redis.Bytes(con.Do("get", "apple"))
fmt.Println(resp, err)
//于是换算成 byte
//[68] <nil>

```

68 为什么会对应的是 D 呢？参照 ascii码表 
一个byte最大的数值就是254 2^8 次方
之后把68转换为 二进制就可以了
```
    resps, _ := redis.Bytes(con.Do("get", "apple"))
	for _, resp := range resps {
		fmt.Println(strconv.FormatUint(uint64(resp), 2))
	}
    // 1000100 
    //输出的二进制 ，现在结束了么？ 还没有，因为 是8位的，上面输入的仅仅是7位，需要把 string 的 前面补全 补全后的 是 01000100的字符串
```

所以又可以改成
```

for _, orign := range origns {
		item := strconv.FormatUint(uint64(orign), 2)
		itemLen := len(item)
		for i := 0; i < 8-itemLen; i++ {
			buffer.WriteString("0")//补全前缀的0
		}
		buffer.WriteString(item)
}

```


## 结论：
最后经过一系列的优化代码
可以把代码写成
```
var poolBuffer *sync.Pool      
func init(){
    poolBuffer = &sync.Pool{
	New: func() interface{} {
		return &bytes.Buffer{}
	    },
	}
}
func GetBitmap(con redis.Conn, key string) []int {
	// 用途在于替换 gitbit 的pipline 的使用
	// 所以最后的返回结果是一致的
	var obj []int
	origns, err := redis.Bytes(con.Do("get", key))
	if err != nil {
		return obj
	}
	size := len(origns) * 8
	// buffer := bytes.Buffer{}
	buffer := poolBuffer.Get().(*bytes.Buffer)

	obj = make([]int, size, size)
	for _, orign := range origns {
		item := strconv.FormatUint(uint64(orign), 2)
		itemLen := len(item)
		for i := 0; i < 8-itemLen; i++ {
			buffer.WriteString("0")
		}
		buffer.WriteString(item)
	}
	// fmt.Println(buffer.String())
	newBit := buffer.String()
	buffer.Reset()
	poolBuffer.Put(buffer)
	for idx, l := range newBit {
		if l == 49 {
			obj[idx] = 1
		}
	}
	return obj
}

```

那么就相对 把 getbit 的循环省掉，节省了 redis 循环gitbit的时间 
下一节，试试把 setbit 也优化一下。
