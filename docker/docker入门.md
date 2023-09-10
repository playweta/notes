
### 安装Docker
---
#### Docker的基本组成
---
##### Docker的架构图
![](../images/Pasted%20image%2020230908192139.png)
##### 镜像（image）：
>Docker镜像（Image）就是一个只读的模板，镜像可以用来创建Docker容器，一个镜像可以创建很多日期，就好似Java中的类和对象，类就是镜像，容量就是对象！

##### 容器（container）：
>Docker 利用容器（Container）独立运行一个或一组应用。容器是用镜像创建的运行实例。
>它可以被启动、开始、停止、删除。每个容器都是相互隔离的，保证安全的平台。
>可以把容器看作是一个简易版的Linux环境（包括root用户权限、进程空间、用户空间和网络空间等）和运行在其中的应用程序。
>容器的定义和镜像几乎一模一样也是一堆层的统一视角，唯一区别在于容器的最上面那一层是可读可写的。

##### 仓库（repository）：
>仓库（Repository）是集中存放镜像文件的场所。
>仓库（Repository）和仓库注册服务器（Registry）是有区别的。仓库注册服务器上往往存放着多个仓库，每个仓库中又包含了多个镜像，每个镜像有不同的标签（tag）。
>仓库分为公开仓库（public）和私有仓库（Private）两种形式。
>最大的公开仓库是Docker Hub（https://hub.docker.com/），存放了数量庞大的镜像供用户下载。
>国内的公开仓库包括阿里云、网易云 等

##### 小结：
需要正确的理解仓库/镜像/容器这几个概念：
* Docker 本身是一个容器运行载体或称之为管理引擎。我们把应用程序和配置以来打包好形成一个 可交付的运行环境，这个打包好的运行环境就似乎image镜像文件。只有通过这个镜像文件才能生成Docker容器。image文件可以看作是容器的模板。Docker根据image文件生成容器的实例。同一个image文件，可以生成多个同时运行的容器实例。
* image文件生成的容器实例，本身也是一个文件，称为镜像文件。
* 一个容器运行一种服务，当我们需要的时候，就可以通过docker客户端创建一个对应的运行实例，也就是我们容器
* 至于仓库，就是放了一堆镜像的地方，我们可以把镜像发布到仓库中，需要的时候从仓库中拉下来就可以了。
##### 环境说明
我们使用的是Centos 7（64bit）
目前，Centos仅发布版本中的内核支持Docker。
Docker运行在Centos7上，要求系统为641位，系统内核版本为3.10以上。
###### 查看自己的内核：
`uname -r`命令用于打印当前系统相关信息（内核版本号、硬件架构、主机名称和操作系统类型等）。

```shell
$ uname -r
4.15.0-169-generic
```

######查看版本信息：
`cat /etc/os-release`
```shell
root@hecs-30245:~# cat /etc/os-release
NAME="Ubuntu"
VERSION="18.04.6 LTS (Bionic Beaver)"
ID=ubuntu
ID_LIKE=debian
PRETTY_NAME="Ubuntu 18.04.6 LTS"
VERSION_ID="18.04"
HOME_URL="https://www.ubuntu.com/"
SUPPORT_URL="https://help.ubuntu.com/"
BUG_REPORT_URL="https://bugs.launchpad.net/ubuntu/"
PRIVACY_POLICY_URL="https://www.ubuntu.com/legal/terms-and-policies/privacy-policy"
VERSION_CODENAME=bionic
UBUNTU_CODENAME=bionic
```

#### 安装步骤
1、官网安装参考手册：https://docs.docker.com/engine/install/centos/
2、确定你是Centos7及以上版本，我们已经做过了
3、yum安装gcc相关环境（需要确保 虚拟机可以上外网）
```shell
yum -y install gcc
yum -y install gcc-c++
```
4、卸载旧的版本
```shell
# 安装yum
apt install yum
# 删除旧版
 sudo yum remove docker \
                  docker-client \
                  docker-client-latest \
                  docker-common \
                  docker-latest \
                  docker-latest-logrotate \
                  docker-logrotate \
                  docker-engine
```
5、安装需要的软件包
```shell
yum install -y yum-utils
```
6、设置镜像仓库
```shell
# 错误
yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo ## 报错
[Errno 14] curl#35 - TCP connection reset by peer
[Errno 12] curl#35 - Timeout
```
```shell
# 正确推荐使用国内的
yum-config-manager --add-repo http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
```
7、安装yum软件包索引
```shell
yum makecache fast
```
8、安装Docker CE
```shell
yum install docker-ce docker-ce-cli containerd.io
```

