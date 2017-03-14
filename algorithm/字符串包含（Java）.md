# 字符串包含（Java）

[TOC]

## 题目

给定两个分别由字母组成的字符串A和字符串B，字符串B的长度比字符串A短。请问，如何最快地判断字符串B中所有字母是否都在字符串A里？

为了简单起见，我们规定输入的字符串只包含大写英文字母，请实现函数bool StringContains(string &A, string &B)

比如，如果是下面两个字符串：

String 1：ABCD

String 2：BAD

答案是true，即String2里的字母在String1里也都有，或者说String2是String1的真子集。

如果是下面两个字符串：

String 1：ABCD

String 2：BCE

答案是false，因为字符串String2里的E字母不在字符串String1里。

同时，如果string1：ABCD，string 2：AA，同样返回true。

## 解释

思路一：遍历字符串B，判断每一个字符是否出现在字符串A中，时间复杂度O(n*m)，空间复杂度O(1)

增加一个数据，用来记录光标的移动

```java
    public static void main(String[] args) {
        System.out.println(con(new char[]{'A', 'B', 'C'}, new char[]{'B'}));
    }

    public static boolean con(char[] charA, char[] charB) {
        for (int i = 0; i < charB.length; i++) {
            int index;
            for (index = 0; (index < charA.length) && (charA[index] != charB[i]); index++) {

            }
            if (index>=charA.length){
                return false;
            }
        }
        return true;
    }
```

思路二：先对两个字符串排序，然后同时遍历字符串A和B，判断B中的每一个字符是否都在字符串A中。时间复杂度O(nlogn)，空间复杂度O(1)

这次增加两个光标，用于指定两个char类型的数组：

```java
    public static void main(String[] args) {
        char[] charA = {'D', 'A', 'C'};
        char[] charB = {'D', 'C'};
        sort(charA);
        sort(charB);
        System.out.println(con(charA, charB));
    }

    public static boolean con(char[] charA, char[] charB) {
        for (int a = 0, b = 0; b < charB.length; ) {
            while ((a < charA.length) && (charA[a] < charA[b])) {
                ++a;
            }
            if ((a>charA.length)||(charA[a]>charB[b])){
                return false;
            }
            ++b;
        }
        return true;
    }
     /**
     * 选择排序算法
     * 在未排序序列中找到最小元素，存放到排序序列的起始位置
     * 再从剩余未排序元素中继续寻找最小元素，然后放到排序序列第二个位置。
     * 以此类推，直到所有元素均排序完毕。
     */
    public static void sort(char[] chars) {
        char temp;
        for (int i = 0; i < chars.length; i++) {
            int min = i;
            for (int j = i + 1; j < chars.length; j++) {
                if (chars[min] > chars[j]) {
                    min = j;
                }
            }
            temp = chars[i];
            chars[i] = chars[min];
            chars[min] = temp;
        }
    }
```

思路三：将每一个字符映射到一个素数上，对字符串A中的每一个字符表示的素数，求累积；然后遍历字符串B，用每一个字符表示的素数去除字符串A的累积，判断余数是否为0。时间复杂度：O(n)，空间复杂度O(1)。可能存在的问题：乘积时可能会溢出。

假设有一个仅由字母组成字串，让每个字母与一个素数对应，从2开始，往后类推，A对应2，B对应3，C对应5，......。遍历第一个字串，把每个字母对应素数相乘。最终会得到一个整数。

利用上面字母和素数的对应关系，对应第二个字符串中的字母，然后轮询，用每个字母对应的素数除前面得到的整数。如果结果有余数，说明结果为false。如果整个过程中没有余数，则说明第二个字符串是第一个的子集了（判断是不是真子集，可以比较两个字符串对应的素数乘积，若相等则不是真子集）。

思路总结如下：

1. 按照从小到大的顺序，用26个素数分别与字符'A'到'Z'一一对应。
2. 遍历长字符串，求得每个字符对应素数的乘积。
3. 遍历短字符串，判断乘积能否被短字符串中的字符对应的素数整除。
4. 输出结果。

如前所述，算法的时间复杂度为O(m+n)的最好的情况为O(n)（遍历短的字符串的第一个数，与长字符串素数的乘积相除，即出现余数，便可退出程序，返回false），n为长字串的长度，空间复杂度为O(1)。

```java
   public static void main(String[] args) {
        char[] charA = {'D', 'A', 'C'};
        char[] charB = {'D', 'C'};
        System.out.println(con(charA, charB));
    }

    public static boolean con(char[] charA, char[] charB) {
        int[] p = {2, 3, 5, 7, 11, 13, 17, 19, 23, 29, 31,
                37, 41, 43, 47, 53, 59, 61, 67, 71, 73, 79, 83, 89, 97, 101};
        int sum = 1;
        for (int i = 0; i < charA.length; i++) {
            sum *= p[charA[i] - 'A'];
        }
        for (int i = 0; i < charB.length; i++) {
            if (sum % p[charB[i] - 'A'] != 0) {
                return false;
            }
        }
        return true;
    }
```

思路四：如果可以使用Java中的数据结构，HashMap和Set可以很方便地解决问题；如果不能，我们可以构造一个“签名”，将每一个字符映射为整数(范围：0到26)，然后遍历A中的每一个字符，将32位整数的对应位置1(整数初始为0)，最后遍历B中的每一个字符，判断每一个字符代表的整数在整数中是否已置位。时间复杂度O(n)，空间复杂度O(1)，思路四为最优算法。

```java
		// 位与( & )
		// 位与：第一个操作数的的第n位于第二个操作数的第n位如果都是1，那么结果的第n为也为1，否则为0
		System.out.println(5 & 3);// 结果为1
		System.out.println(4 & 1);// 结果为0
	
		// 位或( | )
		// 第一个操作数的的第n位于第二个操作数的第n位 只要有一个是1，那么结果的第n为也为1，否则为0
		System.out.println(5 | 3);// 结果为7


		// 左移( << )
		// 0000 0000 0000 0000 0000 0000 0000 0101 然后左移2位后，低位补0：//
		// 0000 0000 0000 0000 0000 0000 0001 0100 换算成10进制为20
		System.out.println(5 << 2);// 运行结果是20
```

左移运算符就是相当于第一个数乘以2的第二个数次方，就是5`*`2`*`2

char是当成int来计算的，所以在也可以表示成`0000 0000 0000 0000 0000 0000 0000 0000`

每一位都代表2的几次方，所以可以使用`1 << (charA[i] - 'A')`把数组中的某一个数据变成了上面32个0中间的一个1，也就是某一位，而且某一位只能代表一个字符

```java
    public static boolean hashCheck(char[] s1, char[] s2) {
        int mask = 0;
        for (char c: s1) {
            mask = mask | (1 << (c - 'A'));
        }
        for (char c: s2) {
            if ((mask & (1 << (c - 'A'))) == 0) {
                return false;
            }
        }
        return true;
    }
```

先求位运算可以使得数据最大化，也就是那一个位上有1，再求与运算，可以得出在s2中是否有那一个位

## 参考

整理自：https://github.com/julycoding/The-Art-Of-Programming-By-July/blob/master/ebook/code/java/chapter1/1.2：字符串包含.java

https://github.com/julycoding/The-Art-Of-Programming-By-July/blob/master/ebook/zh/01.02.md

http://aijuans.iteye.com/blog/1850655

http://www.cnblogs.com/0201zcr/p/4764427.html