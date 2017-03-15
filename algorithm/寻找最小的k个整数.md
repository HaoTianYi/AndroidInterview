# 寻找最小的k个整数

[TOC]

## 说明

输入n个整数，输出其中最小的k个。

### 利用排序来解题

要求一个序列中最小的k个数，按照惯有的思维方式，则是先对这个序列从小到大排序，然后输出前面的最小的k个数。

至于选取什么的排序方法，我想你可能会第一时间想到快速排序（我们知道，快速排序平均所费时间为`n*logn`），然后再遍历序列中前k个元素输出即可。因此，总的时间复杂度：`O（n * log n)+O(k)=O（n * log n）`。

例如取前三个数据：

```java
    static int[] ints = {1, 4, 3, 8, 3, 7, 1};

    public static void main(String[] args) {
        bubbleSort(ints);
        for (int i = 0; i < 3; i++) {
            System.out.print(ints[i] + "----");
        }
    }

    /**
     * 冒泡排序
     * 比较相邻的元素。如果第一个比第二个大，就交换他们两个。
     * 对每一对相邻元素作同样的工作，从开始第一对到结尾的最后一对。在这一点，最后的元素应该会是最大的数。
     * 针对所有的元素重复以上的步骤，除了最后一个。
     * 持续每次对越来越少的元素重复上面的步骤，直到没有任何一对数字需要比较。
     *
     * @param numbers 需要排序的整型数组
     */
    public static void bubbleSort(int[] numbers) {
        int temp = 0;
        int size = numbers.length;
        for (int i = 0; i < size - 1; i++) {
            for (int j = 0; j < size - 1 - i; j++) {
                if (numbers[j] > numbers[j + 1])  //交换两数位置
                {
                    temp = numbers[j];
                    numbers[j] = numbers[j + 1];
                    numbers[j + 1] = temp;
                }
            }
        }
    }
```

### 解法二

咱们再进一步想想，题目没有要求最小的k个数有序，也没要求最后n-k个数有序。既然如此，就没有必要对所有元素进行排序。这时，咱们想到了用选择或交换排序，即：

1、遍历n个数，把最先遍历到的k个数存入到大小为k的数组中，假设它们即是最小的k个数；
2、对这k个数，利用选择或交换排序找到这k个元素中的最大值kmax（找最大值需要遍历这k个数，时间复杂度为`O（k）`）；
3、继续遍历剩余n-k个数。假设每一次遍历到的新的元素的值为x，把x与kmax比较：如果`x < kmax` ，用x替换kmax，并回到第二步重新找出k个元素的数组中最大元素kmax‘；如果`x >= kmax`，则继续遍历不更新数组。

每次遍历，更新或不更新数组的所用的时间为`O（k）`或`O（0）`。故整趟下来，时间复杂度为`n*O（k）=O（n*k）`。

> 重点来了，这里没有新建k个单位的数组，而是直接在原数组中去前k个进行循环，节省一个变量

```java
    public static List<Integer> topKV2(List<Integer> list, int k) {
        if (null == list || k > list.size())
            return null;
        int length = list.size();
        if (k == length)
            return list;
        int numI = 0;
        int numJ = 0;
        int temp = 0;
        for (int i = 0; i < k; i++) {
            numI = list.get(i);
            for (int j = i + 1; j < length; j++) {
                numJ = list.get(j);
                if (numJ < numI) {
                    temp = numI;
                    list.set(i, numJ);
                    list.set(j, temp);
                    numI = list.get(i);
                }
            }
        }
        return list.subList(0, k);
    }
```

## 说明

https://github.com/julycoding/The-Art-Of-Programming-By-July/blob/master/ebook/code/java/chapter2/TopK.java

https://github.com/julycoding/The-Art-Of-Programming-By-July/blob/master/ebook/zh/02.01.md

http://www.cnblogs.com/0201zcr/p/4763806.html