9、启动Docker
```shell
system start docker
```
10、测试命令
```shell
docker version

docker run hello-world

docker image
```

![](../images/Pasted%20image%2020230908222120.png)

11、卸载
```shell
systemctl stop docker

yum -y remove docker-ce docker-ce-cli containerd.io

rm -rf /var/lib/docker
```

阿里云镜像加速
1、介绍：https://www.aliyun.com/product/acr 
2、注册一个属于自己的阿里云账户(可复用淘宝账号) 
3、进入管理控制台设置密码，开通 
4、查看镜像加速器自己的
![](../images/Pasted%20image%2020230908222405.png)
5、配置镜像加速
```shell
sudo mkdir -p /etc/docker


sudo tee /etc/docker/daemon.json <<-'EOF' 
{ 
"registry-mirrors": ["https://dhr2h8gt.mirror.aliyuncs.com"] 
}
EOF

sudo systemctl daemon-reload

sudo systemctl restart docker
```

测试helloworld
1、启动helloworld
```shell
docker run hello-world
```
2、run干了什么？
![](../images/Pasted%20image%2020230909111922.png)
##### 底层原理
###### Docker是怎么工作的
Docker是一个Client-Server结构的系统，Docker守护进程运行在主机上，然后通过Socket连接从客户端访问，守护进程从客户端接受命令并管理运行在主机上的容器。容器，是一个运行时环境，就是我们前面说到的集装箱。
![](../images/Pasted%20image%2020230909112333.png)
###### 为什么Docker比较VM快
1、docker有着比虚拟机更少的抽象层，由于docker不需要Hypervisor实现硬件资源虚拟化，运行在docker容器上的程序直接使用的都是实际物理机的硬件资源，因此在CPU、内存利用率上docker将会在效率上有明显的优势。
2、docker利用的是宿主机的内核，而不需要Guest OS。因此，当新建一个容器时，docker不需要和虚拟机一样重新加载一个操作系统内核。仍而避免引寻、加载操作系统内核返个比较费资源的过程，当新建一个虚拟机时，虚拟机软件需要加载Guest OS，返个新建过程是分钟级别的。而docker由于直接利用宿主机的操作系统，这省略了返个过程，因此新建一个docker容器只需要几秒钟。
![](../images/Pasted%20image%2020230909113417.png)

##### Docker常用命令
###### 帮助命令
```shell
docker version #显示 Docker 版本信息。
docker info    #显示 Docker 系统信息，包括镜像和容器数。。
docker --help  #帮助
```

######镜像命令
`docker image`
```shell
# 列出本地主机上的镜像
[root@VM-8-12-centos ~]# docker images
REPOSITORY    TAG       IMAGE ID       CREATED        SIZE
hello-world   latest    9c7a54a9a43c   4 months ago   13.3kB

# 解释
REPOSITORY   镜像的仓库源
TAG          镜像的标签
IMAGE ID     镜像的ID
CREATED      镜像创建时间
SIZE         镜像大小

# 同一个仓库可以有多个TAG，代表这个仓库源的不同版本，我们使用REPOSITORY：TAG定义不同的镜像，如果你不定义镜像的标签版本，docker将默认使用lastset镜像！

# 可选项
-a:         列出本地的所有镜像
-q:         只显示镜像id
--digests   显示镜像的摘要信息
```
`docker search`
```shell
# 搜索镜像
[root@VM-8-12-centos ~]# docker search mysql
NAME                            DESCRIPTION                                     STARS     OFFICIAL   AUTOMATED
mysql                           MySQL is a widely used, open-source relation…   14436     [OK]       

# docker search 某个镜像的名称   对应DockerHub仓库中的镜像

# 可选项
--filter=stars=50   ：列出收藏数量小于指定值的镜像。
```
`docker pull`
```shell
# 下载镜像
[root@VM-8-12-centos ~]# docker pull mysql
Using default tag: latest # 不写tag，默认是latest
latest: Pulling from library/mysql
b193354265ba: Downloading  5.504MB/44.91MB   # 分层下载
14a15c0bb358: Download complete 
02da291ad1e4: Download complete 
9a89a1d664ee: Download complete 
a24ae6513051: Download complete 
b85424247193: Download complete 
9a240a3b3d51: Download complete 
8bf57120f71f: Download complete 
c64090e82a0b: Downloading  2.703MB/57.49MB
af7c7515d542: Downloading     721B/5.39kB

Digest: sha256:e9027fe4d91c0153429607251656806cc784e914937271037f7738bd5b8e7709 # 签名
Status: Downloaded newer image for mysql:latest
docker.io/library/mysql:latest 真实位置

# 指定版本下载
[root@VM-8-12-centos ~]# docker pull mysql:5.7
```

