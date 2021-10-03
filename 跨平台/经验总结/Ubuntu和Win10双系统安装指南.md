@[TOC](Ubuntu和Win10双系统安装指南)

**目录**
[toc]

标题：Ubuntu和Win10双系统安装指南

> **所需**：
> 1) Ubuntu16.04安装包 
> 2) Windows安装包 
> 3) U盘 
> 4) DiskGenius 
> 5) WePE 
> 6) Ultraiso（软碟通） 
> 7) 网络等


本文以联想Thinkpad笔记本 E440为例进行讲解。


## 1 制作启动盘
---
**移动硬盘做启动盘**

 - 先删除分区，保存分区表，再快速分区，格式化
 - 再点击删除扇区数据彻底清除数据
 - 再把Guid改成了MBR引导，就可以利用移动硬盘来做启动盘了。
 - 但移动硬盘有个问题，驱动器识别得不是很好，有时无法识别。

**U盘做启动盘**
- 直接利用WEPE按要求刻录，装一个微型的win10系统
- 然后将系统映像文件装在非系统盘目录下。

**虫部落装系统教程链接**
[http://bbs.chongbuluo.com/thread-5195-1-1.html](http://bbs.chongbuluo.com/thread-5195-1-1.html)

**雨林木风各版本系统下载**
[http://www.ylmf888.com/win10/](http://www.ylmf888.com/win10/)

## 2 装 Win7 系统

----

**装系统必备知识**
1.	开机时 Thinkpad 连续按F1进入BIOS界面，不同品牌快捷键不同
2.	在Startup中Boot选项中 调整启动优先顺序
3.	制作U盘启动盘

**U盘重装系统步骤**
1. 设立U盘启动盘
2. 改变Boot启动优先顺序 U盘启动
3. 重启，正常安装系统并分区
4. 激活系统

**【U盘重装Win7系统】**
1. 将win7iso镜像文件拷到非系统盘文件夹里
2. 制作微PE启动盘 
3. 按F1进入Bios Boot启动界面选择USB HDD启动为首位
4. 重启进入PE中CGI还原界面
5. 找到对应镜像文件即可操作自动重装
CGI备份还原Win7，重装只要两三分钟，固态硬盘就是快。

**【重装Win7系统启动后一直停留在正在启动页面解决办法】**
由于Windows7不完全支持UEFI，需要CSM(Compatibility Support Module)支持，也就是Legacy BIOS的支持。可以用以下的方法：
1. 开机后按F1进入BIOS，在Security里secure boot 选择disabled（可解决开机时要数字签名的问题）
2. 在Startup 里将UEFI Boot改为both，允许Legacy。
3. 将CSM Support设置为Yes。
4. 备份还原不影响非系统盘的文件，但以防万一还是请将重要文件备份。 
5. 系统及Office2016激活，使用激活软件即可。
6. Win10安装步骤类同Win7，通过U盘启动盘，用WinPE系统中的备份还原来安装镜像即可。


## 3 装 Ubuntu 16.04 系统
-----
步骤如下：
- Ultraiso做U盘启动时先装载进软件软碟通里
- 再通过菜单栏里的选项进行硬盘映像写入
- 装Ubuntu前不用分区只要留一个压缩卷自由空间出来给它即可

**装Ubuntu 16.04教程参考链接**
[https://blog.csdn.net/leijieZhang/article/details/53704732](https://blog.csdn.net/leijieZhang/article/details/53704732)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20191016185627460.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzE3MjU2Njg5,size_16,color_FFFFFF,t_70)
 **挂载配置及分区方案**
[https://jingyan.baidu.com/article/4d58d5411380dd9dd5e9c07e.html
https://www.jianshu.com/p/0802840f3e1c](https://jingyan.baidu.com/article/4d58d5411380dd9dd5e9c07e.html%20https://www.jianshu.com/p/0802840f3e1c)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20191016185606499.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzE3MjU2Njg5,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20191016185554783.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzE3MjU2Njg5,size_16,color_FFFFFF,t_70)
**Device for boot installation的选择**
[https://blog.csdn.net/weixin_41053564/article/details/85724884](https://jingyan.baidu.com/article/4d58d5411380dd9dd5e9c07e.html%20https://www.jianshu.com/p/0802840f3e1c)

**各模块分配情况，仅供参考，根据个人需求定**
/ 40G，/boot 400M，Swap 4000M，/home 50G，余下的都分配给/usr，总共100G

## 4 第二次重装Win10和Ubuntu双系统总结    
----
时间：2019年6月3日   

**现状**
win7系统正常，Ubuntu在win10下安装后再安装了win7，Ubuntu开机引导被覆盖无法进入。但Ubuntu一直还分区里未删除。

**所需**
WePE制作windows的U盘启动盘、UltraISO软碟通制作Ubuntu的U盘启动盘、Ubuntu16.04映像文件、win10雨林木风ghost一键装机gho文件。

**思路**
- 1）优先从硬盘装，同时复制映像文件到U盘里以便第二手准备从U盘启动。
- 2）装好win10后先用EasyBCD建立引导，看是否能进入原Ubuntu。
- 3）如果不能进入，则将固态硬盘合并，删除原来的ubuntu，固态硬盘中110G全做C盘装系统和软件，再在机械硬盘D盘里装Ubuntu。

下载雨林木风的一键装机ghost系统。
1. ghost文件通过自身系统无法安装。进入wepe，ghost备份还原软件里读不出C盘来，无法恢复。于是U盘启动直接进入硬盘文件里点击ghost安装包里的系统安装器.exe安装。然后等待重启即可，进入系统前改变下boot优先顺序便可启动。
2. 用easyBCD不小心把win10引导项弄没了，倒腾两个多小时，最后利用重装win7自动修复引导，把进入windows的引导问题搞定了。
3. 修复好开机引导后，又重装了一遍win10。
机械硬盘里的逻辑分区转化，直接用diskgenius右键转化为主分区即可，不会损坏文件。

**ubuntu的boot引导挂载点问题**
设置在固态硬盘中，这样就可以gurb方式引导启动，唯一坏处就是ubuntu一旦坏了就得使用windows引导器修复windows引导。但有个好处就是得先装ubuntu再装windows。

**各模块分配情况**
/ 40G，/boot 400M，Swap 4000M，/home 50G，余下的都分配给/usr，总共100G。Ubuntu可直接访问windows下的其他盘所有文件。


## 5 数据恢复
---
想往硬盘里备份数据，结果误删移动硬盘的分区，用DiskGenius软件，找到对应硬盘恢复分区索引表，左上角保存修改，得以恢复回来。

## 6 重装系统后其他问题
---
**【Win10下触摸面板的关闭及显卡驱动重装调节显示屏亮度的方法】**
利用win7的方法一直显示驱动安装失败， 官网搜索UltraNav驱动和intelvideo显卡驱动下载后，直接重新安装即可。
联想Thinkpad E440的win10驱动官方地址：
[https://think.lenovo.com.cn/support/driver/mainpage.aspx#ThinkPad](https://think.lenovo.com.cn/support/driver/mainpage.aspx#ThinkPad)
[https://think.lenovo.com.cn/support/driver/newdriversdownlist.aspx?categoryid=11374&CODEName=ThinkPad%20E440&SearchType=0&wherePage=1&SearchNodeCC=ThinkPad%20E440](https://think.lenovo.com.cn/support/driver/newdriversdownlist.aspx?categoryid=11374&CODEName=ThinkPad%20E440&SearchType=0&wherePage=1&SearchNodeCC=ThinkPad%20E440)

**【装双系统后重装Windows后，Ubuntu启动项不见】**
利用U盘启动从Ubuntu的安装体验系统进去修改原Ubuntu的启动项。
[https://www.jb51.net/os/windows/84561.html](https://www.jb51.net/os/windows/84561.html)
