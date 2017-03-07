# 02-Mysql学习笔记-数据类型

[TOC]

## 分类

![sp170112_074411](http://oaxelf1sk.bkt.clouddn.com/sp170112_074411.png)

## 数值型

###整数型

| **类型**      | **大小** | **范围（有符号）**                              | **范围（无符号）**                    | **用途** |
| ----------- | ------ | ---------------------------------------- | ------------------------------ | ------ |
| TINYINT     | 1 字节   | (-128，127)                               | (0，255)                        | 小整数值   |
| SMALLINT    | 2 字节   | (-32 768，32 767)                         | (0，65 535)                     | 大整数值   |
| MEDIUMINT   | 3 字节   | (-8 388 608，8 388 607)                   | (0，16 777 215)                 | 大整数值   |
| INT或INTEGER | 4 字节   | (-2 147 483 648，2 147 483 647)           | (0，4 294 967 295)              | 大整数值   |
| BIGINT      | 8 字节   | (-9 233 372 036 854 775 808，9 223 372 036 854 775 807) | (0，18 446 744 073 709 551 615) | 极大整数值  |

一个字节有8位，所以范围就是2的几次方

SQL中的数值类型默认有符号---------数据类型后面加上 unsigned就是无符号类型

在Mysql中的数据类型后面的数字表示最终可以**最大显示的宽度**（包括负号）如：-321是四位，但是321是三位；显示宽度不能改变数据的大小-----当宽度小于数字的位数的时候，字段可以正常显示。

```
CREATE DATABASE demo2;
USE demo2;
CREATE TABLE data(d1 TINYINT UNSIGNED,d2 SMALLINT,d3 TINYINT ,d4 INT ,d5 TINYINT(1) ) CHARSET utf8;
DESC data;
```

![sp170112_081226](http://oaxelf1sk.bkt.clouddn.com/sp170112_081226.png)

```
INSERT INTO data VALUES(100,100,100,100,100);
SELECT *FROM data;
```

![sp170112_081455](http://oaxelf1sk.bkt.clouddn.com/sp170112_081455.png)

当数据的宽度不够现实宽度的时候，系统会自动添加数据宽度；使用0填充的时候数据会自动变成**无符号的数据**--------就是为了报纸固定的数据格式

```sql
ALTER TABLE data ADD d6 TINYINT(3) ZEROFILL;
INSERT INTO data VALUES (100,100,100,100,100,1);
ALTER TABLE data ADD d7 TINYINT(2) ZEROFILL;
INSERT INTO data VALUES (100,100,100,100,100,1,1);
DESC data;
SELECT *
FROM data;
```
![sp170112_081935](http://oaxelf1sk.bkt.clouddn.com/sp170112_081935.png)

![sp170112_082420](http://oaxelf1sk.bkt.clouddn.com/sp170112_082420.png)

其中的数据变成01和001；

### 小数型

浮点型：小数点浮动，会丢失精度

定点性：小数点固定，不会丢失精度

#### 浮点型

浮点数当超出范围之后会丢失数据

| **类型** | **大小** | **范围（有符号）**                              | **范围（无符号）**                              | **用途**  |
| ------ | ------ | ---------------------------------------- | ---------------------------------------- | ------- |
| FLOAT  | 4 字节   | (-3.402 823 466 E+38，-1.175 494 351 E-38)，0，(1.175 494 351 E-38，3.402 823 466 351 E+38) | 0，(1.175 494 351 E-38，3.402 823 466 E+38) | 单精度浮点数值 |
| DOUBLE | 8 字节   | (-1.797 693 134 862 315 7 E+308，-2.225 073 858 507 201 4 E-308)，0，(2.225 073 858 507 201 4 E-308，1.797 693 134 862 315 7 E+308) | 0，(2.225 073 858 507 201 4 E-308，1.797 693 134 862 315 7 E+308) | 双精度浮点数值 |

float的精度范围是7位，double的精度范围是15位。

直接使用float表示没有小数部分；float（M,D）其中M表示总长度，D表示小数部分长度

```
CREATE TABLE data_float(f1 FLOAT,f2 FLOAT(10,2),f3 FLOAT(6,4))CHARSET utf8;
DESC data_float;
INSERT INTO data_float VALUES (100.10,100.10,10.10);  #符合条件
INSERT INTO  data_float VALUES (9999999999,99999999.99,99.9999);  #符合条件
INSERT INTO  data_float VALUES (3e38,2.11e7,10.2222);   #符合条件
```

小数的插入数据的时候可以使用科学记数法也可以直接使用小数；插入的数据整数的部分不能超出长度，但是小数部分可以超出长度，超出的部分自动四舍五入

```
INSERT INTO data_float VALUES (100.10,100.10,1000.10);  #Data truncation: Out of range value for column 'f3' at row 1
INSERT INTO data_float VALUES (100.10,100.10,10.101111111);  #符合条件
```

 当浮点数是由于系统的四舍五入导致的整数位超速范围，系统默认是可以的

```
SELECT *
FROM data_float;
```

![sp170112_085138](http://oaxelf1sk.bkt.clouddn.com/sp170112_085138.png)

#### 定点型

整数部分保证绝对不会丢失精度，小数部分可能丢失精度

| **类型**  | 字节大小                            | **范围（有符号）** | **范围（无符号）** | **用途** |
| ------- | ------------------------------- | ----------- | ----------- | ------ |
| DECIMAL | 对DECIMAL(M,D) ，如果M>D，为M+2否则为D+2 | 依赖于M和D的值    | 依赖于M和D的值    | 小数值    |

M最大值是65，D最大值是30，D的默认值是10

```
CREATE TABLE data_decimal(f1 FLOAT(8,2),d1 DECIMAL(8,2))CHARSET utf8;
DESC data_decimal;

INSERT INTO data_decimal VALUES (123456.12,123456.12);   #合法数据
INSERT INTO data_decimal VALUES (123.123456,123.123456);  #合法数据
--提示Data truncated for column 'd1' at row 1  数据被截断
INSERT INTO data_decimal VALUES (123.123456,123.123456);  #合法数据 
SELECT *
FROM data_decimal;
```

![sp170112_090425](http://oaxelf1sk.bkt.clouddn.com/sp170112_090425.png)

```
show warnings；显示警告
```

浮点数进位导致的长度溢出可以正常的插入，但是定点不行

![sp170112_093027](http://oaxelf1sk.bkt.clouddn.com/sp170112_093027.png)

## 时间和日期

| 类型        | 大小(字节) | 范围                                      | 格式                  | 用途           |
| --------- | ------ | --------------------------------------- | ------------------- | ------------ |
| DATE      | 3      | 1000-01-01/9999-12-31                   | YYYY-MM-DD          | 日期值          |
| TIME      | 3      | '-838:59:59'/'838:59:59'                | HH:MM:SS            | 时间值或持续时间     |
| YEAR      | 1      | 1901/2155                               | YYYY                | 年份值          |
| DATETIME  | 8      | 1000-01-01 00:00:00/9999-12-31 23:59:59 | YYYY-MM-DD HH:MM:SS | 混合日期和时间值     |
| TIMESTAMP | 4      | 1970-01-01 00:00:00/2037 年某时            | YYYYMMDD HHMMSS     | 混合日期和时间值，时间戳 |

datatime有零值0000-00-00     00:00:00

tinestamp和datatime格式完全相同

```
CREATE TABLE data_time(d1 DATETIME,d2 DATE,d3 TIME,d4 TIMESTAMP,d5 YEAR)CHARSET utf8;
DESC data_time;
```

 timestamp有他的特殊性

![sp170112_093754](http://oaxelf1sk.bkt.clouddn.com/sp170112_093754.png)

#### 插入时间

```sql
#标准的数据插入格式
INSERT INTO data_time VALUES ('2066-10-10 10:10:10','2066-10-10','10:10:10','2006-10-10 10:10:10',2066);
#时间使用负数
INSERT INTO data_time VALUES ('2066-10-10 10:10:10','2066-10-10','-10:10:10','2006-10-10 10:10:10',2066);
#这里的负数表示一个时间段
INSERT INTO data_time VALUES ('2066-10-10 10:10:10','2066-10-10','-321:10:10','2006-10-10 10:10:10',2066);
#这里的2表示2天，也就是过去的两天和10小时
INSERT INTO data_time VALUES ('2066-10-10 10:10:10','2066-10-10','-3 10:10:10','2006-10-10 10:10:10',2066);

#year可以使用2位数也可以使用4位数
INSERT INTO data_time VALUES ('2066-10-10 10:10:10','2066-10-10','10:10:10','2006-10-10 10:10:10',66);
INSERT INTO data_time VALUES ('2066-10-10 10:10:10','2066-10-10','10:10:10','2006-10-10 10:10:10',70);

SELECT * FROM data_time;
```

注意时间的两种格式：以及70和66的分别

![sp170112_094853](http://oaxelf1sk.bkt.clouddn.com/sp170112_094853.png)

timestap只要该字段被更新，那么时间一定会更新成当前时间

```
UPDATE data_time SET d1 = '1980-10-10 11:11:11' WHERE d5 = '1970';
SELECT * FROM data_time;
```

![sp170112_095307](http://oaxelf1sk.bkt.clouddn.com/sp170112_095307.png)

timestamp会更新位当前时间2017年1月12日

## 字符串类型

SQL中把字符串分成了6类：char、varchar、text、blob、enum、set

| 类型         | 大小              | 用途                 |
| ---------- | --------------- | ------------------ |
| CHAR       | 0-255           | 定长字符串              |
| VARCHAR    | 0-65535         | 变长字符串              |
| TINYBLOB   | 0-255           | 不超过 255 个字符的二进制字符串 |
| TINYTEXT   | 0-255           | 短文本字符串             |
| BLOB       | 0-65 535        | 二进制形式的长文本数据        |
| TEXT       | 0-65 535        | 长文本数据              |
| MEDIUMBLOB | 0-16 777 215    | 二进制形式的中等长度文本数据     |
| MEDIUMTEXT | 0-16 777 215    | 中等长度文本数据           |
| LONGBLOB   | 0-4 294 967 295 | 二进制形式的极大文本数据       |
| LONGTEXT   | 0-4 294 967 295 | 极大文本数据             |

### 定长字符串

在定义二维表的时候就已经定义了存储长度，char（L）其中L表示的是字符长度

例如char（5）在utf8的环境中需要使用4*3 = 12字节来存储

### 变长字符串

varchar在分配空间的时候，按照最大的空间分配，但是实际使用是根据具体的数据来分配的；varchar(L)其中L表示字符的长度，而且会多使用1~2个字节来确定边界，比255小的字符长度用一个字节，比255大使用两个字节；其实1~2是具体用来表示位数的字节，1个字节256位，2个字节65536位，所以使用两个字节就可以表示

例如：varchar（10）在utf8的环境下占有 10*3+1 = 31个字节

存储空间（utf8环境之下）

| 实际数据 | char(4) | varchar(4) | char占用字节 | varchar(占用字节) |
| ---- | ------- | ---------- | -------- | ------------- |
| abcd | abcd    | abcd       | 4*3=12   | 4*3+1=13      |
| a    | a       | a          | 4*3=12   | 1*3+1 = 4     |
|      | 错误      | 错误         | 数据超出长度   | 数据超出长度        |

注意char是定长字符串，空间不变

当数据的实际长度超过255的时候是**text**；定长浪费空间，但是效率高，如身份证、电话号码；变长省空间但是效率低，如地址。

### 文本字符串

数据量巨大，字符个数超过255使用文本字符串，分类：

1. text存储文字（二进制数据通常使用text存储路径）
2. blob存储二进制，如文件（通常不用）

### 枚举

将事先所有的结果都设计好，实际是哪个使用的数据必须是规定中的一个，使用方式：

enum（可能出现列表），如enum('男','女')；

![sp170112_132609](http://oaxelf1sk.bkt.clouddn.com/sp170112_132609.png)

插入数据

```
INSERT INTO data_enum VALUES ('男');   #正确的数据
INSERT INTO data_enum VALUES ('nan'); #数据格式错误
SELECT *
FROM data_enum;
```

作用：

1. 规范数据格式（数据只能是选项中的一个）
2. 节省空间（枚举实际存储的数值）

-------------

判断实际存储是否是数值？

解决方案：把结果+0，如果最后的值是0，那么存储的就是字符串，反之是数值

```
SELECT gender + 0,gender FROM data_enum;
```

![sp170112_133711](http://oaxelf1sk.bkt.clouddn.com/sp170112_133711.png)

那么知道枚举系统中最终存储的是数值

按照元素出现的顺序，把备选项从**1开始不断向后排列**。

![sp170112_134105](http://oaxelf1sk.bkt.clouddn.com/sp170112_134105.png)

因为枚举存储的数值，所以可以**直接插入数值**

-------------

### 集合字符串

集合字符串和枚举很类似，实际存储的都是数值而不是字符串

创建：set（集合元素）

使用：元素类表中的元素，可以使用多个

示例代码：

```sql
CREATE TABLE data_set(love SET('q','w','e','r'))CHARSET utf8;
INSERT INTO data_set VALUES (3);     #这里的本质
INSERT INTO data_set VALUES ('q,w');
SELECT *
FROM data_set;
```

![sp170112_135246](http://oaxelf1sk.bkt.clouddn.com/sp170112_135246.png)

```
CREATE TABLE data_set(love SET('q','w','e','r'))CHARSET utf8;
INSERT INTO data_set VALUES (3);
INSERT INTO data_set VALUES ('q,w');
INSERT INTO data_set VALUES ('q,e,r');

SELECT love +0 ,love FROM data_set;
```

![sp170112_135638](http://oaxelf1sk.bkt.clouddn.com/sp170112_135638.png)

**集合中每一个元素对应二进制中的一个位，选中为1，没有选中就是0，然后再把位数反过来。**

其他实例：

![sp170112_135825](http://oaxelf1sk.bkt.clouddn.com/sp170112_135825.png)

所以元素的顺序和插入时候的顺序无关，系统会重新匹配

## Mysql记录长度

Mysql中任何一条记录的长度不能超过65535个字符（varchar永远达不到理论值）

![sp170112_141540](http://oaxelf1sk.bkt.clouddn.com/sp170112_141540.png)

21844是由系统提示得来

![sp170112_141746](http://oaxelf1sk.bkt.clouddn.com/sp170112_141746.png)

## 列属性

属性列表：NULL/NOT NULL default ,primary key,unique key ,auto_increment,comment

![sp170112_144931](http://oaxelf1sk.bkt.clouddn.com/sp170112_144931.png)

![sp170112_145011](http://oaxelf1sk.bkt.clouddn.com/sp170112_145011.png)

![sp170112_145056](http://oaxelf1sk.bkt.clouddn.com/sp170112_145056.png)

![sp170112_145219](http://oaxelf1sk.bkt.clouddn.com/sp170112_145219.png)

![sp170112_145158](http://oaxelf1sk.bkt.clouddn.com/sp170112_145158.png)