`docker rmi`
```shell
# 删除镜像
docker rmi -f 镜像id                    #删除单个
docker rmi -f 镜像名:tag 镜像名:tag      #删除多个
docker rmi -f ${docker images -qa}      #删除全部
```
##### 容器命令
**说明**：有镜像才能创建容器，我们这里使用centos的镜像来测试，就是虚拟一个centos！
```shell
docker pull centos
```
###### 新建容器并启动
```shell
#命令
docker run [OPTIONS] IMAGE [COMMAND][ARG...]

#常用参数说明
--name="Name"             # 给容器指定一个名字
-d                        # 后台方式运行容器，并返回容器的id！
-i                        # 以交互模式运行容器，通过和-t一起使用
-t                        # 以容器重新分配一个终端，通常和 -i 一起使用
-P                        # 随机端口映射（大写）
-p                        # 指定端口映射（小结），一般可以有四种写法
     ip:hostPort:containerPort
     ip::containerPort
     hostPort:containerPort(常用)
     containerPort

# 测试
[root@VM-8-12-centos ~]# docker images
REPOSITORY    TAG       IMAGE ID       CREATED         SIZE
hello-world   latest    9c7a54a9a43c   4 months ago    13.3kB
mysql         5.7       c20987f18b13   20 months ago   448MB
mysql         latest    3218b38490ce   20 months ago   516MB
centos        latest    5d0da3dc9764   24 months ago   231MB

# 使用centos进行交互模式启动容器，在容器内执行/bin/bash命令！
[root@VM-8-12-centos ~]# docker run -it centos /bin/bash
[root@f18597556f8e /]# ls   # 注意地址，已经切换到容器内部了！
bin  etc   lib	  lost+found  mnt  proc  run   srv  tmp  var
dev  home  lib64  media       opt  root  sbin  sys  usr
[root@f18597556f8e /]# exit   # 使用exit 退出容器
exit
```

###### 列出运行的容器

```shell
# 命令
docker ps [OPTIONS]

# 常用参数说明
-a       # 列出当前所有正在运行的容器 + 历史运行过的容器
-l       # 显示最近创建的容器
-n=?     # 显示最近n个创建的容器
-q       # 静默模式，只显示容器编号。
```

###### 退出容器
```shell
exit       # 容器停止并退出
ctrl+P+Q   # 容器不停止退出
```
###### 启动停止容器
```shell
docker start (容器id or 容器名)        # 启动容器
docker restart (容器id or 容器名)      # 重启容器
docker stop (容器id or 容器名)         # 停止容器
docker kill (容器id or 容器名)         # 强制停止容器
```
##### 删除容器
```shell
docker rm 容器id                 # 删除指定容器
docker rm -f $(docker ps -a -q)  # 删除所有容器
docker ps -a -q|xargs docker rm  # 删除所有容器
```

