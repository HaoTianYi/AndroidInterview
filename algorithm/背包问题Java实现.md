# 背包问题Java实现

[TOC]

## 描述

小偷在屋子里偷东西，他带着一只背包。屋子里物品数量有限——每件物品都具有一定的重量和价值——珠宝重量轻但价值高，桌子重但价值低。最重要的是小偷背包容量有限。很明显，他不能把桌子分成两份或者带走珠宝的3/4。对于一件物品他只能选择带走或者不带走。

那么问题就是小偷怎样偷才能获得最大价值的商品？

## 分析

有如下的商品价格和重量：

|  编号  | 重量W  | 价格V  |
| :--: | :--: | :--: |
|  1   |  3   |  4   |
|  2   |  4   |  5   |
|  3   |  5   |  6   |

在商店中有上面3件商品，然后用b（k,w）来表示小偷能获得的最大价值

> k代表的是第一个商品
>
> w代表的背包剩余的空间

那么问题就变成求b(K,W)的最大值的问题，而小偷对待每一个商品首先有一个问题：背包剩余的空间能不能在装下这个商品，不能的话`b(k,w)=b(k-1,w)`；当能偷（不一定偷）的时候又判断怎么样才能最大值，那么有下图：

![snipaste_20170315_150935](http://oaxelf1sk.bkt.clouddn.com/snipaste_20170315_150935.png)

现在开始从头分析：

我们首先面对的是B(3,10)，对待商品3，我们选择偷还是不偷,当然偷啊

那么问题就变成了求B（2,5）+6的最大值问题，然后再判断B(2,5)的最大值，依次递归，求得最大值

> 核心算法应该是求上面的B(max)

## 具体实现

我们刚刚是从最大空间到最小空间，写代码的时候可以从最小到最大空间，可以得到这样一个表格：

![snipaste_20170315_152048](http://oaxelf1sk.bkt.clouddn.com/snipaste_20170315_152048.png)

x轴表示某一个商品，0就是代表没有商品，y轴代表的是最大容纳的重量

可以简单的得到，商品数量为0或者是剩余的重量是0时候，B的值都是0，而且只有第一件商品的时候，B的值都是4

![snipaste_20170315_152434](http://oaxelf1sk.bkt.clouddn.com/snipaste_20170315_152434.png)


### 完整代码

```java
    public static void main(String[] args) {
        int maxW = 10;
        int number = 3;
        int w[] = {3, 4, 5};
        int v[] = {4, 5, 6};
        int[][] b = backPackSolution(maxW, number, w, v);
        for (int i = 0; i <= number; i++) {
            for (int j = 0; j <= maxW; j++) {
                System.out.print(b[i][j]+"\t");
            }
            System.out.println();
        }
    }

    private static int[][] backPackSolution(int maxW, int number, int[] w, int[] v) {
//      没有空间或者商品数是0的时候多出1
        int b[][] = new int[number + 1][maxW + 1];
//      相当于遍历商品
        for (int i = 1; i < number + 1; i++) {
//          j代表剩余的空间
            for (int j = 1; j < maxW + 1; j++) {
//              这件商品和剩余空间进行比较
                if (w[i - 1] < j) {
//                    小于表示可以偷，现在决定怎么偷
                    if (b[i - 1][j] < (b[i - 1][j - w[i - 1]] + v[i - 1])) {
                        b[i][j] = b[i - 1][j - w[i - 1]] + v[i - 1];
                    } else {
                        b[i][j] = b[i - 1][j];
                    }
                } else {
//                  不偷了
                    b[i][j] = b[i - 1][j];
                }
            }
        }
        return b;
    }
```

打印可得下面的数据：

0	0	0	0	0	0	0	0	0	0	0	
0	0	0	0	4	4	4	4	4	4	4	
0	0	0	0	4	5	5	5	9	9	9	
0	0	0	0	4	5	6	6	9	10	11



## 参考

http://www.tudou.com/listplay/Z-bWgybEtig/XaOy3xM2klU.html

http://www.importnew.com/13072.html

http://www.cnblogs.com/lfeng1205/p/5981198.html

http://blog.csdn.net/fg2006/article/details/6766384?reload
