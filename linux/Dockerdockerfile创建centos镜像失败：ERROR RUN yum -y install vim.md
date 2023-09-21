1、编写Dockerfile
```shell
# vim Dockerfile

FROM centos
MAINTAINER coding<1502473931@qq.com>
#把宿主机当前上下文的read.txt拷贝到容器/usr/local/路径下
COPY read.txt /usr/local/cincontainer.txt
# 把java与tomcat添加到容器中
ADD jdk-8u11-linux-x64.tar.gz /usr/local/
ADD apache-tomcat-9.0.22.tar.gz /usr/local/
# 安装 vim 编辑器
RUN yum -y install vim
# 设置工作访问时候的WORKDIR路径，登录落脚点
ENV MYPATH /usr/local
WORKDIR $MYPATH
# 配置java与tomcat环境变量
ENV JAVA_HOME /usr/local/jdk1.8.0_11
ENV CLASSPATH $JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
ENV CATALINA_HOME /usr/local/apache-tomcat-9.0.22
ENV CATALINA_BASE /usr/local/apache-tomcat-9.0.22
ENV PATH $PATH:$JAVA_HOME/bin:$CATALINA_HOME/lib:$CATALINA_HOME/bin
# 容器运行时监听的端口
EXPOSE 8080
# 启动时运行tomcat
# ENTRYPOINT ["/usr/local/apache-tomcat-9.0.22/bin/startup.sh"]
# CMD ["/usr/local/apache-tomcat-9.0.22/bin/catalina.sh","run"]
CMD /usr/local/apache-tomcat-9.0.22/bin/startup.sh && tail -F /usr/local/apache-tomcat-9.0.22/bin.logs/catalina.out
```

2、创建镜像时候报错
```shell
 => ERROR [5/6] RUN yum -y install vim                                                  1.9s
------
 > [5/6] RUN yum -y install vim:
1.778 CentOS Linux 8 - AppStream                       83  B/s |  38  B     00:00    
1.786 Error: Failed to download metadata for repo 'appstream': Cannot prepare internal mirrorlist: No URLs in mirrorlist
------
Dockerfile:11
--------------------
   9 |     ADD apache-tomcat-9.0.22.tar.gz /usr/local/
  10 |     # 安装 vim 编辑器
  11 | >>> RUN yum -y install vim
  12 |     # 设置工作访问时候的WORKDIR路径，登录落脚点
  13 |     ENV MYPATH /usr/local
--------------------
ERROR: failed to solve: process "/bin/sh -c yum -y install vim" did not complete successfully: exit code: 1
```
3、问题分析
```shell
ERROR: failed to solve: process "/bin/sh -c yum -y install vim" did not complete successfully: exit code: 1
```

4、问题解决
```shell
FROM centos:7
```
5、解决成功

```shell
[root@VM-8-12-centos tomcat]# docker build -t diytomcat .
[+] Building 88.2s (11/11) FINISHED                                           docker:default
 => [internal] load .dockerignore                                                       0.0s
 => => transferring context: 2B                                                         0.0s
 => [internal] load build definition from Dockerfile                                    0.0s
 => => transferring dockerfile: 1.11kB                                                  0.0s
 => [internal] load metadata for docker.io/library/centos:7                            15.4s
 => CACHED [1/6] FROM docker.io/library/centos:7@sha256:9d4bcbbb213dfd745b58be38b13b99  0.0s
 => [internal] load build context                                                       0.0s
 => => transferring context: 123B                                                       0.0s
 => [2/6] COPY read.txt /usr/local/cincontainer.txt                                     0.0s
 => [3/6] ADD jdk-8u11-linux-x64.tar.gz /usr/local/                                     4.1s
 => [4/6] ADD apache-tomcat-9.0.22.tar.gz /usr/local/                                   0.3s
 => [5/6] RUN yum -y install vim                                                       64.9s
 => [6/6] WORKDIR /usr/local                                                            0.1s
 => exporting to image                                                                  3.2s
 => => exporting layers                                                                 3.2s
 => => writing image sha256:689775732ebe2b7f1bc5b073830bc48808d78059989fc98b81e08ae20b  0.0s 
 => => naming to docker.io/library/diytomcat                                            0.0s 

```