#### 常用其他命令
###### 后台启动容器
```shell
# 命令
docker run -d 容器名

# 例子
docker run -d centos # 启动centos，使用后台方式启动

# 问题：使用docker ps 查看，发现容器已经退出了！
# 解释：Docker容器后台运行，就必须有一个有一个前台进程，容器的命令如果不是那些一直挂起的命令，就会自动退出。
# 比如，你运行了nginx服务，但是docker前台没有运行应用，这种情况下，容器启动后，会立即自杀，因为他觉得没有程序了，所以最好的情况是，将你的应用使用前台进程的方式运行启动。
```
##### 查看日志
```shell
# 命令
docker logs -f -t --tail 容器id

# 例子：我们启动centos，并编写一段脚本来测试玩玩！最后查看日志

[root@VM-8-12-centos ~]# docker run -d centos /bin/sh -c "while true;do echo liubr;sleep 1;done"
36a8b0882c3d71757e234a5b3210374ed5cdc92b56684af0c84446d552e9c15d

[root@VM-8-12-centos ~]# docker ps
CONTAINER ID   IMAGE     COMMAND                  CREATED         STATUS         PORTS     NAMES
36a8b0882c3d   centos    "/bin/sh -c 'while t…"   6 seconds ago   Up 5 seconds    admiring_cray


# -t 显示时间戳
# -f 打印最新的日志
# --tail 数字   显示多少条！
[root@VM-8-12-centos ~]# docker logs -tf --tail 10 36a8b0882c3d
2023-09-09T14:24:45.068203922Z liubr
2023-09-09T14:24:46.070795094Z liubr
2023-09-09T14:24:47.073302616Z liubr
2023-09-09T14:24:48.075813453Z liubr
2023-09-09T14:24:49.078313895Z liubr
2023-09-09T14:24:50.080864154Z liubr
2023-09-09T14:24:51.083440922Z liubr
2023-09-09T14:24:52.086020299Z liubr
# 命令
docker top 容器id

# 测试
[root@VM-8-12-centos ~]# docker top 36a8b0882c3d
UID    PID    PPID   C    STIME  TTY   TIME      CMD
root  15999  5978    0    22:24   ?   00:00:00  /bin/sh -c while true;do...
```
###### 查看容器/镜像的元数据
```shell
# 命令
docker inspect 容器id

# 测试
[root@VM-8-12-centos ~]# docker inspect 36a8b0882c3d
[
    {
        "Id": "36a8b0882c3d71757e234a5b3210374ed5cdc92b56684af0c84446d552e9c15d",
        "Created": "2023-09-09T14:24:09.703818195Z",
        "Path": "/bin/sh",
        "Args": [
            "-c",
            "while true;do echo liubr;sleep 1;done"
        ],
        "State": {
            "Status": "running",
            "Running": true,
            "Paused": false,
            "Restarting": false,
            "OOMKilled": false,
            "Dead": false,
            "Pid": 15999,
            "ExitCode": 0,
            "Error": "",
            "StartedAt": "2023-09-09T14:24:09.9674008Z",
            "FinishedAt": "0001-01-01T00:00:00Z"
        },
	//...
]
```
###### 进入正在运行的容器
```shell
# 命令1
docker exec -it 容器id bashShell

# 测试1
[root@VM-8-12-centos ~]# clear
[root@VM-8-12-centos ~]# docker ps
CONTAINER ID   IMAGE     COMMAND                  CREATED          STATUS          PORTS     NAMES
36a8b0882c3d   centos    "/bin/sh -c 'while t…"   12 minutes ago   Up 12 minutes             admiring_cray
[root@VM-8-12-centos ~]# docker exec -it 36a8b0882c3d /bin/bash
[root@36a8b0882c3d /]# ps -ef
UID        PID  PPID  C STIME TTY          TIME CMD
root         1     0  0 14:24 ?        00:00:00 /bin/sh -c while true;do echo liubr;sleep 1;done
root      1311     0  0 14:45 pts/0    00:00:00 /bin/bash
root      1329     1  0 14:46 ?        00:00:00 /usr/bin/coreutils --coreutils-prog-shebang=sleep 
root      1330  1311  0 14:46 pts/0    00:00:00 ps -ef

# 命令2
docker attach 容器id

# 测试2
[root@VM-8-12-centos ~]# docker exec -it 36a8b0882c3d /bin/bash
[root@36a8b0882c3d /]# ps -ef
UID        PID  PPID  C STIME TTY          TIME CMD
root         1     0  0 14:24 ?        00:00:00 /bin/sh -c while true;do echo liubr;sleep 1;done
root      1595     0  0 14:50 pts/0    00:00:00 /bin/bash
root      1692     1  0 14:51 ?        00:00:00 /usr/bin/coreutils --coreutils-prog-shebang=sleep 
root      1693  1595  0 14:51 pts/0    00:00:00 ps -ef

# 区别
# exec   是在容器中打开新的终端，并且可以启动新的进程 
# attach 直接进入容器启动命令的终端，不会启动新的进程
```

