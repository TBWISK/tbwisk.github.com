---
layout: post
category : lessons
tagline: "python"
tags : [python,celery,翻译]
date: 2016-04-22 15:01:45
title: celery 最佳实践  
---

### 1：背景  
来源 [https://denibertovic.com/talks/celery-best-practices/#/](https://denibertovic.com/talks/celery-best-practices/#/)  

什么是celery ？  
分布式任务队列  

什么是任务队列？  
就好像我们所知道的工作队列，意味着是 异步/后台任务进程。   

分布式？ 
workers（任务工作的进程们）可以在不同的机器上。   

Broker?AMQP?
中央组件，所有可以组成任务们队列的工具   
1：RabbitMQ   
2：Redis   
3：...  

因此有点类似下面这样   
<img src="http://tbwisk.github.com/assets/picture/celery1.png">   
   
那么如何django 如何适配呢？   
1:避免长时间的请求去运行   
2：好的抽象异步消息传递   

如何去安装？   

```python   

                    # install
                    pip install celery

                    # celeryconfig.py
                    RABBITMQ_PORT = '5672'
                    RABBITMQ_USER = 'user'
                    RABBITMQ_PASS = 'password'

                    BROKER_URL = 'amqp://{0}:{1}@localhost:{2}//'.format(
                        RABBITMQ_USER,
                        RABBITMQ_PASS,
                        RABBITMQ_PORT)
                    CELERY_IMPORTS = ('mymodule.tasks',)

                    # and run it
                    celery worker -E -l INFO -n myWorker_1
```


如何去使用？   

```python
                    # in tasks.py
                    app = Celery('myapp')
                    app.config_from_object('celeryconfig')
                    ## app.config_from_object('django.conf:settings')
                    ## app.autodiscover_tasks(lambda: settings.INSTALLED_APPS)

                    @app.task(bind=True)
                    def regular_py_function(self, x, y):
                        print("Doing stuff...")
```


如何去使用?   

```python
   # calling it
        from tasks import *
        regular_py_function.delay(x=1, y=2)

        # or
        regular_py_function.apply_async((1, 2), retry=True)

        # or even better
        from celery.execute import send_task
        send_task("tasks.regular_py_function", args=[1, 2], kwargs={})
```


下面开始说关于最好的实践：   

1：不用使用数据库作为broker,这个是个坏的主义。甚至在开发环境中。   
2：使用多个队列，不要只使用默认队列   

```python
#下面的代码存在问题
 CELERY_QUEUES = (
        Queue('default', Exchange('default'), routing_key='default'),
        Queue('for_task_A', Exchange('for_task_A'), routing_key='for_task_A'),
        Queue('for_task_B', Exchange('for_task_B'), routing_key='for_task_B'),
    )


    CELERY_ROUTES = {
        'my_taskA': {'queue': 'for_task_A', 'routing_key': 'for_task_B'},
        'my_taskB': {'queue': 'for_task_B', 'routing_key': 'for_task_B'},
    }

```

3:使用一些worker处理优先级比较高的任务。因为有些任务是特别重要的   

```shell
celery worker -E -l INFO -n workerA -Q for_task_A
celery worker -E -l INFO -n workerB -Q for_task_B
```

4:使用错误处理器   

```python
            app.task(bind=True, default_retry_delay=10, max_retries=5)
            def awesome_task(self, x):
                try:
                    print("Doing some network IO")
                    r = requests.get('https://httpbin.org/get')
                    parse_request(r)
                except ConnectionError as e:
                    self.retry(e)
                except MyException as e:
                    do_some_cleanup()
                    self.retry(e) # or don't
```
   
5:如果你们需要返回的结果，那么就跟踪结果。  

```
 CELERY_IGNORE_RESULT = True
```

6:使用Flower-https://github.com/mher/flower 

```
            pip install flower
            celery flower --port=5555 # http://localhost:5555/
```
a:任务进程和历史  
b:显示任务的详细信息(参数,启动时间、运行等)  
c:图表和统计数据  
d:远程控制  

谢谢观看。