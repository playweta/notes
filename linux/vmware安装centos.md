## 一、CentOS7虚拟机安装

1. **默认已经安装VMware** ，点击创建新的虚拟机；  
    ![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/20201111114710625.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zODUzNTI0NA==,size_16,color_FFFFFF,t_70#pic_center)
    
2. **这里一般使用典型配置即可，点击下一步** ；  
    ![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/20201111114850920.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zODUzNTI0NA==,size_16,color_FFFFFF,t_70#pic_center)
    
3. **选择稍后安装操作系统，点击下一步** ；  
    ![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/20201111115058862.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zODUzNTI0NA==,size_16,color_FFFFFF,t_70#pic_center)
    
4. **选择linux操作系统，版本选择CentOS64位，若您的计算机为32位，则版本选择CentOS（此步骤会影响网络配置，请提前检查自己计算机为32/64位），点击下一步** ；  
    ![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/20201111115445526.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zODUzNTI0NA==,size_16,color_FFFFFF,t_70#pic_center)
    
5. **命名虚拟机及选择虚拟机存储磁盘，点击下一步** ；  
    ![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/2020111111570276.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zODUzNTI0NA==,size_16,color_FFFFFF,t_70#pic_center)
    
6. **指定虚拟机磁盘空间大小，默认20GB（推荐），在此我选择30G，并选择将虚拟磁盘拆分成多个文件，点击下一步** ；  
    ![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/20201111115857522.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zODUzNTI0NA==,size_16,color_FFFFFF,t_70#pic_center)
    
7. **这一步可选择直接完成，也可以选择自定义硬件，将USB,声卡，打印机移除（根据个人情况选择），点击完成**；  
    ![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/20201111120000308.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zODUzNTI0NA==,size_16,color_FFFFFF,t_70#pic_center)
    
8. **虚拟机基本配置完成，点击虚拟机的名称，找到CD/DVD一项进行设置，如下图所示（如没有centos iso文件的， 可评论找本小白获取）** 。  
    ![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/20201111120638466.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zODUzNTI0NA==,size_16,color_FFFFFF,t_70#pic_center)
    
9. **点击开启虚拟机，选择第一项安装CentOS7操作系统，键盘上下控制** 。  
    ![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/20201111120905471.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zODUzNTI0NA==,size_16,color_FFFFFF,t_70#pic_center)  
    ![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/20201111120942576.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zODUzNTI0NA==,size_16,color_FFFFFF,t_70#pic_center)
    
10. **系统自动跳转到下面一步，选择语言，选择中文（根据自身情况选择），点击继续** 。  
    ![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/20201111121518316.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zODUzNTI0NA==,size_16,color_FFFFFF,t_70#pic_center)
    
11. **软件安装可选择最小安装，也可以选择桌面安装（根据情况决定），安装位置点击，选择自动分区，点击完成；网络和主机名选择开启，点击完成，然后开始安装** 。  
    ![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/20201111122432896.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zODUzNTI0NA==,size_16,color_FFFFFF,t_70#pic_center)  
    ![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/20201111122607701.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zODUzNTI0NA==,size_16,color_FFFFFF,t_70#pic_center)
    
12. **设置root密码（记住）及用户密码，等待系统安装完成后重启** 。![](https://i2.wp.com/img-blog.csdnimg.cn/20201111122828561.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zODUzNTI0NA==,size_16,color_FFFFFF,t_70#pic_center)  
    ![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/20201111122927680.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zODUzNTI0NA==,size_16,color_FFFFFF,t_70#pic_center)  
    ![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/20201111123413140.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zODUzNTI0NA==,size_16,color_FFFFFF,t_70#pic_center)

## 二、CentOS7网络配置（可上网）

1. **用root超级用户登录，输入刚才设置的密码** 。  
    ![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/2020111112352930.png#pic_center)
    
2. **点击编辑后选择虚拟网络编辑器，设置网络连接方式，点击更改设置，在此选择桥接模式（本人使用的是无线WLAN），点击应用，然后确认** 。  
    ![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/2020111112381973.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zODUzNTI0NA==,size_16,color_FFFFFF,t_70#pic_center)  
    ![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/20201111123952776.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zODUzNTI0NA==,size_16,color_FFFFFF,t_70#pic_center)
    

![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/20201111124057865.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zODUzNTI0NA==,size_16,color_FFFFFF,t_70#pic_center)

3. **设置该虚拟机的网络连接方式为桥接模式，点击确定** 。  
    ![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/20201111124338489.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zODUzNTI0NA==,size_16,color_FFFFFF,t_70#pic_center)
    
4. **打开网络与共享中心，选择以太网，点击更改适配器选项** 。  
    ![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/20201111125036103.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zODUzNTI0NA==,size_16,color_FFFFFF,t_70#pic_center)
    
5. **选择WLAN，右击状态，点击详细信息，查看具体信息** 。  
    ![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/20201111125249509.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zODUzNTI0NA==,size_16,color_FFFFFF,t_70#pic_center)
    
6. **进入虚拟机，查看/etc/sysconfig/network-scripts，命令如下** 。  
    ![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/20201111125738743.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zODUzNTI0NA==,size_16,color_FFFFFF,t_70#pic_center)
    
7. **打开if-cfg-eno16777728文件，命令如下，进行修改，IPADDR需与WLAN在同一个网段，NETWORK与网关相同，NETSTAT为子网掩码，GATEWAY为网关，DNS为DNS服务器，保存后重启网关** 。  
    ![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/20201111130243577.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zODUzNTI0NA==,size_16,color_FFFFFF,t_70#pic_center)  
    ![重启网卡](https://i2.wp.com/img-blog.csdnimg.cn/20201111131307130.png#pic_center)
    
8. **ping百度网址，成功ping通** 。  
    ![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/20201111131138916.png#pic_center)