##### 从容器内拷贝文件到主机上
```shell
# 命令
docker cp 容器id:容器内路径 目的主机路径

# 测试
# 容器内执行，创建一个文件测试
[root@70d61ecedcbc /]# cd home
[root@70d61ecedcbc home]# touch f1
[root@70d61ecedcbc home]# ls
f1
[root@70d61ecedcbc home]# exit
exit
[root@VM-8-12-centos ~]# docker cp 70d61ecedcbc:/home/f1 /home
Successfully copied 1.54kB to /home
[root@VM-8-12-centos ~]# cd /home
[root@VM-8-12-centos home]# ls
f1  lighthouse
```

#### 小结
![](../images/Pasted%20image%2020230910112116.png)

常用命令

```docker
attach    Attach to a running container                 # 当前 shell 下 attach 连接指定运行镜像
build     Build an image from a Dockerfile              # 通过 Dockerfile 定制镜像
commit    Create a new image from a container changes   # 提交当前容器为新的镜像 
cp        Copy files/folders from the containers filesystem to the host path #从容器中拷贝指定文件或者目录到宿主机中
create    Create a new container                        # 创建一个新的容器，同run，但不启动容器
diff      Inspect changes on a container's filesystem   # 查看 docker 容器变化 
events    Get real time events from the server          # 从 docker 服务获取容器实时事件
exec      Run a command in an existing container        # 在已存在的容器上运行命令
export    Stream the contents of a container as a tar archive   # 导出容器的内容流作为一个 tar 归档文件[对应 import ]
history   Show the history of an image                  # 展示一个镜像形成历史 
images    List images                                   # 列出系统当前镜像
import    Create a new filesystem image from the contents of a tarball # 从 tar包中的内容创建一个新的文件系统映像[对应export]
info      Display system-wide information               # 显示系统相关信息 
inspect   Return low-level information on a container   # 查看容器详细信息
kill      Kill a running container                      # kill 指定 docker 容器
load      Load an image from a tar archive              # 从一个 tar 包中加载一个镜像[对应 save]
login     Register or Login to the docker registry server    # 注册或者登陆一个 docker 源服务器
logout    Log out from a Docker registry server          # 从当前 Docker registry 退出
logs      Fetch the logs of a container                 # 输出当前容器日志信息 port      Lookup the public-facing port which is NAT-ed to PRIVATE_PORT    # 查看映射端口对应的容器内部源端口
pause     Pause all processes within a container        # 暂停容器
ps        List containers                               # 列出容器列表
pull      Pull an image or a repository from the docker registry server   # 从docker镜像源服务器拉取指定镜像或者库镜像
push      Push an image or a repository to the docker registry server    # 推送指定镜像或者库镜像至docker源服务器
restart   Restart a running container                   # 重启运行的容器
rm        Remove one or more containers                 # 移除一个或者多个容器 
rmi       Remove one or more images             # 移除一个或多个镜像[无容器使用该镜像才可删除，否则需删除相关容器才可继续或 -f 强制删除]
run       Run a command in a new container              # 创建一个新的容器并运行一个命令
save      Save an image to a tar archive                # 保存一个镜像为一个tar 包[对应 load]
search    Search for an image on the Docker Hub         # 在 docker hub 中搜索镜像
start     Start a stopped containers                    # 启动容器
stop      Stop a running containers                     # 停止容器
tag       Tag an image into a repository                # 给源中镜像打标签
top       Lookup the running processes of a container   # 查看容器中运行的进程信息
unpause   Unpause a paused container                    # 取消暂停容器
version   Show the docker version information           # 查看 docker 版本号 
wait      Block until a container stops, then print its exit code   # 截取容
器停止时的退出状态值
```

