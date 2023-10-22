**大致内容点**：

1、Docker 的基本使用（查看、删除镜像，查看、暂停、删除容器，查看日志，交互式的方式进入容器内部等）

2、Docker 命令行参数的大致介绍

3、redis-cli 的使用

本文大纲：![](../images/Pasted%20image%2020231018110131.png)

### 一、简易版本启动

#### 1.1、一条命令直接搞定

无需配置文件，所有的参数全部跟在docker 命令后，命令如下：
```shell
docker run --restart=always -p 6379:6379 --name myredis -d redis:7.0.12  --requirepass ningzaichun
```
启动效果：
![](../images/Pasted%20image%2020231018110727.png)
备注：在宿主机没有镜像的情况下，会自动在Docker hub的公开仓库中进行寻找和下载。
1、docker ps命令，查看本机docker运行的容器
```shell
docker ps
docker ps -a # 查看全部的容器，包括已经停止的
```
2、docker logs 查看日志
```shell
docker logs [容器名 | 容器ID]
docker logs -f [容器名 | 容器ID] # 表示实时的跟踪日志输出
docker logs --since 30m myredis # 此处 --since 30m 是查看此容器30分钟之内的日志情况
```
![](../images/Pasted%20image%2020231018111723.png)
#### 1.2、测试连接

**1）测试本地连接**
```shell
docker exec -it [容器名 | 容器ID] bash # 以交互的方式进入容器内部，具体的我这里没解释啦
#最近准备了一篇文章专门来讲这个

redis-cli
set k1 v1
auth ningzaichun #自己设置的密码
get k1
```
![](../images/Pasted%20image%2020231018112037.png)
**2）测试外部连接**

外部连接的话，你可以使用连接工具，也可以使用 redis-cli

笔者此处使用的本机的 redis-cli 环境，我此处是配置了环境变量，你们可能需要在安装redis的环境下才可以运行 redis-cli 命令
![](../images/Pasted%20image%2020231018113801.png)
备注：

1、如果你是云环境的话，记得要去开放安全端口，否则外部是无法连接的。

2、虚拟机环境的话，记得要去开放防火墙，或者直接关闭防火墙。

1.3、优缺点
优点及适用场景：

1、适用于各位搭建测试环境，直接一条命令到位，不需要考虑这考虑那

2、适用于新手玩redis，只是想要学习Redis命令，完全可以应付前期的学习，给予自己一定的正向反馈，让自己坚持学下去。

缺点：

1、数据比较容易丢失，因为没有配置需要落入磁盘，所以全部都是在内存中，一旦关机或者容器挂掉，数据就没有啦。

2、没法修改配置文件，不好去自定义配置

### 二、指定 redis.conf 配置文件启动
#### 2.1、获取Redis配置文件
这个配置文件需要自己上传到服务器上~

截止我编写文章 2023年8月1号，目前最新版是 7.2 ，但并非是稳定版，然后我就选择了 7.02 版本，在这里选择了 7.0 版本的配置文件（一般大版本的配置文件都是通用的），那么在获取镜像的时候，也需要找相对应的镜像版本。

7.02 Redis.conf Github，有考虑了一下可能有访问失败的朋友，笔者 fock了一份放到了 gitee redis.conf

此处的配置文件笔者只修改一下密码和允许外部连接的配置，以确保是使用了我们自己修改过的配置文件。想要了解详细配置文件的朋友，可以找相关的文章学习一下，英文能力尚可的情况下，大部分也可以理解。

配置文件具体修改的地方：
```shell
# requirepass foobared 找到这个，改成你想改的密码即可
```
![](../images/Pasted%20image%2020231018114346.png)
```shell
bind 127.0.0.1 -::1 # 这个的话改成 0.0.0.0 即可，127.0.0.1 是只允许本机访问
```
![](../images/Pasted%20image%2020231018114410.png)
我的目录结构如下，后面的启动命令中的参考路径也是这里的：
![](../images/Pasted%20image%2020231018114503.png)
#### 2.2、Docker Hub 上查看已有 Redis 相关版本镜像
在Docker hub 上可以进行搜索，找到目前 Redis 已有的镜像和相关版本，有些版本是三位数，不好确定，可以来这里看一下，此处的版本号不完全和 Github 上Redis 版本相对应，但只要是稳定版本，且在同一个大版本之内的，配置文件就大概率可以通用。

Docker Hub Redis Images

要查看的具体的版本，可以点击tags，可以看到相关的版本，并且也有拉取镜像的命令。
![](../images/Pasted%20image%2020231018114530.png)
补充：笔者尝试过使用 docker search redis:7.0.12 是无法搜到镜像，好像默认就是只能搜索到 latest版本，但是使用 docker pull redis:7.0.12 是可以成功拉取镜像的。

`docker pull redis:7.0.12`
![](../images/Pasted%20image%2020231018114549.png)
#### 2.3、指定redis.conf启动redis容器
启动命令，我去掉了之前的一些繁琐的参数，比如大家常常说指定日志文件大小

