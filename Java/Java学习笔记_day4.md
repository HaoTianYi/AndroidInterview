# Java学习笔记_day4

[TOC]

## 选择结构

### 语法结构

switch语句格式：

switch(表达式) {

          case 值1：

           语句体1;

           break;

           case 值2：

           语句体2;

           break;

           …

           default：  

           语句体n+1;

           break;

   }

### 语法说明

•    switch表示这是switch语句

•    表达式的取值：byte,short,int,char

•    JDK5以后可以是枚举

•    JDK7以后可以是String

•    case后面跟的是要和表达式进行比较的值

•    语句体部分可以是一条或多条语句

•    break表示中断，结束的意思，可以结束switch语句

•    default语句表示所有情况都不匹配的时候，就执行该处的内容，和if语句的else相似。

注意：

       A:遇到左大括号缩进一个tab的位置。

       B:关联不是很大的语句间空行

### switch语句的注意事项

       A:case后面只能是常量，不能是变量，而且，多个case后面的值不能出现相同的

       B:default可以省略吗?

           可以省略，但是不建议，因为它的作用是对不正确的情况给出提示。

           特殊情况：

              case就可以把值固定。

              A,B,C,D

       C:break可以省略吗?

           可以省略，但是结果可能不是我们想要的。

           会出现一个现象：case穿透。

           最终我们建议不要省略

       D:default一定要在最后吗?

           不是，可以在任意位置。但是建议在最后。

       E:switch语句的结束条件

           a:遇到break就结束了

           b:执行到末尾就结束了

### switch练习题

1.   判断b的值

```
int a = 2;
		int b = 3;
		switch(a){
			default:
				b++;
			case 3:
				b++;
			case 4:
				b++;
		}
		System.out.println("b="+b);
```

注意：

这样写太麻烦了，我们使用一个我们不想使用的东西：case穿透

```
		switch(month) {
			case 1:
			case 2:
			case 12:
				System.out.println("冬季");
				break;
			case 3:
			case 4:
			case 5:
				System.out.println("春季");
				break;
			case 6:
			case 7:
			case 8:
				System.out.println("夏季");
				break;
			case 9:
			case 10:
			case 11:
				System.out.println("秋季");
				break;
			default:
				System.out.println("你输入的月份有误");
		}

```

## 循环语句

### 分类

for循环,while循环,do...while循环

### 例题

1.   求1-100所有偶数之和：

```
		//方式1
		int sum2 = 0;
		
		for(int x=1; x<=100; x++) {
			if(x%2 == 0) {
				sum2 += x;
			}
		}
		
		System.out.println("1-100偶数之和是："+sum2);
		System.out.println("------------------");
		
		//方式2
		int sum3 = 0;
		
		for(int x=0; x<=100; x+=2) {
				sum3 += x;
		}
		
		System.out.println("1-100偶数之和是："+sum3);
		System.out.println("------------------");

```

2.   求出所有的水仙花数（3位）

所谓的水仙花数是指一个三位数，其各位数字的立方和等于该数本身。

       举例：153就是一个水仙花数。

       153= 1*1*1 + 5*5*5 + 3*3*3 = 1 + 125 + 27 = 153

参考：

```
//三位数其实是告诉了我们范围。

       for(intx=100; x<1000; x++) {

           intge = x%10;

           intshi = x/10%10;

           intbai = x/10/10%10;

           

           //让gegege+shishishi+baibaibai和该数据比较

           if(x== (gegege+shishishi+baibaibai)) {

              //如果相同，就把该数据在控制台输出。

              System.out.println(x);

           }

       }

其实这种也可以只不过算法的空间复杂度太高：

for (int i = 1; i<= 9; i++) {

           for(int j = 0; j <= 9; j++) {

              for(int j2 = 0; j2 < 9; j2++) {

                  intcondition = 100i + 10j + j2 ;

                  intcon = iii + jjj + j2j2j2;

                  if(condition==con) {

                     System.out.println(condition);
                  }                
              }             
           }
       }
```

### while循环和for循环的区别

使用区别：如果你想在循环结束后，继续使用控制条件的那个变量，用while循环，否则用for循环。不知道用for循环。因为变量及早的从内存中消失，可以提高内存的使用效率。

                

其实还有一种场景的理解:

如果是一个范围的，用for循环非常明确。

如果是不明确要做多少次，用while循环较为合适。

#### While的练习题

我国最高山峰是珠穆朗玛峰：8848m，我现在有一张足够大的纸张，厚度为：0.01m。

    请问，我折叠多少次，就可以保证厚度不低于珠穆朗玛峰的高度?

参考：

//定义一个统计变量，默认值是0

       intcount = 0;       

       //最高山峰是珠穆朗玛峰：8848m这是最终的厚度

       //我现在有一张足够大的纸张，厚度为：0.01m这是初始厚度

       //为了简单，我把0.01变成1，同理8848就变成了884800

       intend = 884800;

       intstart = 1;       

       while(start<end){

           //只要每次变化的厚度没有超过珠穆朗玛峰的高度，就折叠，统计变量++

           count++;           

           //折叠一次有什么变化呢?就是厚度是以前的2倍。

           start*= 2;           

           System.out.println("第"+count+"次厚度是"+start);

       }       

       //输出统计变量。

       System.out.println("要叠"+count+"次");

### 循环语句的区别

    do...while循环至少执行一次循环体。

    而for,while循环必须先判断条件是否成立，然后决定是否执行循环体语句。

优先考虑for，其次考虑while，最后考虑do...while

### 注意死循环

一定要注意控制条件语句控制的那个变量的问题，不要弄丢了，否则就容易死循环

while(true){...}

转义字符：

\t tab建     \r   回车键    \n     换行建    后两者意义相同

 

## 跳转控制语句

l  break 中断

l  continue 继续

l  return 返回 

#### break中断

1.   使用场景

A:switch语句中

B:循环语句中。

循环语句中加入了if判断的情况)

注意：离开上面的两个场景，无意义。

2.   使用方法

       A:跳出单层循环

       B:跳出多层循环-----à表示跳出至某层循环

           要想实现这个效果，就必须知道一个东西。带标签的语句。

           格式：

              标签名: 语句

```
		wc:for(int x=0; x<3; x++) {
			nc:for(int y=0; y<4; y++) {
				if(y == 2) {
					//break nc;
					break wc;
				}
				System.out.print("*");
			}
			System.out.println();
		}

```

#### Break和continue对比

•    break  退出当前循环

•    continue  退出本次循环

#### 练习题

    练习题：

```
		for(int x=1; x<=10; x++) {
			if(x%3==0) {
				//在此处填写代码
			}
			System.out.println(“Java基础班”);
		}
```

要求：

       我想在控制台输出2次:“Java基础班“

       我想在控制台输出7次:“Java基础班“

       我想在控制台输出13次:“Java基础班“ 

 

参考答案：

       我想在控制台输出2次:“Java基础班“

           break;

       我想在控制台输出7次:“Java基础班“

           continue;

       我想在控制台输出13次:“Java基础班“ 

           System.out.println(“Java基础班