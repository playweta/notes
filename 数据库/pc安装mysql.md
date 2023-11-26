## 二进制安装：

官网：https://dev.mysql.com/downloads/mysql/

以下为my.ini文件的配置内容：
```shell
[mysqld]
# 设置3306端口
port=3306
# 设置mysql的解压目录
basedir=D:\\MYSQL\\mysql-8.0.22-winx64
# 设置mysql数据库的数据的存放目录,一般在解压目录下的data(此目录在下一个初始化操作后会出现)
datadir=D:\\MYSQL\\mysql-8.0.22-winx64\\data
# 允许最大连接数
max_connections=200
# 允许连接失败的次数。
max_connect_errors=10
# 服务端使用的字符集默认为utf8mb4
character-set-server=utf8mb4
# 创建新表时将使用的默认存储引擎
default-storage-engine=INNODB
# 默认使用“mysql_native_password”插件认证
#mysql_native_password
default_authentication_plugin=mysql_native_password
[mysql]
# 设置mysql客户端默认字符集
default-character-set=utf8mb4
[client]
# 设置mysql客户端连接服务端时默认使用的端口
port=3306
default-character-set=utf8mb4

```

使用初始化命令

```shell
mysqld --initialize-insecure --user=mysql
```

 跑完之后就会自动生产data文件夹了

 安装并注册Mysql服务
 
```shell
mysqld -install Mysql8
```
删除服务

```shell
sc delete 服务名
```

启动服务

```shell
net start Mysql8
```

