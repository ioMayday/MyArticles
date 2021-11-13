
**Ŀ¼**
[toc]

> ���ߣ���֪��
> ���ںţ���֪��
> ��飺������˶ʿ����Ϊ�㷨����ʦ��רע������顢ְ������������������������


���⣺Cѧϰ�����������������


## ����
----
��ͬ���������ڻ������ʱ��ʱ�������������������ǿ��ת���Ĳ�����C������������������ͼ��
![���������ͼƬ����](https://img-blog.csdnimg.cn/20201108151854178.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzE3MjU2Njg5,size_16,color_FFFFFF,t_70#pic_center)

## ˵��
-----

 - ��short����char�������ݽ��л������ʱ��ͳһ����ת��int���ͣ�������ֵ��������������ת��������һ�㲻����������߽ض�����
 - float/double����������ת��ʱ��ֱ�ӽض�ȡ����ȥ��С���������ݡ�

## ��ϰ
----
### ��Ŀ1

��������if�жϽ����Σ�

```c
unsigned int a = 20;
int b = 13;
int k = b - a;
if (k > b + a) {
    printf("k > b + a   Ok\n");
}
if (k == b - a) {
    printf("k == b - a   Ok");
}
int b = 13;
```
**�𰸣�**
����if�����Ϊ�档

**������**
 - k�Ǵ��-7��b - aʱ��b����Ϊunsigned������a������õ�����һ���ϴ��unsigned����֮�󸳸�k��ǿתΪint��ֵΪ-7
 - k > b + a �Ƚ�ʱ��������������Ϊ`unsigned`��k��ĸ�����������Ϊһ���ܴ���������϶����� `b + a` �Ľ��33
 - k == b - a ���߶��Ǵ�Ľϴ��unsigned��������int������Ϊ-7
 - int���͵�-7�ڼ�����洢��ֵΪ `0xfff9`���������Ϊ�з�������Ϊ-7������Ϊ�޷�������Ϊ`65529`
 - int����Ĭ��Ϊ`signed int`

	
### ��Ŀ2
�����������Ľ����Ϊ���٣�
```c
unsigned int t = -1;
//�Ͼ�ȼ���
//unsigned int t = 0xffffffff;
unsigned int a = 10;
int b = -13;
int c = a + b;

printf("%d\n", t); 
printf("%ud\n", (a+b));  
printf("%d\n", (a+b));  
printf("%d\n", c);
```

**�𰸣�**
```c
-1            // unsigned int תint����չ����signed����Ϊ-1
4294967293d   // d˵������ʮ���Ʊ�ʾ��
-3
-3
```

**������**

```c
printf("%d\n", (a+b));
printf("%ud\n", (a+b));  
```
 - %d����ʾĬ�ϰ�signed int��������������ұ߱�����short/char�������޷��ţ���󶼻���չΪsigned int
 - %ud�����ʾ���ұ߱���ǿ�ư��޷�������unsigned int���
 - %ld�����ʾ��long int�������

### ��Ŀ3
�����������Ľ���ֱ���?
```c
signed short i0 = 65535; // 0xffff
unsigned short i1 = 65535; 
printf("%d \n", i0);
printf("%d \n", i1);
```

**�𰸣�**
```c
-1
65535
```

**������**

 - �������з��Ż����޷��ŵ�short���ͣ�%d���ʱ��ͳһ���ǰ�signed int�����
 - ���Ե�һ��0xffff������λ��չ������չΪ0xffffffff�����-1��
   �ڶ���0xffff�����޷���λ��ʽ��չ������չΪ0x0000ffff�����65535.


### ��Ŀ4
�����������Ľ���ֱ���?
```c
signed char i0 = 255; // 0xff
unsigned char i1 = 255; 
printf("%d \n", i0);
printf("%d \n", i1);
```
**�𰸣�**
```c
-1
255
```
**������**
���Ƶģ�255�Ĵ洢ֵ��0xff����signed char��ʾ��-1��������չ��intʱ����Ϊ�Ǹ�������ȫ���1����unsigned char��չʱ���޷���λȫ���0�����԰�signed int���ʱ��255.

�����������Ľ���ֱ���?
```c
signed char i0 = 0xff;
unsigned char i1 = 0xff;  
// ������������һ��ȼ�
// unsigned char i1 = -1;   // ��Ϊ-1ʵ�ʴ洢ֵ����0xff
// unsigned char i1 = 255;
printf("%d \n", i0);
printf("%d \n", i1);
```
**��**
```c
-1
255
```
**������**
0xff���Խ���Ϊ-1��Ҳ���Խ���Ϊ255�����忴���������Ƿ�Ϊ�з�������
