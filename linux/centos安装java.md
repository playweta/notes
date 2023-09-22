###  无需配置环境变量

#### 1.查看云端目前支持安装的JDK版本
```
yum search java|grep jdk
```
##### 2.选择JDK版本，并安装
```
yum install -y java-1.8.0-openjdk
```
##### 3.检查是否安装成功

```
java -version
```

##### 4.查看JDK的安装目录

```
find / -name 'java'
```

