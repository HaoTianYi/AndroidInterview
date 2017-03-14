# Manacher算法及其Java实现

[TOC]

## 说明

现给定一个已知的字符串str[]，现在想要在O(n)的时间复杂度之内求出一个最长的回文子字符串(正着和倒着顺序读一致)。

Manacher最早发现了可以用O(n)的时间复杂度来解决该问题，所以这种方法称之为Manacher算法

## 实现步骤

### 基本过程

求最大回文字串的长度一般要看原串的长度是奇数还是偶数，然后再分别求得，但是Manacher算法的第一个神奇之处，就是把两种字符串都转化为奇数的字符串，从而简化计算：

```java
        // 1.构造新的字符串
        // 为了避免奇数回文和偶数回文的不同处理问题，在原字符串中插入'#'，将所有回文变成奇数回文
        StringBuilder newStr = new StringBuilder();
        newStr.append('#');
        for (int i = 0; i < str.length(); i ++) {
            newStr.append(str.charAt(i));
            newStr.append('#');
        }
```

例如原来是aaaba的字符串变化之后就是，而且无论原来的字符串是奇数还是偶数，变化之后都是奇数（方便运算）
![snipaste_20170314_141428](http://oaxelf1sk.bkt.clouddn.com/snipaste_20170314_141428.png)

当构建完成新的字符串之后，**从左边第一个字符开始遍历，并且记录每一个字符的最大回文半径**，（包括自身），比如第一个`#`的回文半径就是1，左边第一个`A`的回文半径是2，遍历每一个字符之后，得到一个关于半径的数组，数组最大的值减1就是最大回文字串的长度，例如：

![snipaste_20170314_142301](http://oaxelf1sk.bkt.clouddn.com/snipaste_20170314_142301.png)

### 完整实现

Manacher算法引入三个重要的概念（符号可能有所不同）：

第一个是表示已知回文字串的中心点位置id，第二个是已知回文字串最右边的位置right，最后一个就是表示已知字串的回文半径数组rad[]

对应上面来说，id就是不断遍历的位置信息，right就是回文半径`+`id`-`1，rad[]也就是把每一个半径存放起来

#### 确定最小半径

假设现在求出了rad[1, ..., i]，现在要求后面的rad值，再假设现在有个指针k（实际中就是1），从1循环到rad[i]，试图通过某些手段来求出[i + 1, i + rad[i] - 1]的rad值

如图所示，黑色的部分是一个回文子串，两段红色的区间对称相等。因为之前已经求出了rad[i - k]，所以可以避免一些重复的查找和判断，有3种情况：

![001az5G6gy6Gck3Wwgu5e&690](http://oaxelf1sk.bkt.clouddn.com/001az5G6gy6Gck3Wwgu5e&690.jpg)

- rad[i] - k < rad[i - k]

如图，rad[i - k]的范围为青色。因为黑色的部分是回文的,且青色的部分超过了黑色的部分，所以rad[i + k]肯定至少为rad[i]-k,即橙色的部分。那橙色以外的部分就不是了吗?这是肯定的，因为如果橙色以外的部分也是回文的，那么根据青色和红色部分的关系，可以证明黑色部分再往外延伸一点也是一个回文子串,这肯定是不可能的，因此rad[i + k] = rad[i] - k

![001az5G6gy6Gck6wd628a&69020171427](http://oaxelf1sk.bkt.clouddn.com/001az5G6gy6Gck6wd628a&69020171427.jpg)

- rad[i] - k > rad[i - k]

如图，rad[i-k]的范围为青色，因为黑色的部分是回文的，且青色的部分在黑色的部分里面，根据定义，很容易得出:rad[i + k] = rad[i - k]。

根据上面两种情况，可以得出结论:当rad[i] - k != rad[i - k]的时候,**rad[i + k] = min(rad[i] - k, rad[i - k])**

![001az5G6gy6Gck8urVI76&69020143141426](http://oaxelf1sk.bkt.clouddn.com/001az5G6gy6Gck8urVI76&69020143141426.jpg)

- rad[i] - k = rad[i - k]

如图，通过和第一种情况对比之后会发现，因为青色的部分没有超出黑色的部分，所以即使橙色的部分全等，也无法像第一种情况一样引出矛盾，因此橙色的部分是有可能全等的。

根据已知的信息，我们不知道橙色的部分是多长，因此就需要再去尝试和判断了，但是最少**rad[i + k] = min(rad[i] - k, rad[i - k])**，当然此时两者相等

#### 具体代码

```java
    public static int getPalindromeLength(String str) {
        // 1.构造新的字符串
        // 为了避免奇数回文和偶数回文的不同处理问题，在原字符串中插入'#'，将所有回文变成奇数回文
        StringBuilder newStr = new StringBuilder();
        newStr.append('#');
        for (int i = 0; i < str.length(); i ++) {
            newStr.append(str.charAt(i));
            newStr.append('#');
        }
        
        // rad[i]表示以i为中心的回文的最大半径，i至少为1，即该字符本身
        int [] rad = new int[newStr.length()];
        // right表示已知的回文中，最右的边界的坐标
        int right = -1;
        // id表示已知的回文中，拥有最右边界的回文的中点坐标
        int id = -1;
        // 2.计算所有的rad
        // 这个算法是O(n)的，因为right只会随着里层while的迭代而增长，不会减少。
        for (int i = 0; i < newStr.length(); i ++) {
            // 2.1.确定一个最小的半径
            int r = 1;
            if (i <= right) {
                r = Math.min(rad[id] - i + id, rad[2 * id - i]);
            }
            // 2.2.尝试更大的半径
            while (i - r >= 0 && i + r < newStr.length() && newStr.charAt(i - r) == newStr.charAt(i + r)) {
                r++;
            }
            // 2.3.更新边界和回文中心坐标
            if (i + r - 1> right) {
                right = i + r - 1;
                id = i;
            }
            rad[i] = r;
        }
        
        // 3.扫描一遍rad数组，找出最大的半径
        int maxLength = 0;
        for (int r : rad) {
            if (r > maxLength) {
                maxLength = r;
            }
        }
        return maxLength - 1;
    }
```
### 复杂度分析

空间复杂度：插入分隔符形成新串，占用了线性的空间大小；RL数组也占用线性大小的空间，因此空间复杂度是线性的。
时间复杂度：尽管代码里面有两层循环，通过amortized analysis我们可以得出，Manacher的时间复杂度是线性的。由于内层的循环只对尚未匹配的部分进行，因此对于每一个字符而言，只会进行一次，因此时间复杂度是**O(n)**

## 参考

http://blog.sina.com.cn/s/blog_3fe961ae0101iwc2.html

https://segmentfault.com/a/1190000003914228#articleHeader7

http://blog.csdn.net/pi9nc/article/details/9251455

http://blog.csdn.net/xingyeyongheng/article/details/9310555
