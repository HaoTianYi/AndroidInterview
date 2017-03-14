# 最长回文子串—4种解法（Java版）

[TOC]

## 题目

给定一个字符串，求它的最长回文子串的长度。

### 暴力破解

最容易想到的就是暴力破解，求出每一个子串，之后判断是不是回文，找到最长的那个。

```java
    public static void main(String[] args) {
        System.out.println(longestPalindrome1("aaba"));
    }

    public static String longestPalindrome1(String s) {

        int maxPalinLength = 0;
        String longestPalindrome = null;

        for (int i = 0; i < s.length(); i++) {
            for (int j = i + 1; j < s.length(); j++) {
                int len = j - i;
//               注意在截取字串的时候好是包头不包尾
                String curr = s.substring(i, j+1);
                if (isRalindrome(curr)) {
                    if (len > maxPalinLength) {
                        longestPalindrome = curr;
                        maxPalinLength = len;
                    }
                }
            }
        }

        return longestPalindrome;
    }

    public static boolean isRalindrome(String s) {
        for (int i = 0; i < s.length() - 1; i++) {
            if (s.charAt(i) != s.charAt(s.length() - 1 - i)) {
                return false;
            }
        }
        return true;
    }
```

### 动态规划

回文字符串的子串也是回文，比如P[i,j]（表示以i开始以j结束的子串）是回文字符串，那么P[i+1,j-1]也是回文字符串。这样最长回文子串就能分解成一系列子问题了。

核心思路就是从左开始遍历，然后不断的从原字符串中拿出1到length-1长度的字串，进行判断

这里用一个二维数组来表示回文字符串的起始位置和结束位置，值`1`代表的是回文字符串，`0`代表不是

```java
   public static void main(String[] args) {
        System.out.println(longestPalindrome2("1263625"));//babcbabcbaccba
    }
    
    public static String longestPalindrome2(String s) {
        if (s == null)
            return null;

        if(s.length() <=1)
            return s;

        int maxLen = 0;
        String longestStr = null;

        int length = s.length();

        int[][] table = new int[length][length];

        //every single letter is palindrome
        for (int i = 0; i < length; i++) {
            table[i][i] = 1;
        }
        printTable(table);

        //e.g. bcba
        //two consecutive same letters are palindrome
        for (int i = 0; i <= length - 2; i++) {
            //System.out.println("i="+i+"  "+s.charAt(i));
            //System.out.println("i="+i+"  "+s.charAt(i+1));
            if (s.charAt(i) == s.charAt(i + 1)){
                table[i][i + 1] = 1;
                longestStr = s.substring(i, i + 2);
            }
        }
        System.out.println(longestStr);
        printTable(table);
        //condition for calculate whole table
        for (int l = 3; l <= length; l++) {
            for (int i = 0; i <= length-l; i++) {
                int j = i + l - 1;
                if (s.charAt(i) == s.charAt(j)) {
                    table[i][j] = table[i + 1][j - 1];
                    if (table[i][j] == 1 && l > maxLen)
                        longestStr = s.substring(i, j + 1);
                } else {
                    table[i][j] = 0;
                }
                printTable(table);
            }
        }

        return longestStr;
    }
    public static void printTable(int[][] x){
        for(int [] y : x){
            for(int z: y){
                System.out.print(z + " ");
            }
            System.out.println();
        }
        System.out.println("------");
    }

```

`table[i][j] = table[i + 1][j - 1]`代码的赋值就是体现P[i,j]（表示以i开始以j结束的子串）是回文字符串，那么P[i+1,j-1]也是回文字符。

### 中心扩展

​	因为回文字符串是以中心轴对称的，所以如果我们从下标 i 出发，用2个指针向 i 的两边扩展判断是否相等，那么只需要对0到n-1的下标都做此操作，就可以求出最长的回文子串。但需要注意的是，回文字符串有奇偶对称之分，即"abcba"与"abba"2种类型，因此需要在代码编写时都做判断。
设函数int Palindromic ( string &s, int i ,int j) 是求由下标 i 和 j 向两边扩展的回文串的长度，那么对0至n-1的下标，调用2次此函数：
int lenOdd =  Palindromic( str, i, i ) 和 int lenEven = Palindromic (str , i , j )，即可求得以i 下标为奇回文和偶回文的子串长度。
接下来以lenOdd和lenEven中的最大值与当前最大值max比较即可。

```java
    public  static String longestPalindrome(String s) {  
        if (s.isEmpty()) {  
            return null;  
        }  
        if (s.length() == 1) {  
            return s;  
        }  
        String longest = s.substring(0, 1);  
        for (int i = 0; i < s.length(); i++) {  
            // get longest palindrome with center of i  
            String tmp = helper(s, i, i);  
            if (tmp.length() > longest.length()) {  
                longest = tmp;  
            }  
  
            // get longest palindrome with center of i, i+1  
            tmp = helper(s, i, i + 1);  
            if (tmp.length() > longest.length()) {  
                longest = tmp;  
            }  
        }  
        return longest;  
    }  
  
    // Given a center, either one letter or two letter,  
    // Find longest palindrome  
    public static String helper(String s, int begin, int end) {  
        while (begin >= 0 && end <= s.length() - 1  
                && s.charAt(begin) == s.charAt(end)) {  
            begin--;  
            end++;  
        }  
        String subS = s.substring(begin + 1, end);  
        return subS;  
    }  
  
    public static void main(String[] args) {  
        System.out.println(longestPalindrome("ABCCBA"));//babcbabcbaccba  
    }  
```

### Manacher算法

参考：[Manacher算法及其Java实现](http://pugong.studio/2017/03/14/Manacher算法及其Java实现/)

## 参考

整理自：http://blog.csdn.net/soszou/article/details/37312317

http://www.cnblogs.com/leoin2012/p/3984997.html