#### 作业练习
---
> 使用Docker安装Nginx
```
# 1、搜索镜像
[root@VM-8-12-centos ~]# docker search nginx
NAME           DESCRIPTION                    STARS    
nginx         Official build of Nginx.         18986

# 2、拉取镜像
[root@VM-8-12-centos ~]# docker pull nginx
Using default tag: latest
latest: Pulling from library/nginx
a2abf6c4d29d: Pull complete 
a9edb18cadd1: Pull complete 
589b7251471a: Pull complete 
186b1aaa4aa6: Pull complete 
b4df32aa5a72: Pull complete 
a0bcbecc962e: Pull complete 
Digest: sha256:0d17b565c37bcbd895e9d92315a05c1c3c9a29f762b011a10c54a66cd53c9b31
Status: Downloaded newer image for nginx:latest
docker.io/library/nginx:latest

# 3、启动容器
[root@VM-8-12-centos ~]# docker images
REPOSITORY    TAG       IMAGE ID       CREATED         SIZE
hello-world   latest    9c7a54a9a43c   4 months ago    13.3kB
nginx         latest    605c77e624dd   20 months ago   141MB
mysql         5.7       c20987f18b13   20 months ago   448MB
mysql         latest    3218b38490ce   20 months ago   516MB
centos        latest    5d0da3dc9764   24 months ago   231MB

[root@VM-8-12-centos ~]# docker run -d --name mynginx -p 3500:80 nginx
9fdfbbd35c95f2756a68bc3de1c82441f0c244ef7a71949693626f780b953393

[root@VM-8-12-centos ~]# docker ps
CONTAINER ID   IMAGE     COMMAND                  CREATED         STATUS         PORTS                                   NAMES
9fdfbbd35c95   nginx     "/docker-entrypoint.…"   8 seconds ago   Up 7 seconds 0.0.0.0:3500->80/tcp, :::3500->80/tcp   mynginx

# 4、测试访问
[root@VM-8-12-centos ~]# curl localhost:3500
<html>
<title>Welcome to nginx!</title>
...
</html>

# 5、进入容器
 [root@VM-8-12-centos ~]# docker exec -it mynginx /bin/bash
root@9fdfbbd35c95:/# whereis nginx 
nginx: /usr/sbin/nginx /usr/lib/nginx /etc/nginx /usr/share/nginx
root@9fdfbbd35c95:/# cd /usr/share/nginx
root@9fdfbbd35c95:/usr/share/nginx# ls
html
root@9fdfbbd35c95:/usr/share/nginx# cd html
root@9fdfbbd35c95:/usr/share/nginx/html# ls
50x.html  index.html
root@9fdfbbd35c95:/usr/share/nginx/html# cat index.html 
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
<style>
html { color-scheme: light dark; }
body { width: 35em; margin: 0 auto;
font-family: Tahoma, Verdana, Arial, sans-serif; }
</style>
</head>
<body>
<h1>Welcome to nginx!</h1>
<p>If you see this page, the nginx web server is successfully installed and
working. Further configuration is required.</p>

<p>For online documentation and support please refer to
<a href="http://nginx.org/">nginx.org</a>.<br/>
Commercial support is available at
<a href="http://nginx.com/">nginx.com</a>.</p>

<p><em>Thank you for using nginx.</em></p>
</body>
</html>
```
开放TCP/UDP端口
![|400](../images/Pasted%20image%2020230910143726.png)
浏览器正常访问
![|500](../images/Snipaste_2023-09-10_14-37-54.png)

> 使用docker安装tomcat
```shell
# 官网文档解锁
# -it ：交互模式
# --rm ：容器启动成功并退出以后容器自动移除，一般在测试情况下使用！
docker run -it --rm tomcat:9.0

# 1、下载tomcat镜像
docker pull tomcat

# 2、启动
docker run -d -p 8080:8080 --name tomcat9 tomcat

# 3、进入tomcat
docker exec -it tomcat9 /bin/bash

# 4、思考：我们以后要部署项目，还需要进入容器中，是不是十分麻烦，要是有一种技术，可以将容器内和我们Linux进行映射挂载就好了？我们后面会将数据卷技术来进行挂载操作，也是一个核心内容，这里大家先听听名词就好，我们很快就会讲到！
```

>使用 docker 部署 es + kibana

