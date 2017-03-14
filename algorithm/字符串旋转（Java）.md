# 字符串旋转（Java）

[TOC]

## 旋转

给定一个字符串，要求把字符串前面的若干个字符移动到字符串的尾部，如把字符串“abcdef”前面的2个字符'a'和'b'移动到字符串的尾部，使得原字符串变成字符串“cdefab”。请写一个函数完成此功能，要求对长度为n的字符串操作的时间复杂度为 O(n)，空间复杂度为 O(1)。

-------------

将一个字符串分成X和Y两个部分，在每部分字符串上定义反转操作，如X^T，即把X的所有字符反转（如，X="abc"，那么X^T="cba"），那么就得到下面的结论：(X^TY^T)^T=YX，显然就解决了字符串的反转问题。

例如，字符串 abcdef ，若要让def翻转到abc的前头，只要按照下述3个步骤操作即可：

1. 首先将原字符串分为两个部分，即X:abc，Y:def；
2. 将X反转，X->X^T，即得：abc->cba；将Y反转，Y->Y^T，即得：def->fed。
3. 反转上述步骤得到的结果字符串X^TY^T，即反转字符串cbafed的两部分（cba和fed）给予反转，cbafed得到defabc，形式化表示为(X^TY^T)^T=YX，这就实现了整个反转。



```
/**
 * 1.1 旋转字符串
 * (github链接：https://github.com/julycoding/The-Art-Of-Programming-By-July/blob/master/ebook/zh/01.01.md)
 *
 * Created by lingguo on 14-6-26.
 */
public class RotateString {
    private Logger logger = LoggerFactory.getLogger(this.getClass());

    /**
     * 问题描述：
     *  给定一个字符串，要求把字符串前面的若干个字符移动到字符串的尾部，如把字符串“abcdef”前面的2个字符'a'和'b'移动到字符串
     *  的尾部，使得原字符串变成字符串“cdefab”。请写一个函数完成此功能，要求对长度为n的字符串操作的时间复杂度为 O(n)，空间
     *  复杂度为 O(1)。
     *
     * 思路：
     *  三步旋转法：(X^TY^T)^T=YX
     *
     * @param str   输入串
     * @param n     前n个字符移到串的最后
     * @return      翻转后的串
     */
     public static char[] leftRotateString(char[] str, int n) {
         char[] rotateLeft = rotate(str, 0, n - 1);
         char[] rotateRight = rotate(rotateLeft, n, str.length - 1);
         char[] result = rotate(rotateRight, 0, str.length - 1);
         return result;
     }

    /**
     * 将字符串翻转
     *
     * @param str       输入串
     * @param start     字符串中的起始索引(包括)
     * @param end       字符串中的终止索引(包括)
     * @return          翻转后的串
     */
    private static char[] rotate(char[] str, int start, int end) {
        while (start < end) {
            char tmp = str[start];
            str[start] = str[end];
            str[end] = tmp;
            start++;
            end--;
        }
        return str;
    }
/*********************************************************************************************************/


    /**
     * 问题描述：
     *  链表翻转。给出一个链表和一个数k，比如，链表为1→2→3→4→5→6，k=2，则翻转后2→1→6→5→4→3，若k=3，翻转后
     *  3→2→1→6→5→4，若k=4，翻转后4→3→2→1→6→5，用程序实现。
     *
     * 思路：
     *  使用反转链表的思路，将链表的前部分反转，然后将链表的后部分反转，最后将前部分链表的尾节点指向后部分链表的头节点。
     *
     * @param head  链表的头节点
     * @param n     将前n个节点反转，剩下的节点反转
     * @return      翻转后链表的头节点
     */
    public static ListNode rotateLinkList(ListNode head, int n) {
        ListNode leftEnd = head;
        int i = 0;
        while (i++ < n) {
            leftEnd = leftEnd.next;     // 左链表的终点
        }
        ListNode leftRotateStart = rotateList(head, leftEnd);       // 左部分反转
        ListNode rightRotateStart = rotateList(leftEnd, null);      // 右部分反转
        head.next = rightRotateStart;                               // 左右部分连接起来
        return leftRotateStart;
    }

    /**
     * 将链表从起点start(inclusive)到终点end(exclusive)的节点反转
     *
     * @param start     链表的起点(包括)
     * @param end       链表的终点(不包括)
     * @return          反转后链表的头节点
     */
    private static ListNode rotateList(ListNode start, ListNode end) {
        ListNode pre = null;
        ListNode cur = start;
        ListNode next = cur.next;
        while (cur != end) {
            next = cur.next;
            cur.next = pre;
            pre = cur;
            cur = next;
        }
        return pre;
    }

    /**
     * 问题描述：
     *  编写程序，在原字符串中把字符串尾部的m个字符移动到字符串的头部，要求：长度为n的字符串操作时间复杂度为O(n)，空间复杂度
     *  为O(1)。 例如，原字符串为”Ilovebaofeng”，m=7，输出结果为：”baofengIlove”。
     *
     * 思路：
     *  与左旋转字符串的思路是一样的；
     *
     * @param str       输入串
     * @param n         将最后n个字符移到串的最前面
     * @return          翻转后的串
     */
    public static char[] rightRotateString(char[] str, int n) {
        rotate(str, 0, str.length - n - 1);
        rotate(str, str.length - n, str.length - 1);
        char[] result = rotate(str, 0, str.length - 1);
        return result;
    }
/**********************************************************************************************************/

    /**
     * 问题描述：
     *  单词翻转。输入一个英文句子，翻转句子中单词的顺序，但单词内字符的顺序不变，句子中单词以空格符隔开。为简单起见，标点符
     *  号和普通字母一样处理。例如，输入“I am a student.”，则输出“student. a am I”。
     *
     * 思路：
     *  先将每个单词反转，然后将整个句子反转；
     *
     * @param sentence      输入的句子
     * @return              翻转后的句子
     */
    public static char[] rotateSentence(char[] sentence) {
        int start = 0;
        int end = 0;
        int i = 0;
        while (i < sentence.length) {
            // 空格是单词的分隔符
            while (i < sentence.length && sentence[i] != ' ') {
                i++;
            }
            end = i - 1;
            sentence = rotate(sentence, start, end);        // 旋转一个单词
            start = (++i);
        }
        sentence = rotate(sentence, 0, sentence.length - 1);    // 翻转整个句子
        return sentence;
    }
/*********************************************************************************************/

}
```

## 参考

整理自：https://github.com/julycoding/The-Art-Of-Programming-By-July/blob/master/ebook/code/java/chapter1/1.1：左旋转字符串.java

https://github.com/julycoding/The-Art-Of-Programming-By-July/blob/master/ebook/zh/01.01.md