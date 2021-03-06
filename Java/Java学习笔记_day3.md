# Java学习笔记_day3

[TOC]

## 运算符operator

就是对常量和变量进行操作的符号。

### 分类

算术运算符，赋值运算符，比较运算符，逻辑运算符，位运算符，三目运算符

注：定义变量应该int x = 3；应该说把3赋值给int类型的x；

### 实例

//定义变量

int x = 3;  //把3赋值给int类型的变量x

int y = 4;

System.out.println(x/y); //整数相除只能得到整数-----》结果是0

//我就想得到小数，该肿么办呢?

    //只需要把操作的数据中任意的一个数据变为浮点数

    System.out.println(x*1.0/y);----------à结果是3

A:整数相除只能得到整数。如果想得到小数，必须把数据变化为浮点数类型

    B:/获取的是除法操作的商，%获取的是除法操作的余数

### 算术运算符

![snipaste_20170313_204424](http://oaxelf1sk.bkt.clouddn.com/snipaste_20170313_204424.png)

++,--运算符的使用：

    单独使用：

       放在操作数的前面和后面效果一样。(这种用法是我们比较常见的)

       //x++;

       //y--;

    参与运算使用：

       放在操作数的前面，先自增或者自减，然后再参与运算。

       放在操作数的后面，先参与运算，再自增或者自减。

       int a = 3;

       //int c = a++;--------à结果是3

#### 练习题

第一题：

    int a = 10;

    int b = 10;

    int c = 10;

 

    a = b++;

    c = --a;

    b = ++a;

    a = c--;

    请分别计算出a,b,c的值

    

    第二题：

    int x = 4;

    int y = (x++)+(++x)+(x*10);

    请分别计算出x,y的值      

 

参考答案：

1.   a=9,b=10,c=8

2.   70

 

#### +的用法：

    A:加法

    B:正号

    C:字符串连接符

### 赋值运算符

基本的赋值运算符：=

把=右边的数据赋值给左边。

           

扩展的赋值运算符：+=,-=,*=,/=,%=

    +=把左边和右边做加法，然后赋值给左边

#### 案例

short s=1, s = s+1; short s=1, s+=1;上面两个代码有没有问题，如果有，那里有问题

第一个有问题，short参与数值运算时，先被转换成int类型的数据，第二个正确

扩展的赋值运算符其实隐含了一个强制类型转换。

           s+= 1;

           不是等价于 s = s + 1;

           而是等价于 s = (s的数据类型)(s + 1);

### 关系运算符

![snipaste_20170313_204449](http://oaxelf1sk.bkt.clouddn.com/snipaste_20170313_204449.png)

### 逻辑运算符

![snipaste_20170313_204501](http://oaxelf1sk.bkt.clouddn.com/snipaste_20170313_204501.png)

l  “&”和“&&”的区别：

•    单&时，左边无论真假，右边都进行运算；

•    双&时，如果左边为真，右边参与运算，如果左边为假，那么右边不参与运算。

    “|”和“||”的区别同理，双或时，左边为真，右边不参与运算。

### 位运算符

| **位运算符** |           |                                |
| -------- | --------- | ------------------------------ |
| **运算符**  | **运算**    | **范例**                         |
| **<<**   | **左移**    | **3 << 2 = 12  --> 3\*2*2=12** |
| **>>**   | **右移**    | **3 >> 1 =  1  --> 3/2=1**     |
| **>>>**  | **无符号右移** | **3 >>> 1 =  1 --> 3/2=1**     |
| **&**    | **位与运算**  | **6 & 3 = 2**                  |
| **\|**   | **位或运算**  | **6 \| 3 = 7**                 |
| **^**    | **位异或运算** | **6 ^ 3 = 5**                  |
| **~**    | **位反码**   | **~6 = -7**                    |

#### 案例

System.out.println(3& 4);

       System.out.println(3| 4);

       System.out.println(3^ 4);

       System.out.println(~3);

    分析：因为是位运算，所以我们必须先把数据换算成二进制。

    

    3的二进制：11

       0000000000000000 00000000 00000011

    4的二进制：100

       0000000000000000 00000000 00000100

    

    &位与运算：有0则0。

       0000000000000000 00000000 00000011

       &00000000 00000000 00000000 00000100

       -----------------------------------

       0000000000000000 00000000 00000000

       结果是：0

       

    |位或运算：有1则1。

       0000000000000000 00000000 00000011

       |00000000 00000000 00000000 00000100

       -----------------------------------

       0000000000000000 00000000 00000111

       结果是：7

       

    ^位异或运算：相同则0，不同则1。

       0000000000000000 00000000 00000011

       &00000000 00000000 00000000 00000100

       -----------------------------------

       0000000000000000 00000000 00000111

       结果是：7

       

    ~按位取反运算符：0变1，1变0

       0000000000000000 00000000 00000011

       ~11111111 11111111 11111111 11111100 (补码)

       

       补码：1111111111111111 11111111 11111100

       反码：1111111111111111 11111111 11111011

       原码：1000000000000000 00000000 00000100

       结果是：-4

 

^的特点：一个数据对另一个数据位异或两次，该数本身不变。

 

#### 练习题

把两个数值相掉

//方式1：使用第三方变量(开发中用的)

       intc = a;

       a= b;

       b= c;

       System.out.println("a:"+a+",b:"+b);

       System.out.println("------------");

       //方式2：用位异或实现(面试用)

       //左边：a,b,a

       //右边：a ^ b

       a= a ^ b;

       b= a ^ b; //a ^ b ^ b = a

       a= a ^ b; //a ^ b ^ a = b

       System.out.println("a:"+a+",b:"+b);

       //方式3：用变量相加的做法

       a= a + b; //a=30

       b= a - b; //b=10

       a= a - b; //a=20

       System.out.println("a:"+a+",b:"+b);

       //方式4：一句话搞定

       b= (a+b) - (a=b); //b=30-20=10,a=20

       System.out.println("a:"+a+",b:"+b);

 

#### 其他位运算符

<<:左移 左边最高位丢弃，右边补齐0

\>>:右移 最高位是0，左边补齐0；最高为是1，左边补齐1

\>>>:无符号右移 无论最高位是0还是1，左边补齐0

 

//<< 把<<左边的数据乘以2的移动次幂

       System.out.println(3<< 2); //3*2^2 = 3*4 = 12;

    

       //>>把>>左边的数据除以2的移动次幂

       System.out.println(24>> 2); //24 / 2^2 = 24 / 4 = 6

       System.out.println(24>>> 2);

       

       System.out.println(-24>> 2); //依旧是-24/4 = -6

 

计算出3的二进制：11

       0000000000000000 00000000 00000011

    (00)00000000000000 00000000 0000001100

       

    >>的移动： 

    计算出24的二进制：11000

       原码：10000000 00000000 00000000 00011000

       反码：11111111 11111111 11111111 11100111

       补码：11111111 11111111 11111111 11101000

       

       1111111111111111 11111111 11101000

       111111111111111111 11111111 111010(00) 补码

       

       补码：1111111111 11111111 11111111 111010

       反码：1111111111 11111111 11111111 111001

       原码：1000000000 00000000 00000000 000110

       

       结果：-6

       

    >>>的移动：

       计算出24的二进制：11000

       原码：10000000 00000000 00000000 00011000

       反码：11111111 11111111 11111111 11100111

       补码：11111111 11111111 11111111 11101000

       

       1111111111111111 11111111 11101000

       001111111111111111 11111111 111010(00)

       

       结果：

1.   获取三个整数中的最大值

       inta = 10;

       intb = 30;

       intc = 20;

       

       //分两步：

       //A:先比较a,b的最大值

       //B:拿a,b的最大值在和c进行比较

       inttemp = ((a > b)? a: b);

       //System.out.println(temp);

       intmax1 = (temp > c? temp: c);

       System.out.println("max1:"+max1);

## 流程控制语句

    (1)顺序结构 从上往下，依次执行

    (2)选择结构 按照不同的选择，执行不同的代码

    (3)循环结构 做一些重复的代码

### if语句

#### 三种格式

       A:格式1

           if(比较表达式) {

              语句体;

           }

           

           执行流程：

              判断比较表达式的值，看是true还是false

              如果是true，就执行语句体

              如果是false，就不执行语句体

       

       B:格式2

           if(比较表达式) {

              语句体1;

           }else{

              语句体2;

           }

           

           执行流程：

              判断比较表达式的值，看是true还是false

              如果是true，就执行语句体1

              如果是false，就执行语句体2

              

       C:格式3

           if(比较表达式1) {

              语句体1;

           }elseif(比较表达式2){

              语句体2;

           }

           ...

           else{

              语句体n+1;

           }

#### 三元运算符和if语句第二种格式的关系

所有的三元运算符能够实现的，if语句的第二种格式都能实现。

反之不成立。

如果if语句第二种格式控制的语句体是输出语句，就不可以。

因为三元运算符是一个运算符，必须要有一个结果返回，不能是一个输出语句