```shell
# 我们启动es这种容器需要考虑几个问题
1、端口暴露问题9200、9300
2、数据卷的挂载问题 data、plugins、conf
3、吃内存  -“ES_JAVA_OPTS=-Xms512m -Xmx512”

# 扩展命令
docker stats 容器id    # 查看容器的cpu内存和网络状态

# 1、启动es测试
docker run -d --name elasticsearch -p 9200:9200 -p 9300:9300 -e "discovery.type=single-node" elasticsearch:7.6.2

# 2、启动之后很卡，使用docker stats 容器 id 查看cpu状态 ， 发现占用的很大
CONTAINER ID   NAME          CPU %  MEM USAGE / LIMIT   MEM %   NET I/O     BLOCK I/O       PIDS
df3910010035  elasticsearch  0.03%  1.26GiB / 1.952GiB  64.58%  656B / 0B   166MB / 729kB   42

# 3、测试访问
[root@VM-8-12-centos ~]# curl localhost:9200
{
  "name" : "df3910010035",
  "cluster_name" : "docker-cluster",
  "cluster_uuid" : "r0f9Z3vRSmekBlxkdHSy6A",
  "version" : {
    "number" : "7.6.2",
    "build_flavor" : "default",
    "build_type" : "docker",
    "build_hash" : "ef48eb35cf30adf4db14086e8aabd07ef6fb113f",
    "build_date" : "2020-03-26T06:34:37.794943Z",
    "build_snapshot" : false,
    "lucene_version" : "8.4.0",
    "minimum_wire_compatibility_version" : "6.8.0",
    "minimum_index_compatibility_version" : "6.0.0-beta1"
  },
  "tagline" : "You Know, for Search"
}

# 4、增加上内存限制启动
docker run -d --name elasticsearch -p 9200:9200 -p 9300:9300 -e "discovey.type=single-node" -e ES_JAVA_OPTS="-Xms64m -Xmx512m" elasticsearch:7.6.2

# 5、启动之后，使用 docker stats 查看下 cpu 状态
CONTAINER ID   NAME        CPU %     MEM USAGE / LIMIT   MEM %   NET I/O   BLOCK I/O      PIDS
9f7b62dc48b7  elasticsearch  0.81%  382.5MiB / 1.952GiB  19.14%  656B / 0B 106MB / 729kB   44

# 6、访问测试，效果一样，ok
[root@VM-8-12-centos ~]# curl localhost:9200

# 思考：如果我们要使用 kibana ， 如果配置连接上我们的es呢？网络该如何配置呢？
```
![](../images/Pasted%20image%2020230910155204.png)
#### 可视化
* Portainer（先用这个）
```shell
docker run -d -p 8088:9000 \
--restart=always -v /var/run/docker.sock:/var/run/docker.sock --privileged=true portainer/portainer
```

* Rancher（CI/CD再用这个）
```shell
# 安装rancher-server
docker run --name rancher-server -p 8000:8080 -v /ect/localtime:/etc/localtime:ro -d rancher/server
# 安装agent
docker run --rm --privileged -v /var/run/docker.sock:/var/run/docker.sock -v /var/lib/rancher:/var/lib/rancher rancher/agent:v1.2.11 http://39.101.191.131:8000/v1/scripts/D3DBD43F263109BB881F:1577750400000:7M0y BzCw4XSxJklD7TpysYIpI
```
#### 介绍
Portaine 是 Docker 的图形化管理工具，提供状态显示面板、应用模板快速部署、容器镜像网络数据卷的基本操作（包括上传下载镜像，创建容器等操作）、事件日志显示、容器控制台操作、Swarm集群和服务等集中管理和操作、登录用户管理和控制等功能。功能十分全面，基本能满足中小型单位对容器管理的全部需求。
如果仅有一个 docker 宿主机，则可以使用单机版运行，Portainer单机版运行十分简单，只需要一条语句即可启动容器，来管理该机器上的docker镜像、容器等数据。
```shell
docker run -d -p 8088:9000 \
--restart=always -v /var/run/docker.sock:/var/run/docker.sock --privileged=true portainer/portainer
```
访问方式：http://IP:8088
首次登陆需要注册用户，给admin用户设置密码：
![|500](../images/Snipaste_2023-09-10_16-21-15.png)
单机版这里选择local即可，选择完毕，点击Connect即可连接到本地docker：
![](../images/Pasted%20image%2020230910165027.png)
登录成功！
![](../images/Pasted%20image%2020230910165040.png)

#### Docker镜像讲解
##### 镜像是什么
镜像是一种轻量级、可执行的独立软件包，用来打包软件运行环境和基本运行环境开发的软件，它包含某个软件所有内容，包括代码、运行时、库、环境变量和配置文件。

##### Docker镜像加载原理
>UnionFS （联合文件系统）