`-log-opt max-size=100m --log-opt max-file=2` 从而导致报错之类的，现在的话，大伙可以看自己的需求是否需要添加。
```shell
docker run --restart=always \
-p 6379:6379 \
--name myredis \
-v /itwxe/dockerData/redis/redis.conf:/etc/redis/redis.conf \
-v /itwxe/dockerData/redis/data:/data \
-d redis:7.0.12 redis-server /etc/redis/redis.conf
```
![](../images/Pasted%20image%2020231018124329.png)
笔者宿主机的文件目录如下：
![](../images/Pasted%20image%2020231018124353.png)
各参数的意义：

1）-restart=always 总是开机启动
2）-p 6379:6379 将6379端口挂载出去
3）–name 给这个容器取一个名字
4）-v 数据卷挂载 /home/dj/redis/redis.conf:/etc/redis/redis.conf

此处是将宿主机 /home/dj/redis/redis.conf 文件映射到 redis 容器下的 /etc/redis/redis.conf，此处你也可以理解为docker容器和宿主机共享这个文件。

5）-d redis:7.0.12 后台运行容器，不加-d就是直接在控制台输出，关闭窗口即停止容器。

6） redis-server /etc/redis/redis.conf 以配置文件启动redis，加载容器内的 redis.conf文件，最终找到的是挂载的目录 /etc/redis/redis.conf 也就是宿主机下共享的 /home/dj/redis/redis.conf。

补充：如果有权限相关的问题，可以给容器一个特权模式。加一个 --privileged

7）--log-opt max-size=100m --log-opt max-file=3

max-size：指定日志文件大小上限

max-file：指定日志文件个数

查看启动日志
```shell
docker logs myredis # 后面跟容器名 or 容器ID 都可以
docker logs --since 30m <容器名> # --since 30m 是查看此容器30分钟之内的日志情况。
```
![](../images/Pasted%20image%2020231018124423.png)
**测试连接**

使用交互方式进入到容器内部：
```shell
docker exec -it myredis bash
redis-cli
set k1 v1 #你会发现失败的
get k1
auth ningzaichun # 验证密码
```
![](../images/Pasted%20image%2020231018124452.png)
### 三、Docker 停止、删除、重启、启动容器

正常删除容器，一般是先停止容器，再进行删除
```shell
docker stop [容器名|容器ID] #停止容器
docker start   [容器名|容器ID]  #启动停止的容器
docker restart  [容器名|容器ID]  # 将容器重新启动
docker kill [容器名|容器ID] #强行终止
docker rm [容器名|容器ID]   # 删除停止的容器
```
docker stop和 docker kill容器有一些区别，stop 是会等待容器内的应用终止的，但是kill不会给容器内应用任何时间，会直接kill掉，这一点和 linux中的 kill -15 和 kill -9 是类似的。

备注：kill -9 PID 是操作系统从内核级别强制杀死一个进程. kill -15 PID 可以理解为操作系统发送一个通知告诉应用主动关闭. SIGNTERM（15） 的效果是正常退出进程，退出前可以被阻塞或回调处理。

强行删除
```shell
docker rm -f [容器名|容器ID]
```
### 四、Docker 搜索、拉取、删除、查看镜像

四行命令都很简单：
```shell
docker search redis #查找公开仓库中的redis镜像，这里貌似不能指定具体的版本号，只会提供最新的，上文已经谈过了
docker pull redis[:tag] #拉取镜像，tag 同上
docker rmi redis[:tag] #删除镜像，这里也可以跟镜像ID， 即 docker rmi 镜像ID
docker images #查看所有的镜像
```
备注：不添加[:tag]的情况下，默认是 latest 版本。
不加版本号的时候，默认就是 latest 版本
4.1、Untagged 和 Deleted
Untagged

我们首先都知道镜像的唯一标识是其 ID 和摘要，但一个镜像可以有多个标签

因此当我们使用上面命令删除镜像的时候，实际上是在要求删除某个标签的镜像。所以首先需要做的是将满足我们要求的所有镜像标签都取消，这就是我们看到的 Untagged 的信息。因为一个镜像可以对应多个标签，因此当我们删除了所指定的标签后，可能还有别的标签指向了这个镜像，如果是这种情况，那么 Delete 行为就不会发生。所以并非所有的 docker rmi 都会产生删除镜像的行为，有可能仅仅是取消了某个标签而已。

Delated

当一个镜像所有的标签都被取消了，该镜像很可能会失去了存在的意义，因此会触发删除行为。镜像是多层存储结构，因此在删除的时候也是从上层向基础层方向依次进行判断删除。镜像的多层结构让镜像复用变动非常容易，因此很有可能某个其它镜像正依赖于当前镜像的某一层。这种情况，依旧不会触发删除该层的行为。直到没有任何层依赖当前层时，才会真实的删除当前层。所以delated命令触发的判断机制要比 untagged 要难上许多。
