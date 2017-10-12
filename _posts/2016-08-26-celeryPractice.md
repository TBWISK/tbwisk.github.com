---
layout: post
title: celery 优化和实践以及遇到的问题  
date: 2016-08-26 15:17:13
tags: [celery,python]
---


作为一个Celery使用重度用户，看到[Celery Best Practices](https://denibertovic.com/posts/celery-best-practices/)这篇文章,加入我们项目中celery的实战经验。   
至于Celery为何物，看这里[Celery](http://www.celeryproject.org/)。   
通常在使用Django的时候，你可能需要执行一些长时间的后台任务，没准你可能需要使用一些能排序的任务队列，那么Celery将会是一个非常好的选择。   
当把Celery作为一个任务队列用于很多项目中后，作者积累了一些最佳实践方式，譬如如何用合适的方式使用Celery，以及一些Celery提供的但是还未充分使用的特性。

### 1:不要使用数据库作为你的AMQP Broker   

数据库并不是天生设计成能用于AMQP broker的，在生产环境下，它很有可能在某时候当机（PS，当掉这点我觉得任何系统都不能保证不当吧！！！）。
  
作者猜想为啥很多人使用数据库作为broker主要是因为他们已经有一个数据库用来给web app提供数据存储了，于是干脆直接拿来使用，设置成Celery的broker是很容易的，并且不需要再安装其他组件（譬如RabbitMQ）。
  
假设有如下场景：你有4个后端workers去获取并处理放入到数据库里面的任务，这意味着你有4个进程为了获取最新任务，需要频繁地去轮询数据库，没准每个worker同时还有多个自己的并发线程在干这事情。
  
某一天，你发现因为太多的任务产生，4个worker不够用了，处理任务的速度已经大大落后于生产任务的速度，于是你不停去增加worker的数量。突然，你的数据库因为大量进程轮询任务而变得响应缓慢，磁盘IO一直处于高峰值状态，你的web应用也开始受到影响。这一切，都因为workers在不停地对数据库进行DDOS。
  
而当你使用一个合适的AMQP（譬如RabbitMQ）的时候，这一切都不会发生，以RabbitMQ为例，首先，它将任务队列放到内存里面，你不需要去访问硬盘。其次，consumers（也就是上面的worker）并不需要频繁地去轮询因为RabbitMQ能将新的任务推送给consumers。当然，如果RabbitMQ真出现问题了，至少也不会影响到你的web应用。
  
这也就是作者说的不用数据库作为broker的原因，而且很多地方都提供了编译好的RabbitMQ镜像，你都能直接使用，譬如这些。
      

其实这个时候需要明确的是，数据库的读写机制。它有内存作为缓存，但数据真实的是写进硬盘里。在大量数据读写的时候，会触发一些读写硬盘的io机制，以便更好的把数据储存下到硬盘里，保护数据。所以大量的worker读写数据库的时候，会导致数据库缓慢。   

### 2：使用更多的queue（不要只用默认的）   

Celery非常容易设置，通常它会使用默认的queue用来存放任务（除非你显示指定其他queue）。通常写法如下：   

```python
    @app.task()
    def my_taskA(a, b, c):
        print("doing something here...")

    @app.task()
    def my_taskB(x, y):
        print("doing something here...")
```

这两个任务都会在同一个queue里面执行，这样写其实很有吸引力的，因为你只需要使用一个decorator就能实现一个异步任务。作者关心的是taskA和taskB没准是完全两个不同的东西，或者一个可能比另一个更加重要，那么为什么要把它们放到一个篮子里面呢？（鸡蛋都不能放到一个篮子里面，是吧！）没准taskB其实不怎么重要，但是量太多，以至于重要的taskA反而不能快速地被worker进行处理。增加workers也解决不了这个问题，因为taskA和taskB仍然在一个queue里面执行。  

### 3:使用具有优先级的workers

为了解决2里面出现的问题，我们需要让taskA在一个队列Q1，而taskB在另一个队列Q2执行。同时指定x workers去处理队列Q1的任务，然后使用其它的workers去处理队列Q2的任务。使用这种方式，taskB能够获得足够的workers去处理，同时一些优先级workers也能很好地处理taskA而不需要进行长时间的等待。   

首先手动定义queue   

```python 
CELERY_QUEUES = (
    Queue('default', Exchange('default'), routing_key='default'),
    Queue('for_task_A', Exchange('for_task_A'), routing_key='for_task_A'),
    Queue('for_task_B', Exchange('for_task_B'), routing_key='for_task_B'),
)
```

然后定义routes用来决定不同的任务去哪一个queue

```python
CELERY_ROUTES = {
    'my_taskA': {'queue': 'for_task_A', 'routing_key': 'for_task_A'},
    'my_taskB': {'queue': 'for_task_B', 'routing_key': 'for_task_B'},
}

```
最后再为每个task启动不同的  

```python
celery worker -E -l INFO -n workerA -Q for_task_A celery worker -E -l INFO -n workerB -Q for_task_B
```

如果在我们项目中，会涉及到大量文件转换问题，有大量小于1mb的文件转换，同时也有少量将近20mb的文件转换，小文件转换的优先级是最高的，同时不用占用很多时间，但大文件的转换很耗时。如果将转换任务放到一个队列里面，那么很有可能因为出现转换大文件，导致耗时太严重造成小文件转换延时的问题。   

### 4:使用Celery的错误处理机制

大多数任务并没有使用错误处理，如果任务失败，那就失败了。在一些情况下这很不错，但是作者见到的多数失败任务都是去调用第三方API然后出现了网络错误，或者资源不可用这些错误，而对于这些错误，最简单的方式就是重试一下，也许就是第三方API临时服务或者网络出现问题，没准马上就好了，那么为什么不试着重试一下呢？   


```python
@app.task(bind=True, default_retry_delay=300, max_retries=5)
def my_task_A():
    try:
        print("doing stuff here...")
    except SomeNetworkException as e:
        print("maybe do some clenup here....")
        self.retry(e)
```

### 5:使用Flower
Flower是一个非常强大的工具，用来监控celery的tasks和works。

### 6:没事别太关注任务退出状态  

一个任务状态就是该任务结束的时候成功还是失败信息，没准在一些统计场合，这很有用。但我们需要知道，任务退出的状态并不是该任务执行的结果，该任务执行的一些结果因为会对程序有影响，通常会被写入数据库（例如更新一个用户的朋友列表）。   

```python
CELERY_IGNORE_RESULT = True
```

对于我们来说，因为是异步任务，知道任务执行完成之后的状态真没啥用，所以果断丢弃。但我们可以自己弄个表单监听我们的工作状态，因为在一些敏感的数据上面，我们需要知道我们为啥会失败，最后有没有成功，重试是否失败。

### 7:不要给任务传递 Database/ORM 对象   
这个其实就是不要传递Database对象（例如一个用户的实例）给任务，因为没准序列化之后的数据已经是过期的数据了。所以最好还是直接传递一个user id，然后在任务执行的时候实时的从数据库获取。   

这里也可以从内存的角度出发。可以优化内存。因为我们使用python的时候，python的机制是计数引用，使用之后某个时间段会有检测是否有对象在使用，如果有，则不回收，如果没有，则作标记。等下集中回收。
  
如果我们使用了orm传送对象，那么有可能。我们使用了one to many 的任务分发机制，那么在many中引用了one的对象。则会导致one不会被回收，然后many其中一个引用了的对象，是one的list中的某个数据，这个list的对象又分布在各个的many中。容易造成雪崩效应，要回收一起回收，不然都不回收。造成了回收内存的效率低效，如果many的数量足够多，那么内存会造成溢出。  

[Celery Talk https://denibertovic.com/talks/celery-best-practices](https://denibertovic.com/talks/celery-best-practices)


## 在使用celery中踩过的坑以及回避策略:  

### 1: redis 服务器死了  

这次是青云的问题，因为青云在有段时间服务器故障，然后死了。

### 2: worker 莫名其妙死了

worker 在运行某段时间后，会莫名其妙的不继续运行新的tasks，这个时候beat还是有心跳的。  

解决思路，重启worker.  
如何重启：
      0：监听有没有新数据，然后通知方式 email and 模板消息。
          问题：半夜无人管理。   
      1：在接口层面重启，利用api接口检测是否有新数据，如果长达一个小时没有，那么就重启worker。  
          问题：权限问题，如果成功了，那么就相当于hack了服务器，安全性问题。       
      2：利用api接口，如果长达一个小时没有新数据，那么新开一个celery worker。
          问题：不确定性，容易造成内存过大，和有时候新开失败。     
      3：利用linux层面的定时任务重启。    
          linux上面有个crontab 的定时任务工作，编写crontab 脚本。然后定时检查服务器是否有新数据，如果没有，直接重启

相关性：uwsgi 用户权限 supervisorctl配置 crontab脚本编写   

正确和错误是相对的。只有适合的场景。  

错误解决方法：
```python
    cmd = '/opt/.virtualenvs/wxmonitor/bin/celery multi restart worker -A weixin -P gevent -c 100 -l INFO --pidfile="/srv/wxmonitor.imyyh.com/application/celerybeat2.pid"  --without-mingle'
    os.system(cmd)
```

下面的是解决方法：

```python
# coding:utf-8
import MySQLdb
from dateutil import tz
import time
from datetime import datetime
import os
# 用于一个小时检查一次有没有运行celery
# 如果没有运行celery 在重启
# 代码运行成面 依赖 是 cron crontab 的linxu工具
# 因此代码中不可依赖 django的模块
def chagetime(now):
    from_zone = tz.gettz('UTC')
    to_zone = tz.gettz('CST')
    a = now.replace(tzinfo=from_zone)
    return a.astimezone(to_zone)


def connect():
    conn = MySQLdb.connect(host='', user='',
                           passwd='', db='', charset='utf8')
    sql = "select * from account_weixinreadstart order by id desc limit 0,1"
    cursor = conn.cursor()
    n = cursor.execute(sql)
    app = None
    items = cursor.fetchall()
    for item in items:
        for i in item:
            if isinstance(i, datetime):
                app = i
    old_time = time.mktime(chagetime(app).timetuple())
    new_time = time.time()
    print datetime.today()
    if new_time - old_time > 40 * 60:
        path = "/srv/wxmonitor.imyyh.com/application/celeryworker.pid"
        f = open(path, 'r')
        items = f.readlines()
        item = items[0].strip()
        cmd = "kill -hup %s" % item
        print "cmd", cmd
        os.system(cmd)
    else:
        print "not need"
if __name__ == "__main__":
    print "start"
    connect()
    print "end"
```

### 3：worker 数量过多，连接redis失败   

错误日志  

```log
[2016-07-28 15:02:09,057: CRITICAL/MainProcess] Couldn't ack u'ac489a2a-8e68-48f0-899d-46a1b901d4be', reason:ConnectionError('Too many connections',)
Traceback (most recent call last):
  File "/opt/.virtualenvs/wxmonitor/local/lib/python2.7/site-packages/kombu/message.py", line 93, in ack_log_error
    self.ack()
  File "/opt/.virtualenvs/wxmonitor/local/lib/python2.7/site-packages/kombu/message.py", line 88, in ack
    self.channel.basic_ack(self.delivery_tag)
  File "/opt/.virtualenvs/wxmonitor/local/lib/python2.7/site-packages/kombu/transport/virtual/__init__.py", line 566, in basic_ack
    self.qos.ack(delivery_tag)
  File "/opt/.virtualenvs/wxmonitor/local/lib/python2.7/site-packages/kombu/transport/redis.py", line 159, in ack
    self._remove_from_indices(delivery_tag).execute()
  File "/opt/.virtualenvs/wxmonitor/local/lib/python2.7/site-packages/redis/client.py", line 2620, in execute
    self.shard_hint)
  File "/opt/.virtualenvs/wxmonitor/local/lib/python2.7/site-packages/redis/connection.py", line 897, in get_connection
    connection = self.make_connection()
  File "/opt/.virtualenvs/wxmonitor/local/lib/python2.7/site-packages/redis/connection.py", line 904, in make_connection
    raise ConnectionError("Too many connections")
ConnectionError: Too many connections
```

思路：一开始以为是redis的链接端口设置限制，于是果断上redis服务器查看端口开的数量，然后发现没多大问题。那么思考是什么问题，叫大牙看青云服务器配置，服务器配置是最大值，最后的解决方法是，更改默认的配置。 

```python
BROKER_POOL_LIMIT = 50
CELERY_REDIS_MAX_CONNECTIONS = 60
BROKER_TRANSPORT_OPTIONS = {
    "max_connections": 400,
}
```

### 4：数据库链接数量过多  
   sqlite -> mysql

### 5：学会搜索，不能偷懒   
   优先级 google -> bing.com -> baidu.com


### 总结   
end




