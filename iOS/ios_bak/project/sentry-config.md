# Sentry

引用：http://blog.csdn.net/largetalk/article/details/8640854

集成：[sentry-coco 捕获异常集攻略](http://blog.csdn.net/yangzm/article/details/76889333)

## 1. Sentry介绍及使用

Sentry is a realtime event logging and aggregation platform. At its core it specializes in monitoring errors and extracting all the information needed to do a proper post-mortem without any of the hassle of the standard user feedback loop.

Sentry 是一个实时事件日志记录和汇集的平台。其专注于错误监控以及提取一切事后处理所需信息而不依赖于麻烦的用户反馈。

### 1.1 Sentry介绍
无论测试如何完善的程序，bug总是免不了会存在的，有些bug不是每次都会出现，测试时运行好好的代码可能在某个用户使用时就歇菜了，可是当程序在用户面前崩溃时，你是看不到错误的，当然你会说:”Hey, 我有记日志呢”。 但是说实话，程序每天每时都在产生大量的日志，而且分布在各个服务器上，并且如果你有多个服务在维护的话，日志的数量之多你是看不过来的吧。等到某天某个用户实在受不了了，打电话来咆哮的时候，你再去找日志你又会发现日志其实没什么用：缺少上下文，不知道用户什么操作导致的异常，异常太多（从不看日志的缘故）不知如何下手 等等。

Sentry就是来帮我们解决这个问题的，它是一款精致的Django应用，目的在于帮助开发人员从散落在多个不同服务器上毫无头绪的日志文件里发掘活跃的异常，继而找到潜在的臭虫。

Sentry是一个日志平台, 它分为客户端和服务端，客户端(目前客户端有Python, PHP,C#, Ruby等多种语言)就嵌入在你的应用程序中间，程序出现异常就向服务端发送消息，服务端将消息记录到数据库中并提供一个web节目方便查看。Sentry由python编写，源码开放，性能卓越，易于扩展，目前著名的用户有Disqus, Path, mozilla, Pinterest等。

### 1.2 Sentry安装
Sentry构建依赖于Python, Django, MySQL/Postgresql, memcache, RabbitMQ/Redis等。

略

### 1.3 Sentry权限介绍
Sentry目前用户类型有四种： 超级管理员， 管理员，普通用户和System agents. 超级用户只能通过命令行来创建，其他用户可以自己注册或由其他用户邀请注册加入，然后由超级管理员或管理员分配项目和权限。为了更好支持团队协助以及信息安全，新版本Sentry(5.4.2)经过了重新设计，重新设计后的Sentry以Team为中心组织权限。所谓Team就是一个团队，一些用户组织在一起对某些项目有操作权限的组织。一个项目只能属于一个Team, 一个用户却可以属于多个Team, 并可在不同Team中扮演不同角色， 如用户A在Team X是管理员而在Team Y中是System agents. Sentry对用户角色的指定只能到Team级别，不能到Project级别， 所以将某个用户加入到某个Team之后，这个用户就对所有所有属于这个Team下所有project有了相同的权限。

Sentry的如此设计虽说不够细致，但我们使用时只要遵照现实世界的情况来划分Team和project即可。比如我们目前有一个团队，这个团队负责两个项目，我们在Sentry上就建立一个Team A和属于这个Team的project a和project b. 当在使用过程中我们发现project b的等级较高，不能让所有人都可以看到信息，我们可以建立一个虚拟Team B,然后将project b的team 改Team B, 然后再给Team B添加用户和相应权限即可. Sentry对team的owner, project的owner, project属于的team 等等实体关系的修改都极为简单，没有副作用。

下面简单介绍下Sentry中各个类型用户的权限。

超级管理员： 能创建各种用户， team和project只能由超级管理员创建。项目的一些设置比如改变Owner, 数据公开可见与否（设为public的数据可以通过url不登陆也能查看）以及客户端domain限制的设定。另外还有管理项目的api key(客户端只有得到此api key才能向Sentry发送消息)的权限等等。
管理员： 能创建用户， team和项目设定中除改变owner之外的权限， 可以对项目中具体数据做resolve, bookmark, public/public和remove操作。
普通用户： 无Team界面，只能对项目中具体数据做resolve, bookmark, public/unpublic和remove操作。
System agents: 无Team界面，只能对项目中具体数据做bookmark, unpublic和remove操作。
此外，还需知晓Sntry中team和project的Onwer概念，只有是team或project的owner(除超级管理员外)才能更改其owner, 一旦成为team或project的Owner,则获得对此team或project如同超级管理员般的权限。

Sentry的权限和实体设置基本可以满足现实的需求：

如果一个团队负责多个项目，可以通过一个Team多个项目方式来实现。
如果一个人参与多个团队， 可以将该用户添加到多个team中去。
一个team或project设置相应的Owner,则可以由此人负责该team或project内的活动。（唯一的缺点是team owner不可以创建project)
一个team或项目的分工应该是有一个管理者来设定项目的基本设定，管理api key, 并将api key分发给项目开发者，项目开发者以普通用户身份登陆Sentry查看错误和resolve错误， 运维以System agents身份登陆Sentry查看系统运行状态。

### 1.4 Sentry性能
有些人担心Sentry会影响程序性能，所以我搭建了一个Sentry服务器做了下测试。

我一共使用了两台机器(机器是HP ProLLiant DL360 G4p)作为服务器， 一台安装了Sentry和mysql, 一台安装了memcache和redis, 然后在sentry.conf.py里将QUEUE和CACHE都打开。使用supervisor启了三个服务

sentry start http
sentry start udp
sentry celeryd -B
首先测试下直接插入消息的效率如何:

from raven import Client
from timeit import timeit
try:
   import cProfile as profile
except ImportError:
   import profile
from pstats import Stats

client = Client('udp://11c5f6e203cb4b228b2dcac3df29dad2:55adfa2e6d8c4fd799694d8dcde372c3@172.16.97.11:9001/2')

def do_capture_msg2():
    for i in range(1000):
        client.captureMessage(str(i) + 'hello world! from do_capture_msg2')

def test(f, number=1000):
    time = timeit(f, number=number)
    st = Stats(profile.Profile().runctx('f()', globals(), locals()))
    print("funcation_name     msec    rps  tcalls  funcs")
    print("%-11s %6.0f %6.0f %7d %6d" % (f.__name__, 1000 * time, number / time, st.total_calls, len(st.stats)))

test(do_capture_msg2, number=10)
测试结果:

funcation_name     msec    rps  tcalls  funcs
do_capture_msg2   4093      2  287004    101
我一共提交了10000个消息，共耗时4093ms, 每次消息耗时很少，这里有两个需要注意的地方，一个是我使用的是udp接口，如果使用http接口，这个耗时会增加不少。对于udp的传输非可靠性，我认为在内网环境内应该是基本可靠的，在测试过程中，未发现消息遗漏的情况。另一个需要注意的地方是由于我的服务器性能不高，这10000个消息并不是在这4s中内就被处理完了，而是被记录到任务队列了，所以在服务器可以看到，客户端的请求完成之后，服务器的cpu使用率还需要一段时间才能降下来。

普通消息的提交处理非常快，那对于异常消息又如何:

def do_capture_exc():
    try:
        1 / 0
    except ZeroDivisionError:
        client.captureException()

test(do_capture_exc, number=1000)
测试结果:

funcation_name     msec    rps  tcalls  funcs
do_capture_exc   1061    942    1029    121
略慢，但我估计异常的抛出和捕获才是元凶

下面结合应用程序来测试性能，创建一个django程序，添加一个非常简单的view响应函数:

def sentry(request):
    t = random.randint(1, 10)
    time.sleep(t/1000.0)
    u = User.objects.all()[0]
    return HttpResponse('user %s say hello world from django:  %sms '%(u.username, t))
启动脚本是:

#!/bin/bash
gunicorn_django -k gevent -w 8 --worker-connections 3000 -b 0.0.0.0:8001 -D
ab测试结果是:

ab -n 1000 -c 100 http://172.16.97.12:8001/
Server Software:        gunicorn/0.17.2
Server Hostname:        172.16.97.12
Server Port:            8001

Document Path:          /sentry
Document Length:        45 bytes

Concurrency Level:      100
Time taken for tests:   2.298 seconds
Complete requests:      1000
Failed requests:        90
   (Connect: 0, Receive: 0, Length: 90, Exceptions: 0)
Write errors:           0
Total transferred:      185090 bytes
HTML transferred:       45090 bytes
Requests per second:    435.11 [#/sec] (mean)
Time per request:       229.827 [ms] (mean)
Time per request:       2.298 [ms] (mean, across all concurrent requests)
Transfer rate:          78.65 [Kbytes/sec] received

Connection Times (ms)
                 min  mean[+/-sd] median   max
                 Connect:        0    0   0.7      0       9
                 Processing:    16  219 149.6    180     823
                 Waiting:       16  219 149.6    180     823
                 Total:         16  219 149.9    180     825
将view函数改为如下:

def sentry(request):
    t = random.randint(1, 10)
    time.sleep(t/1000.0)
    u = User.objects.all()[0]
    client.captureMessage('user %s say hello world from django:  %sms '%(u.username, t))
    return HttpResponse('user %s say hello world from django:  %sms '%(u.username, t))
ab测试结果如下:

ab -n 1000 -c 100 http://172.16.97.12:8001/sentry

Server Software:        gunicorn/0.17.2
Server Hostname:        172.16.97.12
Server Port:            8001

Document Path:          /sentry
Document Length:        45 bytes

Concurrency Level:      100
Time taken for tests:   3.175 seconds
Complete requests:      1000
Failed requests:        90
   (Connect: 0, Receive: 0, Length: 90, Exceptions: 0)
Write errors:           0
Total transferred:      185090 bytes
HTML transferred:       45090 bytes
Requests per second:    314.95 [#/sec] (mean)
Time per request:       317.510 [ms] (mean)
Time per request:       3.175 [ms] (mean, across all concurrent requests)
Transfer rate:          56.93 [Kbytes/sec] received

Connection Times (ms)
              min  mean[+/-sd] median   max
              Connect:        0    0   0.6      0       8
              Processing:    17  301 225.2    241    1308
              Waiting:       17  301 225.2    241    1307
              Total:         17  302 225.5    241    1310
1000次请求运行时间从2.298s变为3.175s, rps从435.11降为314.95

### 1.5 总结
虽然从上面测试看起来，程序效率下降了大概1/4左右，但测试毕竟和实际应用有一些差距，实际运行的应用有更复杂的逻辑，更多的运算和更多的io操作，所以实际上sentry的影响会很小，而带来的好处则很多，并且，Sentry是一个平台级的服务，一旦构建好，所有应用都可以使用受益。

当然，Sentry和log不是相互重叠的东西，他们各有用处，不能把Sentry当做log来使用，大量的网络操作对应用程序来说还是会有影响的。而且有了Sentry后log还是需要的，Sentry在服务器网络异常时会失效，并且业务逻辑的跟踪等等也离不开log。

