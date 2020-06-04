
# 八皇后问题

# 本文在[玩具盒](https://www.hacbox.studio/index.php/%e7%8e%a9%e5%85%b7%e7%9b%92/)同步更新！图片无法阅读请移步至玩具盒！
------------


## 问题背景
> ##### 八皇后问题，一个古老而著名的问题，是回溯算法的典型案例。该问题由国际西洋棋棋手马克斯·贝瑟尔于 1848 年提出：在 8×8 格的国际象棋上摆放八个皇后，使其不能互相攻击，即任意两个皇后都不能处于同一行、同一列或同一斜线上，问有多少种摆法。高斯认为有 76 种方案。1854 年在柏林的象棋杂志上不同的作者发表了 40 种不同的解，后来有人用图论的方法解出 92 种结果。计算机发明后，有多种计算机语言可以编程解决此问题。

![queens](https://bkimg.cdn.bcebos.com/pic/d1a20cf431adcbef8c2f33e5adaf2edda3cc9f3e?x-bce-process=image/watermark,g_7,image_d2F0ZXIvYmFpa2U5Mg==,xp_5,yp_5)

### 问题分析

在8 x 8的棋盘上面放置8个皇后，而且还要不在不同一行和不在同一列，不在同一斜线上，所以每行肯定是得放一个，但是位置就有好多的可能，只要满足上面的要求即可。


设棋盘是一个8 x 8矩阵，皇后i和皇后j的摆放位置分别为（i，Xi）和（j，Xj），要想这些皇后不在同一条斜线上，则需要这两个坐标点的斜率不等于 1 或 - 1。


**也就是满足|Xj —Xi | ≠ |j – i|**


这里采用迭代法解决八皇后问题，迭代就是循环代码中参与运算的变量同时是保存结果的变量，当前保存的结果作为下一次循环计算的初始值。

#### 什么是回溯法

> - 使用回溯法解决问题的过程，实际上是建立一棵“状态树”的过程。例如，在解决列举集合{1,2,3}所有子集的问题中，对于每个元素，都有两种状态，取还是舍，所以构建的状态树为：
> [![树](http://data.biancheng.net/uploads/allimg/170905/2-1FZ5102U5E4.png "树")](http://data.biancheng.net/uploads/allimg/170905/2-1FZ5102U5E4.png "树")
> - 回溯法的求解过程实质上是先序遍历“状态树”的过程。树中每一个叶子结点，都有可能是问题的答案。图 1 中的状态树是满二叉树，得到的叶子结点全部都是问题的解。
> - 在某些情况下，回溯法解决问题的过程中创建的状态树并不都是满二叉树，因为在试探的过程中，有时会发现此种情况下，再往下进行没有意义，所以会放弃这条死路，回溯到上一步。在树中的体现，就是在树的最后一层不是满的，即不是满二叉树，需要自己判断哪些叶子结点代表的是正确的结果。

### 算法思路

八皇后问题是使用回溯法解决的典型案例。算法的解决思路是：

1. 从棋盘的第一行开始，从第一个位置开始，依次判断当前位置是否能够放置皇后，判断的依据为：同该行之前的所有行中皇后的所在位置进行比较，如果在同一列，或者在同一条斜线上（斜线有两条，为正方形的两个对角线），都不符合要求，继续检验后序的位置。
2. 如果该行所有位置都不符合要求，则回溯到前一行，改变皇后的位置，继续试探。
3. 如果试探到最后一行，所有皇后摆放完毕，则直接打印出 8*8 的棋盘。最后一定要记得将棋盘恢复原样，避免影响下一次摆放。

### 源代码

```c
#include <stdio.h>
#include <stdlib.h>
int Queens[8] = {0}, Counts = 0;
int Check(int line, int list)
{
    //遍历该行之前的所有行
    //返回0则代表不能摆放，1代表可以摆放
    for (int index = 0; index < line; index++)
    {
        //挨个取出前面行中皇后所在位置的列坐标
        int data = Queens[index];
        //如果在同一列，该位置不能放
        if (list == data)
        {
            return 0;
        }
        //如果当前位置的斜上方有皇后，在一条斜线上，也不行
        if ((index + data) == (line + list))
        {
            return 0;
        }
        //如果当前位置的斜下方有皇后，在一条斜线上，也不行
        if ((index - data) == (line - list))
        {
            return 0;
        }
    }
    //如果以上都不是，当前位置就可以放
    return 1;
}
//打印结果
void print()
{
    for (int line = 0; line < 8; line++)
    {
        int list;
        for (list = 0; list < Queens[line]; list++)
            printf(" 0");
        printf(" #");
        for (list = Queens[line] + 1; list < 8; list++)
        {
            printf(" 0");
        }
        printf("\n");
    }
    printf("+——+——+——+——+——+——+\n");
}
void eight_queen(int line)
{
    //在数组中为0-7列
    for (int list = 0; list < 8; list++)
    {
        //对于固定的行列，检查是否和之前的皇后位置冲突
        if (Check(line, list))
        {
            //不冲突，以行为下标的数组位置记录列数
            Queens[line] = list;
            //如果最后一样也不冲突，证明为一个正确的摆法
            if (line == 7)
            {
                //统计摆法的Counts加1
                Counts++;
                //输出这个摆法
                print();
                //每次成功，都要将数组重归为0
                Queens[line] = 0;
                return;
            }
            //继续判断下一样皇后的摆法，递归
            eight_queen(line + 1);
            //不管成功失败，该位置都要重新归0，以便重复使用。
            Queens[line] = 0;
        }
    }
}
int main()
{
    //调用回溯函数，参数0表示从第一行开始演算
    eight_queen(0);
    printf("摆放方式有%d种", Counts);
    return 0;
}
```

### 运行结果

![](/wp-content/uploads/2020/06/2-292x300.png) ![](/wp-content/uploads/2020/06/3-209x300.png)

##### 每次结果都会进行输出，用0代替空格棋盘，#代替皇后位置，一共92种摆法
