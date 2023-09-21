##### 问题：查看容器ip时执行命令
`docker exec -it  tomcat01 ip addr`    报错
###### 原因：
下载的镜像是阉割版的有好多命令是没有的，然后在这里提醒大家提前下载好需要用到的指令在镜像中
```shell
[root@VM-8-12-centos ~]# docker exec -it tomcat01 ip addr
OCI runtime exec failed: exec failed: unable to start container process: exec: "ip": executable file not found in $PATH: unknown
```
###### 解决办法
1、进入容器指令 `docker exec -it [容器名] /bin/bash`
2、进入容器，执行`apt update && apt install -y iproute2` 命令
3、容器不停止退出：`ctrl +P + Q`
4、然后再执行`docker exec -it [容器名] ip addr`
```
[root@VM-8-12-centos ~]# docker exec -it tomcat01 ip addr
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
146: eth0@if147: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default 
    link/ether 02:42:ac:11:00:03 brd ff:ff:ff:ff:ff:ff link-netnsid 0
    inet 172.17.0.3/16 brd 172.17.255.255 scope global eth0
       valid_lft forever preferred_lft forever
```

##### 问题：容器互通时执行命令
`docker exec -it tomcat01 ping [ip地址]`  报错
###### 原因：
下载的镜像是阉割版的有好多命令是没有的然后在这里提醒大家提前下载好需要用到的指令在镜像中
```
[root[@zy]/]# docker exec -it tomcat01 ping 172.17.0.3  
OCI runtime exec failed: exec failed: unable to start container process: exec: “ping”: executable file not found in $PATH: unknown
```
###### 解决办法
1、进入容器指令`docker exec -it [容器名] /bin/bash`
2、进入容器执行，`apt-get update && apt-get install -y iputils-ping`  命令
3、容器不停止退出：`Ctrl + P + Q`
4、然后再次执行 `docker exec -it tomcat01 ping [ip地址]`
```shell
[root@VM-8-12-centos ~]# docker exec -it tomcat01 ping baidu.com
PING baidu.com (39.156.66.10) 56(84) bytes of data.
64 bytes from 39.156.66.10 (39.156.66.10): icmp_seq=1 ttl=247 time=40.2 ms

```
