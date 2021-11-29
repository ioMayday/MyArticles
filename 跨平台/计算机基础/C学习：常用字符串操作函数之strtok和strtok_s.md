**Ŀ¼**
[toc]


���⣺Cѧϰ�������ַ�����������֮strtok��strtok_s

## ����˵��
---
**ͷ�ļ�����**

```c
#include <string.h>
```

**����ǩ��**

```c
char * strtok(char *s, const char *delim);
```

**��������**

 - ���ָ���и�Ŀ���ַ���
 - ���طָ�����ಿ���ַ�����ָ��

**����˵��**

 - �����������и���**����ַ����׵�ַ**��ֱ���ָ���ϻ��޷��ָ�ʱ������`NULL`��
 - ��һ�ε�����Ҫ�����һ�����ָ��`s`���ҵ�һ���ָ���ָ���������飬��������ַ����׵�ַ��
 - �ڶ��ε���ʱ����һ������������Ϊ`NULL`��
 - ������ı�������ַ�����**�ָ��ַ������ж��**�������ǽ�`s`�к���`delim`��Ŀ��ָ��ַ����ַ����滻Ϊ`\0`��
 - �ָ����ò���`const`�����ɲ��ɸı���ַ������磺`const char *delim = ":";`  �� `const char split[2] = ","`��
 - `strtok()`�ڲ��漰��`malloc()`��ÿ�ε��ý����ͬ��Ϊ�������뺯����
 - �жϵ�ǰ�ָ���ʹ���µķָ����и�ʱ�������ǣ����´��������ָ��ͷָ��ַ���������`strtok(newStr, newSplit)`���ٴε��÷ָ�ʱ`strtok(NULL, newSplit)`��


##  ʹ��ʾ��
---
```c
#include <string.h>
void main(void){
    char s[] = "ab-cd : ef;gh :i-jkl;mnop;qrs-tu: vwx-y;z";
    char *delim = "-: ;";
    char *p;
    printf("%s\n", strtok(s, delim));
    while((p = strtok(NULL, delim)))
        printf("%s\n", p);
    return;
}
```
�и�����Ч��Ϊ��

> ab
cd
ef
gh
i
jkl
mnop
qrs
tu
vwx
y
z



##  ��ȫ����ʹ��strtok_s()
---
����`strtok()`�������ڶ��̲߳�������Ϊ�þ�̬��������ָ��¸��ַ����׵�ַ�����ڲ��ж�̬�����ڴ棬���������������İ�ȫ����`strtok_s()`��

**ע�⣺**`linux`ƽ̨�¶�Ӧ�Ľӿ���Ϊ`strtok_r()`���÷�һ�£�����ʹ�÷������£�

```c
// strtok_s�÷�����
char str[] = "a.b.c/d.e,ee,eee";
const char demlit[] = ",./";

char *context = NULL;  // contextָ�뱣��ָ����һ���ַ����׵�ַ
char *p = str;
p = strtok_s(p, demlit, &context); // &contextΪ����ָ�룬���ڴ洢һ��ָ��
while (p != NULL) {
    printf("%s\n", p);
    p = strtok_s(NULL, demlit, &context);
}
```
�и�����Ч��Ϊ��    

> a
> b
> c
> d
> e
> ee
> eee

**��ͬһ���ַ����Ⱥ�ʹ�����ַָ��**���Ķ����£�

```c
// strtok_s�÷�����
char str[] = "a.b.c/d.e,ee,eee";
const char demlit1[] = "/";
const char demlit2[] = ",";

char *context = NULL;  // contextָ�뱣��ָ����һ���ַ����׵�ַ
char *p = str;
p = strtok_s(p, demlit1, &context); // &contextΪ����ָ�룬���ڴ洢һ��ָ��
printf("%s\n", p);

p = context; // �ַ�����ͷ��Ϊ/���d.e...��ָ��d
p = strtok_s(p, demlit2, &context); // ��Ϊ�µķָ�����ַ�������ʼ��ַ
while (p != NULL) {
    printf("%s\n", p);
    p = strtok_s(NULL, demlit2, &context);
}
```
�и�����Ч��Ϊ��

> a.b.c
> d.e
> ee
> eee

## ��չ�Ķ�
-----
[1��C����strtok()�������ַ����ָ�](http://c.biancheng.net/cpp/html/175.html)
[2������̳̣�C �⺯�� - strtok()](https://www.runoob.com/cprogramming/c-function-strtok.html)
