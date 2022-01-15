**目录**
[toc]



标题：C学习：动态申请二维数组的方法

> 附：[C代码在线调试工具](https://c.runoob.com/compile/11)

一般二维数组初始化时维数都是常量。**所谓动态申请二维数组**，即在某些场合，需要`在程序运行阶段才能知道二维数组的行数和列数`，从而在堆中动态分配内存给二维数组，此时维数为变量。

## 二维数组动态申请
-----

以下实现思路也可以推广至动态申请三维、四维数组等。**另外注意**使用完数组后，不需要该数组时记得及时释放对应内存，话不多说，直接上代码：

```c
// 动态申请二维数组空间的方法
int main(void)
{
    int a[5][3] = {{1,4,-1},{3,2,3},{3,3,-2},{1,1,1},{4,0,1}};

    int row = 2;
    int col = 2;

    // 申请指向行首指针的二级指针空间
    int **arr2 = (int **)malloc(row * sizeof(int**));
    int i, j;
    for (i = 0; i < row; i++) { 
        // 得到行首指针，注意相邻行的内存空间不一定连续
        // 但同一行内部元素的内存空间是连续的
        arr2[i] = (int*)malloc(col * sizeof(int));
    }

    // 录入数据
    for (i = 0; i < row; i++) {
            for (j = 0; j < col; j++) {
            // arr2[i][j] = a[i][j];  // 快速录入预设数据
            int ret = scanf("%d", &arr2[i][j]);
            if (ret != 1) { return -1; }
        }
    }

    // 打印输入的数组
    for (i = 0; i < row; i++) {
        for (j = 0; j < col; j++) {
            printf("%d ", arr2[i][j]);
        }
        printf("\n");
    }

    // 释放对应内存
    for (i = 0; i < row; i++) { 
        free(arr2[i]);
    }
    free(arr2);
   
    return 0;
}
```

## 三维数组动态申请
-----

**扩展深入**，以下给出动态申请三维数组并初始化的方法，三维数组实际应用不多，仅供参考加深理解，欢迎读者自行实现：

```c
// 动态申请三维数组空间，三维数组用的不多
int main(void)
{
    int a[2][5][3] = {{{1,4,-1},{3,2,3},{3,3,-2},{1,1,1},{4,0,1}},
                      {{2,3,4},{5,6,7},{3,3,-2},{1,2,1},{4,5,1}} };

    int num = 2; // num of 2D matrix
    int row = 5;
    int col = 3;

    // 申请指向不同二维数组指针的三级指针空间和对应二维数组空间
    int ***arr3 = (int ***)malloc(num * sizeof(int***)); 
    int i, j;
    for (i = 0; i < num; i++) { 
        arr3[i] = (int **)malloc(row * sizeof(int**));
        for (j = 0; j < row; j++) {
            arr3[i][j] = (int*)malloc(col * sizeof(int));
        }
    }

    // 录入数据
    int k;
    for (i = 0; i < num; i++) {
            for (j = 0; j < row; j++) {
                for (k = 0; k < col; k++) {
                    arr3[i][j][k] = a[i][j][k];  // 快速录入预设数据
                    // int ret = scanf("%d", &arr3[i][j][k]);
                    // if (ret != 1) { return -1; }
                }
        }
    }

    // 打印输入的数组
    for (i = 0; i < num; i++) {
        printf("2D Matirx Num: %d\n", i + 1);
        for (j = 0; j < row; j++) {
                for (k = 0; k < col; k++) {
                    printf("%d ", arr3[i][j][k]);
                }
                printf("\n");
        }
    }

    // 释放对应内存
    for (i = 0; i < num; i++) { 
        for (j = 0; j < row; j++) {
            free(arr3[i][j]);
        }
        free(arr3[i]);
    }
    free(arr3);

    return 0;
}
```