UnionFS（联合文件系统）：Union文件系统（UnionFS）是一种分层、轻量化并且高性能的文件系统，它支持系统的修改作为一次提交来一层层的叠加，同时可以将不同目录挂载到同一个目录挂载到同一个虚拟文件系统下（unite several directories into a single virtual filesystem）。Union 文件系统是Docker镜像的基础。镜像可以通过分层来进行继承，基于基础镜像（没有父镜像），可以制作各种具体的应用镜像。
特性：一次同时加载多个文件系统，但从外面看起来，只能看到一个文件系统，联合加载会把各层文件系统叠加起来，这样最终的文件系统会包含所有的底层的文件和目录

>Docker 镜像加载原理

docker的镜像实际上由一层一层的文件系统组成，这种层级的文件系统UnionFS。
bootfs(boot file system)主要包含bootloader和kernel，bootloader主要是引导加载kernel，Linux刚启动时会加载bootfs文件系统，在Docker镜像的最底层是bootfs。这一层与我们典型的Linux/Unix系统是一样的，包含boot加载器和内核。当boot加载完成之后整个内核就都在内存中了，此时内存的使用权已由bootfs转交给内核，此时系统也会卸载bootfs。

rootfs(root file system)，在 bootfs之上。包含的就是典型 Linux 系统中的 /dev. /proc, /bin, /etc 等标准目录和文件，rootfs就是各种不同的操作系统发行版，比如 Ubuntu，Centos 等。

![](../images/Pasted%20image%2020230910195621.png)

平时我们安装进去虚拟机的Centos都是好几个G，为什么Docker这里才200M？

![](../images/Pasted%20image%2020230910195750.png)

对于一个精简的OS，rootfs可以很小，只需要包含最基本的命令，工具和程序库就可以了， 因为底层直接用 Host 的 kernel，自己只需要提供rootfs就可以了。由此可见对于不同的linux发行版，bootfs基本是一致的，rootfs会有差别，因此不同的发行版可以公用bootfs。

#### 分层理解
>分层的镜像

我们可以去下载一个镜像，注意观察下载的日志输出，可以看到是一层一层的在下载！

![](../images/Pasted%20image%2020230910201015.png)

思考：为什么 Docker 镜像要采用这种分层的结构呢？

最大的好处，我觉得莫过于是资源共享了！比如有多个镜像都是从相同的Base镜像构建而来，那么宿主机只需要在磁盘上保留一份bash镜像，同时内存中也只需要加载一份bash镜像，这样就可以为所有的容器服务了，而且镜像的每一层都可以共享。

查看镜像分层的方式可以通过 docker image inspect 命令！

```json
[root@VM-8-12-centos ~]# docker image inspect redis:latest
[
    //....
        "RootFS": {
            "Type": "layers",
            "Layers": [
                "sha256:2edcec3590a4ec7f40cf0743c15d78fb39d8326bc029073b41ef9727da6c851f",
                "sha256:9b24afeb7c2f21e50a686ead025823cd2c6e9730c013ca77ad5f115c079b57cb",
                "sha256:4b8e2801e0f956a4220c32e2c8b0a590e6f9bd2420ec65453685246b82766ea1",
                "sha256:529cdb636f61e95ab91a62a51526a84fd7314d6aab0d414040796150b4522372",
                "sha256:9975392591f2777d6bf4d9919ad1b2c9afa12f9a9b4d260f45025ec3cc9b18ed",
                "sha256:8e5669d8329116b8444b9bbb1663dda568ede12d3dbcce950199b582f6e94952"
            ]
        },
        "Metadata": {
            "LastTagTime": "0001-01-01T00:00:00Z"
        }
    }
]
```

###### 理解：
所有的 Docker 镜像都起始于一个基础镜像层，当进行修改或增加新的内容时，就会当前镜像层之上，创建新的镜像层。

举一个简单的例子，假如基于Ubuntu Linux 16.04 创建一个新的镜像，这就是新的镜像的第一层；如果在该镜像中添加Python包，就会在基础镜像层之上创建第二个镜像层；如果继续添加一个安全补丁，就会创建第三个镜像层。

该镜像当前已经包含3个镜像层，如下图所示（这只是一个用于演示的很简单的例子）。

![](../images/Pasted%20image%2020230910202901.png)

在