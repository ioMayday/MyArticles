@[TOC](Ubuntu��Win10˫ϵͳ��װָ��)

**Ŀ¼**
[toc]

���⣺Ubuntu��Win10˫ϵͳ��װָ��

> **����**��
> 1) Ubuntu16.04��װ�� 
> 2) Windows��װ�� 
> 3) U�� 
> 4) DiskGenius 
> 5) WePE 
> 6) Ultraiso�����ͨ�� 
> 7) �����


����������Thinkpad�ʼǱ� E440Ϊ�����н��⡣


## 1 ����������
---
**�ƶ�Ӳ����������**

 - ��ɾ������������������ٿ��ٷ�������ʽ��
 - �ٵ��ɾ���������ݳ����������
 - �ٰ�Guid�ĳ���MBR�������Ϳ��������ƶ�Ӳ�������������ˡ�
 - ���ƶ�Ӳ���и����⣬������ʶ��ò��Ǻܺã���ʱ�޷�ʶ��

**U����������**
- ֱ������WEPE��Ҫ���¼��װһ��΢�͵�win10ϵͳ
- Ȼ��ϵͳӳ���ļ�װ�ڷ�ϵͳ��Ŀ¼�¡�

**�沿��װϵͳ�̳�����**
[http://bbs.chongbuluo.com/thread-5195-1-1.html](http://bbs.chongbuluo.com/thread-5195-1-1.html)

**����ľ����汾ϵͳ����**
[http://www.ylmf888.com/win10/](http://www.ylmf888.com/win10/)

## 2 װ Win7 ϵͳ

----

**װϵͳ�ر�֪ʶ**
1.	����ʱ Thinkpad ������F1����BIOS���棬��ͬƷ�ƿ�ݼ���ͬ
2.	��Startup��Bootѡ���� ������������˳��
3.	����U��������

**U����װϵͳ����**
1. ����U��������
2. �ı�Boot��������˳�� U������
3. ������������װϵͳ������
4. ����ϵͳ

**��U����װWin7ϵͳ��**
1. ��win7iso�����ļ�������ϵͳ���ļ�����
2. ����΢PE������ 
3. ��F1����Bios Boot��������ѡ��USB HDD����Ϊ��λ
4. ��������PE��CGI��ԭ����
5. �ҵ���Ӧ�����ļ����ɲ����Զ���װ
CGI���ݻ�ԭWin7����װֻҪ�������ӣ���̬Ӳ�̾��ǿ졣

**����װWin7ϵͳ������һֱͣ������������ҳ�����취��**
����Windows7����ȫ֧��UEFI����ҪCSM(Compatibility Support Module)֧�֣�Ҳ����Legacy BIOS��֧�֡����������µķ�����
1. ������F1����BIOS����Security��secure boot ѡ��disabled���ɽ������ʱҪ����ǩ�������⣩
2. ��Startup �ｫUEFI Boot��Ϊboth������Legacy��
3. ��CSM Support����ΪYes��
4. ���ݻ�ԭ��Ӱ���ϵͳ�̵��ļ������Է���һ�����뽫��Ҫ�ļ����ݡ� 
5. ϵͳ��Office2016���ʹ�ü���������ɡ�
6. Win10��װ������ͬWin7��ͨ��U�������̣���WinPEϵͳ�еı��ݻ�ԭ����װ���񼴿ɡ�


## 3 װ Ubuntu 16.04 ϵͳ
-----
�������£�
- Ultraiso��U������ʱ��װ�ؽ�������ͨ��
- ��ͨ���˵������ѡ�����Ӳ��ӳ��д��
- װUbuntuǰ���÷���ֻҪ��һ��ѹ�������ɿռ������������

**װUbuntu 16.04�̳̲ο�����**
[https://blog.csdn.net/leijieZhang/article/details/53704732](https://blog.csdn.net/leijieZhang/article/details/53704732)
![���������ͼƬ����](https://img-blog.csdnimg.cn/20191016185627460.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzE3MjU2Njg5,size_16,color_FFFFFF,t_70)
 **�������ü���������**
[https://jingyan.baidu.com/article/4d58d5411380dd9dd5e9c07e.html
https://www.jianshu.com/p/0802840f3e1c](https://jingyan.baidu.com/article/4d58d5411380dd9dd5e9c07e.html%20https://www.jianshu.com/p/0802840f3e1c)
![���������ͼƬ����](https://img-blog.csdnimg.cn/20191016185606499.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzE3MjU2Njg5,size_16,color_FFFFFF,t_70)
![���������ͼƬ����](https://img-blog.csdnimg.cn/20191016185554783.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzE3MjU2Njg5,size_16,color_FFFFFF,t_70)
**Device for boot installation��ѡ��**
[https://blog.csdn.net/weixin_41053564/article/details/85724884](https://jingyan.baidu.com/article/4d58d5411380dd9dd5e9c07e.html%20https://www.jianshu.com/p/0802840f3e1c)

**��ģ���������������ο������ݸ�������**
/ 40G��/boot 400M��Swap 4000M��/home 50G�����µĶ������/usr���ܹ�100G

## 4 �ڶ�����װWin10��Ubuntu˫ϵͳ�ܽ�    
----
ʱ�䣺2019��6��3��   

**��״**
win7ϵͳ������Ubuntu��win10�°�װ���ٰ�װ��win7��Ubuntu���������������޷����롣��Ubuntuһֱ��������δɾ����

**����**
WePE����windows��U�������̡�UltraISO���ͨ����Ubuntu��U�������̡�Ubuntu16.04ӳ���ļ���win10����ľ��ghostһ��װ��gho�ļ���

**˼·**
- 1�����ȴ�Ӳ��װ��ͬʱ����ӳ���ļ���U�����Ա�ڶ���׼����U��������
- 2��װ��win10������EasyBCD�������������Ƿ��ܽ���ԭUbuntu��
- 3��������ܽ��룬�򽫹�̬Ӳ�̺ϲ���ɾ��ԭ����ubuntu����̬Ӳ����110Gȫ��C��װϵͳ����������ڻ�еӲ��D����װUbuntu��

��������ľ���һ��װ��ghostϵͳ��
1. ghost�ļ�ͨ������ϵͳ�޷���װ������wepe��ghost���ݻ�ԭ����������C�������޷��ָ�������U������ֱ�ӽ���Ӳ���ļ�����ghost��װ�����ϵͳ��װ��.exe��װ��Ȼ��ȴ��������ɣ�����ϵͳǰ�ı���boot����˳����������
2. ��easyBCD��С�İ�win10������Ūû�ˣ�����������Сʱ�����������װwin7�Զ��޸��������ѽ���windows����������㶨�ˡ�
3. �޸��ÿ�������������װ��һ��win10��
��еӲ������߼�����ת����ֱ����diskgenius�Ҽ�ת��Ϊ���������ɣ��������ļ���

**ubuntu��boot�������ص�����**
�����ڹ�̬Ӳ���У������Ϳ���gurb��ʽ����������Ψһ��������ubuntuһ�����˾͵�ʹ��windows�������޸�windows���������и��ô����ǵ���װubuntu��װwindows��

**��ģ��������**
/ 40G��/boot 400M��Swap 4000M��/home 50G�����µĶ������/usr���ܹ�100G��Ubuntu��ֱ�ӷ���windows�µ������������ļ���


## 5 ���ݻָ�
---
����Ӳ���ﱸ�����ݣ������ɾ�ƶ�Ӳ�̵ķ�������DiskGenius������ҵ���ӦӲ�ָ̻��������������ϽǱ����޸ģ����Իָ�������

## 6 ��װϵͳ����������
---
**��Win10�´������Ĺرռ��Կ�������װ������ʾ�����ȵķ�����**
����win7�ķ���һֱ��ʾ������װʧ�ܣ� ��������UltraNav������intelvideo�Կ��������غ�ֱ�����°�װ���ɡ�
����Thinkpad E440��win10�����ٷ���ַ��
[https://think.lenovo.com.cn/support/driver/mainpage.aspx#ThinkPad](https://think.lenovo.com.cn/support/driver/mainpage.aspx#ThinkPad)
[https://think.lenovo.com.cn/support/driver/newdriversdownlist.aspx?categoryid=11374&CODEName=ThinkPad%20E440&SearchType=0&wherePage=1&SearchNodeCC=ThinkPad%20E440](https://think.lenovo.com.cn/support/driver/newdriversdownlist.aspx?categoryid=11374&CODEName=ThinkPad%20E440&SearchType=0&wherePage=1&SearchNodeCC=ThinkPad%20E440)

**��װ˫ϵͳ����װWindows��Ubuntu���������**
����U��������Ubuntu�İ�װ����ϵͳ��ȥ�޸�ԭUbuntu�������
[https://www.jb51.net/os/windows/84561.html](https://www.jb51.net/os/windows/84561.html)
