# 字符串解析（Java）

[TOC]

## 题目

输入一个由数字组成的字符串，把它转换成整数并输出。例如：输入字符串"123"，输出整数123。

实现字符串转换成整数的功能，不能使用库函数

## 分析

可以使用`char[i]-'0'`从而得到每一个数字，然后再乘以位数即可得到整数

```java
    public static void main(String[] args) {
        System.out.println(StrToInt(new String("123456").toCharArray()));
    }

    public static int StrToInt(char[] chars){
//      每一位的具体数字
        int index;
        int sum = 0;
        for (int i = 0; i < chars.length; i++) {
            index = chars[i]-'0';
            sum+=index*Math.pow(10,chars.length-1-i);
        }
        return sum;
    }
```

但是还有部分问题：

1. 正负符号：整数不仅包含数字，还有可能是以'+'或'-'开头表示正负整数，因此如果第一个字符是'-'号，则要把得到的整数转换成负整数。
2. 非法字符：输入的字符串中可能含有不是数字的字符。因此，每当碰到这些非法的字符，程序应停止转换。
3. 整型溢出：输入的数字是以字符串的形式输入，因此输入一个很长的字符串将可能导致溢出。

```java
  //表示的是进制
  private static int radis = 10;

    /**
     * 将STRING类型转化为int类型，有如下几个步骤
     * 1、判断正负
     * 2、判断非整数
     * 3、“321” 转为 321 可以看做是 3*100 + 2*10 + 1 这等于是每次循环时，都对前几位数字*10再累减
     * 4、判断是否超出int的最大值范围(-2^31~2^31-1)
     * 判断是否超过最大值范围 不能直接拿值和Integer的max比较, 因为可能会发生溢出  应该使用 max/10来比较
     */
    public static int atoi(String str) {

        if (str == null || "".equals(str)) {
            return 0;
        }
        String s = str.trim();
        int result = 0;
        boolean negative = false;
        int i = 0, len = s.length();
        int limit = -Integer.MAX_VALUE;
        int multmin;
        int digit;
        try {

            if (len > 0) {
                char firstChar = s.charAt(0);
                if (firstChar < '0') { // Possible leading "+" or "-"
                    if (firstChar == '-') {
                        negative = true;
                        limit = Integer.MIN_VALUE;
                    } else if (firstChar != '+')
                        throw new Exception("char Exception");

                    if (len == 1) // Cannot have lone "+" or "-"
                        throw new Exception("char Exception");
                    i++;
                }
                multmin = limit / radis;

                while (i < len) {
                    digit = Character.digit(s.charAt(i++), radis);
                    if (digit < 0) {
                        return negative ? result : -result;
                    }
                    if (result < multmin) {
                        if (negative) {
                            return Integer.MIN_VALUE;
                        } else {
                            return Integer.MAX_VALUE;
                        }
                    }
                    result = result * radis;
                    if (result < limit + digit) {
                        if (negative) {
                            return Integer.MIN_VALUE;
                        } else {
                            return Integer.MAX_VALUE;
                        }

                    }
                    result -= digit;
                }
            }

        } catch (Exception e) {
            e.printStackTrace();
        }
        return negative ? result : -result;
    }
```

无论是正数还是负数都是按照*进制然后累减（目前都是负数），最后根据nagative来判断正负号

## 题目2

回文，英文palindrome，指一个顺着读和反过来读都一样的字符串，比如madam、我爱我，这样的短句在智力性、趣味性和艺术性上都颇有特色，中国历史上还有很多有趣的回文诗。

那么，我们的第一个问题就是：判断一个字串是否是回文？

### 解析

同时从字符串头尾开始向中间扫描字串，如果所有字符都一样，那么这个字串就是一个回文。采用这种方法的话，我们只需要维护头部和尾部两个扫描指针即可。

```java
    public static boolean isPalindromeV1(String str) {
        if (null == str || 0 == str.length()) {
            return false;
        }

        int length = str.length();
        if (1 == length) {
            return true;
        }

        for (int leftFlag = 0, rightFlag = length - 1; leftFlag < rightFlag; leftFlag++, rightFlag--) {
            if (str.charAt(leftFlag) != str.charAt(rightFlag))
                return false;
        }

        return true;
    }
```

上述解法一从两头向中间扫描，那么是否还有其它办法呢？我们可以先从中间开始、然后向两边扩展查看字符是否相等

```java
   public static boolean isPalindromeV2(String str) {
        if (null == str || 0 == str.length()) {
            return false;
        }

        int length = str.length();
        if (1 == length) {
            return true;
        }

        int leftFlag = 0;
        int rightFlag = length;
        leftFlag = length / 2 - 1;
        if (0 == length % 2) {
            rightFlag = leftFlag + 1;
        } else {
            rightFlag = leftFlag + 2;
        }

        for (; leftFlag >= 0; leftFlag--, rightFlag++) {
            if (str.charAt(leftFlag) != str.charAt(rightFlag))
                return false;
        }

        return true;
    }
```

## 参考

整理自：https://github.com/julycoding/The-Art-Of-Programming-By-July/blob/master/ebook/zh/01.04.md

https://github.com/julycoding/The-Art-Of-Programming-By-July/blob/master/ebook/code/java/chapter1/Palindrome.java

https://github.com/julycoding/The-Art-Of-Programming-By-July/blob/master/ebook/code/java/chapter1/StringToInt