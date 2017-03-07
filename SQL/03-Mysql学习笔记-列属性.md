# 03-Mysql学习笔记-列属性

[TOC]

## 主键

![snipaste_20170117_092652](http://oaxelf1sk.bkt.clouddn.com/snipaste_20170117_092652.png)

主键本身就不能为空，不用再指定not null

### 创建主键

![snipaste_20170119_124812](http://oaxelf1sk.bkt.clouddn.com/snipaste_20170119_124812.png)

![snipaste_20170119_124754](http://oaxelf1sk.bkt.clouddn.com/snipaste_20170119_124754.png)

```
CREATE DATABASE demo;
use demo;
CREATE TABLE my_primary(
  name VARCHAR(20) not NULL comment '姓名',
  number CHAR(10) PRIMARY KEY COMMENT '学号'
)CHARSET utf8;
SHOW TABLES ;
DESC my_primary;
```

![snipaste_20170119_125518](http://oaxelf1sk.bkt.clouddn.com/snipaste_20170119_125518.png)

```
CREATE TABLE my_primary2(
  name CHAR(20) NOT NULL COMMENT '姓名',
  number CHAR(10) COMMENT '学号',
  score TINYINT UNSIGNED DEFAULT 60 COMMENT '成绩',
  PRIMARY KEY (number,score)
)CHARSET utf8;
DESC my_primary2;
```

![snipaste_20170119_125411](http://oaxelf1sk.bkt.clouddn.com/snipaste_20170119_125411.png)

![snipaste_20170119_125448](http://oaxelf1sk.bkt.clouddn.com/snipaste_20170119_125448.png)

```
CREATE TABLE my_primary3(
  name CHAR(20) NOT NULL COMMENT '姓名',
  number CHAR(10) COMMENT '学号',
  score TINYINT UNSIGNED DEFAULT 60 COMMENT '成绩'
)CHARSET utf8;
ALTER TABLE my_primary3 MODIFY score CHAR(10) PRIMARY KEY COMMENT '成绩';
DESC my_primary3;
ALTER TABLE my_primary3 ADD PRIMARY KEY (number,score);
```

### 主键约束

![snipaste_20170119_130022](http://oaxelf1sk.bkt.clouddn.com/snipaste_20170119_130022.png)

### 更新主键

![snipaste_20170119_130408](http://oaxelf1sk.bkt.clouddn.com/snipaste_20170119_130408.png)

```sql
CREATE TABLE my_primary3(
  name CHAR(20) NOT NULL COMMENT '姓名',
  number CHAR(10) COMMENT '学号',
  score TINYINT UNSIGNED DEFAULT 60 COMMENT '成绩'
)CHARSET utf8;
ALTER TABLE my_primary3 MODIFY score CHAR(10) PRIMARY KEY COMMENT '成绩';
DESC my_primary3;
ALTER TABLE my_primary3 DROP PRIMARY KEY ;
ALTER TABLE my_primary3 ADD PRIMARY KEY (number,score);
DESC my_primary3;
```

### 主键分类

![snipaste_20170119_130839](http://oaxelf1sk.bkt.clouddn.com/snipaste_20170119_130839.png)

#### 自动增长

![snipaste_20170119_131137](http://oaxelf1sk.bkt.clouddn.com/snipaste_20170119_131137.png)

第一个错误，要求列必须是key（索引）

![snipaste_20170119_131409](http://oaxelf1sk.bkt.clouddn.com/snipaste_20170119_131409.png)

正确实例：

```
CREATE TABLE my_auto (
  id INT PRIMARY KEY AUTO_INCREMENT COMMENT '测试id',
  name VARCHAR(10) NOT NULL
)CHARSET utf8;
```

#### 自增长的特点

```
CREATE TABLE my_auto (
  id INT PRIMARY KEY AUTO_INCREMENT COMMENT '测试id',
  name VARCHAR(10) NOT NULL
)CHARSET utf8;
INSERT INTO my_auto(name) VALUES ('qqq');
INSERT INTO my_auto VALUES (NULL ,'www');
INSERT INTO my_auto VALUES (DEFAULT ,'eee');
SELECT *FROM my_auto;
```

![snipaste_20170119_131917](http://oaxelf1sk.bkt.clouddn.com/snipaste_20170119_131917.png)

自增长默认第一个数值是1，而且每次增加1

![snipaste_20170119_132204](http://oaxelf1sk.bkt.clouddn.com/snipaste_20170119_132204.png)

![snipaste_20170119_132141](http://oaxelf1sk.bkt.clouddn.com/snipaste_20170119_132141.png)

![snipaste_20170119_132349](http://oaxelf1sk.bkt.clouddn.com/snipaste_20170119_132349.png)

可以通过查看创建代码来找出下一次的自动增长的数字

```
CREATE TABLE my_auto (
  id INT PRIMARY KEY AUTO_INCREMENT COMMENT '测试id',
  name VARCHAR(10) NOT NULL
)CHARSET utf8;
INSERT INTO my_auto(name) VALUES ('qqq');
INSERT INTO my_auto VALUES (NULL ,'www');
INSERT INTO my_auto VALUES (DEFAULT ,'eee');
INSERT INTO my_auto VALUES (8 ,'rrr');
INSERT INTO my_auto(name) VALUES ('ttt');
INSERT INTO my_auto VALUES (5 ,'yyy');
INSERT INTO my_auto(name) VALUES ('uuu');
SELECT *FROM my_auto;
SHOW CREATE TABLE my_auto;

#运行结果
CREATE TABLE `my_auto` (
  `id` int(11) NOT NULL AUTO_INCREMENT COMMENT '测试id',
  `name` varchar(10) NOT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB AUTO_INCREMENT=11 DEFAULT CHARSET=utf8
```

查看到AUTO_INCREMENT=11

#### 修改自增长

![snipaste_20170119_133027](http://oaxelf1sk.bkt.clouddn.com/snipaste_20170119_133027.png)

```
ALTER TABLE my_auto AUTO_INCREMENT = 20;
SHOW CREATE TABLE my_auto;
```

![snipaste_20170119_133202](http://oaxelf1sk.bkt.clouddn.com/snipaste_20170119_133202.png)

#### 查看自增长

![snipaste_20170119_133554](http://oaxelf1sk.bkt.clouddn.com/snipaste_20170119_133554.png)

注意这种修改是会话级（当前会话结束不在有效）

![snipaste_20170119_133529](http://oaxelf1sk.bkt.clouddn.com/snipaste_20170119_133529.png)

#### 删除自增长

![snipaste_20170119_134216](http://oaxelf1sk.bkt.clouddn.com/snipaste_20170119_134216.png)

```sql
#错误的写法，不用写出primary key
ALTER TABLE my_auto MODIFY id INT PRIMARY KEY ;
#正确的写法，不用写出primary key
ALTER TABLE my_auto MODIFY id INT ;
DESC my_auto;
```

## 唯一键

![snipaste_20170119_134732](http://oaxelf1sk.bkt.clouddn.com/snipaste_20170119_134732.png)

### 增加唯一键

![snipaste_20170119_134808](http://oaxelf1sk.bkt.clouddn.com/snipaste_20170119_134808.png)

```
CREATE TABLE my_unique (
  number INT UNIQUE
  COMMENT '学号，唯一，可以是空'
)CHARSET utf8;
DESC my_unique;
```

![snipaste_20170119_135051](http://oaxelf1sk.bkt.clouddn.com/snipaste_20170119_135051.png)

![snipaste_20170119_135104](http://oaxelf1sk.bkt.clouddn.com/snipaste_20170119_135104.png)

**再key标示pri不一定是主键**，只要满足不为空而且唯一（表中没有主键），那么就显示pri

![snipaste_20170119_135549](http://oaxelf1sk.bkt.clouddn.com/snipaste_20170119_135549.png)

![snipaste_20170119_135637](http://oaxelf1sk.bkt.clouddn.com/snipaste_20170119_135637.png)

![snipaste_20170119_135756](http://oaxelf1sk.bkt.clouddn.com/snipaste_20170119_135756.png)

### 唯一键约束

![snipaste_20170119_135903](http://oaxelf1sk.bkt.clouddn.com/snipaste_20170119_135903.png)

![snipaste_20170119_140221](http://oaxelf1sk.bkt.clouddn.com/snipaste_20170119_140221.png)

### 更新唯一键

![snipaste_20170119_192034](http://oaxelf1sk.bkt.clouddn.com/snipaste_20170119_192034.png)

## 索引

![snipaste_20170121_130209](http://oaxelf1sk.bkt.clouddn.com/snipaste_20170121_130209.png)

![snipaste_20170121_130454](http://oaxelf1sk.bkt.clouddn.com/snipaste_20170121_130454.png)

## 关系

![snipaste_20170121_130820](http://oaxelf1sk.bkt.clouddn.com/snipaste_20170121_130820.png)

### 一对一关系

![snipaste_20170121_131138](http://oaxelf1sk.bkt.clouddn.com/snipaste_20170121_131138.png)

### 一对多关系

![snipaste_20170121_131226](http://oaxelf1sk.bkt.clouddn.com/snipaste_20170121_131226.png)

### 多对多关系

![snipaste_20170121_131320](http://oaxelf1sk.bkt.clouddn.com/snipaste_20170121_131320.png)

如：老师和学生

## 范式

![snipaste_20170121_131628](http://oaxelf1sk.bkt.clouddn.com/snipaste_20170121_131628.png)

### 1NF

![snipaste_20170121_131902](http://oaxelf1sk.bkt.clouddn.com/snipaste_20170121_131902.png)

### 2NF

![snipaste_20170121_132138](http://oaxelf1sk.bkt.clouddn.com/snipaste_20170121_132138.png)

### 3NF

![snipaste_20170121_132314](http://oaxelf1sk.bkt.clouddn.com/snipaste_20170121_132314.png)

![snipaste_20170121_132425](http://oaxelf1sk.bkt.clouddn.com/snipaste_20170121_132425.png)

### 逆规范化

![snipaste_20170121_132929](http://oaxelf1sk.bkt.clouddn.com/snipaste_20170121_132929.png)

## 数据高级操作

![snipaste_20170121_133129](http://oaxelf1sk.bkt.clouddn.com/snipaste_20170121_133129.png)

### 主键冲突

![snipaste_20170121_133353](http://oaxelf1sk.bkt.clouddn.com/snipaste_20170121_133353.png)

#### 更新

```sql
CREATE TABLE test(
  name VARCHAR(10) PRIMARY KEY ,
  code INT(10)
)CHARSET utf8;
DESC test;
INSERT INTO test VALUES ('qq',10),('ww',22);
SELECT *
FROM test;
INSERT INTO test VALUES ('qq',100) ON DUPLICATE KEY UPDATE code = 100;
```

![snipaste_20170121_134309](http://oaxelf1sk.bkt.clouddn.com/snipaste_20170121_134309.png)

注意这里更新受影响的有2行，首先插入一行，然后再更新一行（没有冲突就不用更新）

#### 替换

```sql
CREATE TABLE test(
  name VARCHAR(10) PRIMARY KEY ,
  code INT(10)
)CHARSET utf8;
DESC test;
INSERT INTO test VALUES ('qq',10),('ww',22);
SELECT *
FROM test;
REPLACE INTO test VALUES ('qq',1000000);
```

### 蠕虫复制

![snipaste_20170121_135508](http://oaxelf1sk.bkt.clouddn.com/snipaste_20170121_135508.png)

只是复制结构不会复制数据

----------------------------

![snipaste_20170121_135618](http://oaxelf1sk.bkt.clouddn.com/snipaste_20170121_135618.png)

![snipaste_20170121_135717](http://oaxelf1sk.bkt.clouddn.com/snipaste_20170121_135717.png)

### 更新数据

![snipaste_20170121_135923](http://oaxelf1sk.bkt.clouddn.com/snipaste_20170121_135923.png)

### 删除数据

![snipaste_20170121_140113](http://oaxelf1sk.bkt.clouddn.com/snipaste_20170121_140113.png)

### 查询数据

![snipaste_20170121_140238](http://oaxelf1sk.bkt.clouddn.com/snipaste_20170121_140238.png)

#### select选项

![snipaste_20170121_140338](http://oaxelf1sk.bkt.clouddn.com/snipaste_20170121_140338.png)

```sql
CREATE TABLE shuJv(
  number CHAR(4)
)CHARSET utf8;
INSERT INTO shuJv VALUES ('q'),('q'),('w'),('e'),('r'),('t'),('y'),('y');
SELECT *
FROM shuJv;
SELECT DISTINCT *FROM shuJv;
```

#### 字段别名

![snipaste_20170121_140949](http://oaxelf1sk.bkt.clouddn.com/snipaste_20170121_140949.png)

----------

可以使用as，也可以不用as

-------------

```sql
CREATE TABLE my_primary3(
  name CHAR(20) NOT NULL COMMENT '姓名',
  number CHAR(10) COMMENT '学号',
  score TINYINT UNSIGNED DEFAULT 60 COMMENT '成绩'
)CHARSET utf8;
ALTER TABLE my_primary3 MODIFY score CHAR(10) PRIMARY KEY COMMENT '成绩';
DESC my_primary3;
ALTER TABLE my_primary3 DROP PRIMARY KEY ;
ALTER TABLE my_primary3 ADD PRIMARY KEY (number,score);
DESC my_primary3;
INSERT INTO my_primary3 VALUES ('王五','j',1000),('王五','j',1010),('王五','j',4100),('王五','j',1700);
SELECT *
FROM my_primary3;
SELECT name AS 名字,
  number AS 成绩,
  score AS 分数
FROM my_primary3;
```

![snipaste_20170121_141518](http://oaxelf1sk.bkt.clouddn.com/snipaste_20170121_141518.png)

注意这里列的显示名字

#### 数据源

![snipaste_20170121_141734](http://oaxelf1sk.bkt.clouddn.com/snipaste_20170121_141734.png)

- 单表数据源

就是普通的`SELECT *FROM test;`

- 多表数据源

类似`SELECT *FROM test,my_primary3;`也就是笛卡尔积

第一张表：

```
CREATE TABLE my_primary3(
  name CHAR(20) NOT NULL COMMENT '姓名',
  number CHAR(10) COMMENT '学号',
  score TINYINT UNSIGNED DEFAULT 60 COMMENT '成绩'
)CHARSET utf8;
ALTER TABLE my_primary3 MODIFY score CHAR(10) PRIMARY KEY COMMENT '成绩';
DESC my_primary3;
ALTER TABLE my_primary3 DROP PRIMARY KEY ;
ALTER TABLE my_primary3 ADD PRIMARY KEY (number,score);
DESC my_primary3;
INSERT INTO my_primary3 VALUES ('王五','j',1000),('王五','j',1010),('王五','j',4100),('王五','j',1700);
SELECT *
FROM my_primary3;
SELECT name AS 名字,
  number AS 成绩,
  score AS 分数
FROM my_primary3;
```

![snipaste_20170121_141518](http://oaxelf1sk.bkt.clouddn.com/snipaste_20170121_141518.png)

第二张表：

```
CREATE TABLE test(
  name VARCHAR(10) PRIMARY KEY ,
  code INT(10)
)CHARSET utf8;
DESC test;
INSERT INTO test VALUES ('qq',10),('ww',22);
REPLACE INTO test VALUES ('qq',1000000);
SELECT *
FROM test;
```

![snipaste_20170121_142333](http://oaxelf1sk.bkt.clouddn.com/snipaste_20170121_142333.png)

多表数据源：

```
SELECT *
FROM test,my_primary3;
```

![snipaste_20170121_142427](http://oaxelf1sk.bkt.clouddn.com/snipaste_20170121_142427.png)

![snipaste_20170121_142520](http://oaxelf1sk.bkt.clouddn.com/snipaste_20170121_142520.png)

- 子查询

![snipaste_20170121_142642](http://oaxelf1sk.bkt.clouddn.com/snipaste_20170121_142642.png)

#### where子句

![snipaste_20170121_143003](http://oaxelf1sk.bkt.clouddn.com/snipaste_20170121_143003.png)

```sql
CREATE TABLE shuJv(
  number CHAR(4)
)CHARSET utf8;
INSERT INTO shuJv VALUES ('q'),('q'),('w'),('e'),('r'),('t'),('y'),('y');
SELECT *
FROM shuJv;
SELECT DISTINCT *FROM shuJv;
SELECT * FROM shuJv WHERE number = 'q'||number = 'e';
SELECT *
FROM shuJv WHERE number IN ('q','e');
```

最后的两种查询方式的效果相同：

```sql
SELECT * FROM shuJv WHERE number = 'q'||number = 'e';
SELECT *
FROM shuJv WHERE number IN ('q','e');
```
查找在1和6之间的数据
```sql
CREATE TABLE shuJv2 (
  number TINYINT
)
  CHARSET utf8;
INSERT INTO shuJv2 VALUES (2), (3), (4), (4), (5), (5), (6), (7), (8);
SELECT *
FROM shuJv2
WHERE number > 1 && number < 6;
SELECT *
FROM shuJv2
WHERE number BETWEEN 1 AND 6;
```
最后的两种查询的结果相同

#### group by子句

![snipaste_20170121_150137](http://oaxelf1sk.bkt.clouddn.com/snipaste_20170121_150137.png)

```sql
CREATE TABLE student2 (
  name   VARCHAR(10),
  age    INT,
  height INT,
  sex    INT
)
  CHARSET utf8;
INSERT INTO student2 VALUES ("王五", 40, 160, 1), ("王五", 40, 160, 0), ("王五", 40, 170, 0), ("王五", 40, 160, 0)
  , ("王五", 40, 170, 1), ("王五", 40, 150, 1);
SELECT *
FROM student2;
SELECT
  sex,
  count(*),
  min(height),
  min(height),
  max(height),
  avg(age)
FROM student2
GROUP BY sex;
```

![snipaste_20170121_150334](http://oaxelf1sk.bkt.clouddn.com/snipaste_20170121_150334.png)

也就是说假如某一个字段的值是null那么，*和字段名的值就会差1

-------------

![snipaste_20170121_151447](http://oaxelf1sk.bkt.clouddn.com/snipaste_20170121_151447.png)

desc降序排序

---------

#### 多字段分组

```sql
CREATE TABLE student3 (
  name  VARCHAR(10),
  age   INT,
  class INT,
  sex   VARCHAR(10)
)
  CHARSET utf8;
INSERT INTO student3
VALUES ("天意8", 22, 1, '男'), ("天意7", 22, 2, '女'), ("天意10", 22, 3, '女'), ("天意41", 22, 4, '男'), ("天意51", 22, 1, '女');
SELECT
  class,
  count(*),
  sex,
  group_concat(name)
FROM student3
GROUP BY class, sex;
```

注意group_concat的用法

![snipaste_20170121_153730](http://oaxelf1sk.bkt.clouddn.com/snipaste_20170121_153730.png)

#### 回溯统计

![snipaste_20170121_161239](http://oaxelf1sk.bkt.clouddn.com/snipaste_20170121_161239.png)

![snipaste_20170121_161303](http://oaxelf1sk.bkt.clouddn.com/snipaste_20170121_161303.png)

回溯统计会把每个空的小组也显示到控制台

### Having子句

![snipaste_20170121_161726](http://oaxelf1sk.bkt.clouddn.com/snipaste_20170121_161726.png)

#### 分组统计的结果和统计函数只有having能使用

![snipaste_20170121_161858](http://oaxelf1sk.bkt.clouddn.com/snipaste_20170121_161858.png)

#### having能够使用字段的别名，where不能

![snipaste_20170121_162135](http://oaxelf1sk.bkt.clouddn.com/snipaste_20170121_162135.png)

### Ooder by子句

![snipaste_20170121_162259](http://oaxelf1sk.bkt.clouddn.com/snipaste_20170121_162259.png)

和group by不同，group by值显示一个元素

![snipaste_20170121_162420](http://oaxelf1sk.bkt.clouddn.com/snipaste_20170121_162420.png)

![snipaste_20170121_162509](http://oaxelf1sk.bkt.clouddn.com/snipaste_20170121_162509.png)

### limit子句

![snipaste_20170121_162611](http://oaxelf1sk.bkt.clouddn.com/snipaste_20170121_162611.png)

![snipaste_20170121_162647](http://oaxelf1sk.bkt.clouddn.com/snipaste_20170121_162647.png)

![snipaste_20170121_162656](http://oaxelf1sk.bkt.clouddn.com/snipaste_20170121_162656.png)

![snipaste_20170121_162839](http://oaxelf1sk.bkt.clouddn.com/snipaste_20170121_162839.png)

