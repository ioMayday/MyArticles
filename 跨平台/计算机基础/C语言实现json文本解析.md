

# C语言实现json文本解析

[toc]

## 背景

----

项目中，有时需要根据外部输入条件，进行程序的灵活切换，运用JSON文件解析，能高效的替换指令行输入，且直观易懂。

## 总体方法

----

**JSON文件准备**

- JSON数据
  - test_data.json
- 自写调度
  - json_parser.c
  - json_parser.h
- 库调用
  - cJSON.c，下载链接：[link](https://github.com/DaveGamble/cJSON/blob/master/cJSON.c)
  - cJSON.h，下载链接：[link](https://github.com/DaveGamble/cJSON/blob/master/cJSON.h)

**DEMO思路**

- 获取json文件大小，JsonGetFileSize
- 全部读入char字符数组，JsonConfigLoader
- 调用cJSON.c中的函数进行对象解析并输出打印，Parser




取自库调用作者的待解析的json数据文件：`test_data.json`

```c
{
    "name": "Awesome 4K",
    "resolutions": [
        {
            "width": 1280,
            "height": 720
        },
        {
            "width": 1920,
            "height": 1080
        },
        {
            "width": 3840,
            "height": 2160
        }
    ]
}
```

注意读取第二个对象resolutions时，内部嵌套有列表需要先获取分辨率对象后，再用列表读取获取子列表项，最后再读取子列表项中的对象元素。

调用JsonPaserDemo函数，**输出效果**：

```
------------------------------- 
      _  _____  ____  _   _
     | |/ ____|/ __ \| \ | |
     | | (___ | |  | |  \| |
 _   | |\___ \| |  | | \  |
| |__| |____) | |__| | |\  |
 \____/|_____/ \____/|_| \_|
-------------------------------
name is Awesome 4k
width=3840
height=2160
```



## 实现源码

-----

文件：`json_parser.h`

```c
#ifndef JSON_TEST_H
#define JSON_TEST_H

#include "cJSON.h"

#ifdef __cplusplus
extern "C" {
#endif

void JsonPaserDemo(void);

#ifdef __cplusplus
}
#endif

#endif
```

文件：`json_parser.c`

```c
#include "stdio.h"
#include "stdlib.h"
#include "string.h"
#include "cJSON.h"
#include "json_parser.h"

static void JsonBanner(void)
{
    char *banner = "\
------------------------------- \n\
      _  _____  ____  _   _     \n\
     | |/ ____|/ __ \\| \\ | |  \n\
     | | (___ | |  | |  \\| |   \n\
 _   | |\\___ \\| |  | | \\  |  \n\
| |__| |____) | |__| | |\\  |   \n\
 \\____/|_____/ \\____/|_| \\_| \n\
------------------------------- \n";
    printf("%s", banner);
}

static int JsonGetFileSize(FILE *fp)
{
    fseek(fp, 0, SEEK_END);
    int fsize = ftell(fp);
    return fsize;
}

static void JsonConfigLoader(FILE *fp, char *data, int fsize)
{
    rewind(fp);
    fsize = fread(data, 1, fsize, fp);
    data[fsize] = '\0';
    return;
}

static int Parser(char *data)
{
    int ret = 0;
    cJSON *json = cJSON_Parse(data);
    if (json == NULL) {
        return -1; // json malloc failed
    }
    cJSON *node = NULL;
    cJSON *subNode = NULL;
    cJSON *tmpNode = NULL;

    node = cJSON_GetObjectItem(json, "name");
    if(strcmp(node->valuestring, "Awesome 4K") == 0){
        printf("name is Awesome 4K\n");;
    } else {
        printf("name not Awesome 4K\n");
    }

    node = cJSON_GetObjectItem(json, "resolutions");
    int resMode = 2;
    subNode = cJSON_GetArrayItem(node, resMode);
    tmpNode = cJSON_GetObjectItem(subNode, "width");
    printf("width=%d\n", tmpNode->valueint);
    tmpNode = cJSON_GetObjectItem(subNode, "height");
    printf("height=%d\n", tmpNode->valueint);

    free(json);
    return ret;
}


#define FILE_PATH "D:\\test_data.json"
void JsonPaserDemo(void)
{
    JsonBanner();

    FILE *fp = fopen(FILE_PATH, "rb");
    if (fp == NULL) {
        printf("file open failed\n");
        fclose(fp);
    }

    int fsize = JsonGetFileSize(fp);
    if (fsize < 0) {
        printf("size < 0\n");
        fclose(fp);
        return;
    }

    char *data = (char *)malloc((fsize + 1) * sizeof(char));
    if (data == NULL) {
        printf("malloc data failed\n");
        fclose(fp);
        free(data);
        data = NULL;
        return;
    }

    JsonConfigLoader(fp, data, fsize);
    int ret = Parser(data);
    if (ret != 0) {
        printf("ParserLoad failed, ret=%d\n", ret);
        fclose(fp);
        free(data);
        data = NULL;
        return;
    }
    fclose(fp);
    free(data);
    data = NULL;

    return;
}
```



## 相关资料

----

1. cJSON使用说明，[link](https://github.com/DaveGamble/cJSON)
2. figlet：ASCII 艺术字生成器，大字符大文字，[link](https://helloacm.com/figlet/?url=JSON)