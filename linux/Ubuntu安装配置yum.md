# 1、简介

Yum（全称为 Yellow dog Updater, Modified）是一个在Fedora和RedHat以及CentOS中的Shell前端软件包管理器。基于RPM包管理，能够从指定的[服务器](https://cloud.tencent.com/act/pro/promotion-cvm?from_column=20065&from=20065)自动下载RPM包并且安装，可以自动处理依赖性关系，并且一次安装所有依赖的软件包，无须繁琐地一次次下载、安装

# 2、安装yum

## 2.1检测是否安装 build-essential 包

```javascript
sudo apt-get install build-essential
或者
apt-get install build-essential
```

复制

## 2.2安装yum

```javascript
sudo apt-get install yum
或者
apt-get install yum
```

## 2.3检测是否安装成功

输入yum指令，看是否有操作提示
![](../images/Pasted%20image%2020230908211659.png)

# 3、配置yum源

由于是Ubuntu没有yum源，所以要想使用yum安装软件必须要配置yum安装源。在/etc/yum/repos.d/目录下创建两个文件，fedora-163.repo和fedora-updates-163.repo。分别复制以下配置信息保存即可。

fedora-163.repo配置如下信息

```javascript
[fedora]
 
name=Fedora 17 - $basearch - 163.com
failovermethod=priority
baseurl=http://mirrors.163.com/fedora/releases/17/Everything/$basearch/os/
mirrorlist=https://mirrors.fedoraproject.org/metalink?repo=fedora-17&arch=$basearch
enabled=1
metadata_expire=7d
gpgcheck=1
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-fedora-$basearch
 
[fedora-debuginfo]
name=Fedora 17 - $basearch - Debug - 163.com
failovermethod=priority
baseurl=http://mirrors.163.com/fedora/releases/17/Everything/$basearch/debug/
mirrorlist=https://mirrors.fedoraproject.org/metalink?repo=fedora-debug-17&arch=$basearch
enabled=0
metadata_expire=7d 
gpgcheck=1
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-fedora-$basearch
 
[fedora-source]
name=Fedora 17 - Source - 163.com
failovermethod=priority
baseurl=http://mirrors.163.com/fedora/releases/17/Everything/source/SRPMS/
mirrorlist=https://mirrors.fedoraproject.org/metalink?repo=fedora-source-17&arch=$basearch
enabled=0
metadata_expire=7d
gpgcheck=1
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-fedora-$basearch
```

fedora-updates-163.repo配置信息如下

```javascript
[updates]
name=Fedora 17 - $basearch - Updates - 163.com
failovermethod=priority
baseurl=http://mirrors.163.com/fedora/updates/17/$basearch/
mirrorlist=https://mirrors.fedoraproject.org/metalink?repo=updates-released-f17&arch=$basearch
enabled=1
gpgcheck=1
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-fedora-$basearch
 
[updates-debuginfo]
name=Fedora 17 - $basearch - Updates - Debug - 163.com
failovermethod=priority
baseurl=http://mirrors.163.com/fedora/updates/17/$basearch/debug/
mirrorlist=https://mirrors.fedoraproject.org/metalink?repo=updates-released-debug-f17&arch=$basearch
enabled=0
gpgcheck=1
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-fedora-$basearch

[updates-source]
name=Fedora 17 - Updates Source - 163.com
failovermethod=priority
baseurl=http://mirrors.163.com/fedora/updates/17/SRPMS/
mirrorlist=https://mirrors.fedoraproject.org/metalink?repo=updates-released-source-f17&arch=$basearch
enabled=0
gpgcheck=1
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-fedora-$basearch
```

vim中删除全部使用ggdG即可
# 4、执行配置

在终端输入

```javascript
yum makecache
```

配置完成，可以使用yum源了