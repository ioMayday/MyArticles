**Ŀ¼**
[TOC]

���⣺Cѧϰ����extern�����˽������Ͷ��������
> ������Դ�ڣ���ͷ�ļ���extern����ȫ�ֱ��������뱨�����¸�����������ͷ�����˵��extern���÷����Լ�������������Ͷ��������


## 1 ����
----
��a.h�ж����˽ṹ���������£�

```c
typedef struct igf_enc_instance_struct
{
    IGF_ENC_PRIVATE_DATA  igfData;
    int                   infoSamplingRate;
    int                   infoStartFrequency;
    int                   infoStopFrequency;
    int                   infoStartLine;
    int                   infoStopLine;
    int                   infoFrameCount;
    int                   flatteningTrigger;
} IGF_ENC_INSTANCE, *IGF_ENC_INSTANCE_HANDLE;
```

��ͬʱ������һ��ȫ�ֱ�����

```c
extern IGF_ENC_INSTANCE g_igfInstance;
```

Ȼ����b.c�а���a.hͷ�ļ������������£�
```c
/* Initialize IGF */
memset(&g_igfInstance, 0, sizeof(g_igfInstance));
g_igfInstance.infoStopFrequency = -1;
// ��־λ�����IGF��  
IGFEncSetMode(&g_igfInstance, st->paramInter.bitRate, 2, 0);
```

## 2 ��������
----

��ʾb.c��`undefined reference to 'g_igfInstance' `

## 3 ԭ�����
----

`g_igfInstance`��`a.h`��ֻ�Ǳ������ˣ���ʵ��û�ж��壬Ҳû�з����ڴ棨������Ϊ�����ˣ����������Ӧ����`a.c`�ж��壨��ȫ�ֱ�������������Ƿ񸳳�ֵ������ȫ�ֱ���Ĭ��ָ��Ϊ`NULL`��ֵΪ`0`������`a.h`������ extern��

**С�᣺**

 - �������������������**����**����**���������ڴ����Ĳ���**
-  **�����ǻ��׶�**�ĸ���
-  **���������ӽ׶�**�ĸ�����߰�����������ģ�飬����ʱҪ������ģ��Ѱ���ⲿ�����ͱ���
- **���������.c�ļ���ʵ�֣�������.h��ʵ��**��������c�ļ��ظ�������ͷ�ļ�ʱ���ᱨ���ظ�����
- c�ļ���include ͷ�ļ���**����ֻ�ǰ�ͷ�ļ��������һ��չ��**��ֻ���ַ����滻����

`������������������`���������Ӱ�����⣺

```c
extern int a;		//����һ��ȫ�ֱ���a
int a;      		//����һ��ȫ�ֱ���a
extern int a = 0;	//����һ��ȫ�ֱ���a ������ֵ
int a = 0;			//����һ��ȫ�ֱ���a ������ֵ
```

## 3 ���
----
a.c�ж��壬`IGF_ENC_INSTANCE g_igfInstance;`
a.h��������`extern IGF_ENC_INSTANCE g_igfInstance;`
b.c����������a.h��`include "a.h"`


## 4 �ο�����
----

1. [extern ��ͷ�ļ�(*.h)���������ϵ](https://www.runoob.com/w3cnote/extern-head-h-different.html)


