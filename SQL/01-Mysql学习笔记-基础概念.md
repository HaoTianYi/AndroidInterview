# 01-Mysql学习笔记-基础概念

[TOC]

## 基本概念

![sp170111_091506](http://oaxelf1sk.bkt.clouddn.com/sp170111_091506.png)

## 关系型数据库

1. 定义：关系型数据库是一种建立在关系模型上的数据库
2. 关系模型（背）：
   1. 数据结构：数据的存储问题（二维表）
   2. 操作指令集：SQL语句
   3. 完整性约束：表内数据约束、表之间的数据约束
3. 关系型数据库的设计：
   1. 找出系统中所存在的实体
   2. 找出实体（表）中应该存储的信息（字段名）

## 教学系统实例

### 描述实体内部的联系：

![sp170111_093638](http://oaxelf1sk.bkt.clouddn.com/sp170111_093638.png)

发现第二列中只能放置性别（内部约束），每一行都是在描述每一个学生（内部联系）

关系型数据库的特点之一：浪费空间

关系型数据库的存储结构是**二维表**

![sp170111_093959](http://oaxelf1sk.bkt.clouddn.com/sp170111_093959.png)

### 描述实体之间的关系：
![sp170111_094713](http://oaxelf1sk.bkt.clouddn.com/sp170111_094713.png)

## 关键字

数据库：database

DBS(Database System)数据库系统

DBS = DBMS(Database Management System数据库管理系统) + DB（Database）

DBA = Database Administrator数据库管理员  

行/记录：row/record 一行（=元组，或记录）是一组相关的数据，例如一条用户订阅的数据，行是从表的结构出发，记录是从数据角度出发

列/字段：column/field： 一列(数据元素) 包含了相同的数据, 例如邮政编码的数据，类也是从表的结构出发，字段是从数据角度出发

## SQL语句

SQL：Structured Query Language（结构化查询语言）

SQL分类（背）：

1. DDL：Data Definition Language：数据定义语言，用来维护数据的结构。如：create、drop（删除）、alter（修改）
2. DML：Data Manipulation Language：数据操作语言，用来对数据进行操作。如：insert、delete、update。又分成部分：DQL Data Query Language：结构查询语言，如：select
3. DCL ：Data Control Language：数据控制语言，主要是负责权限管理，如：grant（分配权限）、revoke（回收权限）

SQL是所有的关系型数据库的操作指令，是一种约束，但不强制，不同的数据产品可能有差异

## SQL服务器对象

Mysql服务器分成四个部分：系统（DBMS）-> 数据库（DB）-> 数据表（Table）-> 字段（field） 

![sp170111_114944](http://oaxelf1sk.bkt.clouddn.com/sp170111_114944.png)

## SQL基本操作

按照操作对象进行分类：库操作、表操作、字段操作

### 库操作

就是面向数据库的增删改查

- 新增数据库

`create database 名字[库选项] ；`

库选项包括两个部分：

1. 字符集设定：charset/charseter set 具体字符集（数据的编码格式）：常见字符集：GBK和UTF8
2. 校对集合设定：collate（数据的比较规则）

示例代码：

`create database demo charset utf8；`

-- 表示单行注释  # 也可以表示单行注释

SQL报错只是会报告大概位置，不会说明报错原因（静默模式），中文数据库是可以但是要保证服务器可以识别中文  

------------------

解决方案：首先设置当前中文的字符集是什么：

`set names gbk;`

`create database 中国 charset utf8；`

这样则可以创建中文的数据库名字

---------------------

执行创建数据库的SQL语句之后服务器执行过程？

1. 在数据库的系统之中，增加了对应的数据库信息
2. 在服务器1目录中增加了对应的数据，在win10中的目录：

![sp170111_130815](http://oaxelf1sk.bkt.clouddn.com/sp170111_130815.png)

3. 每一个数据库中个都有db.opt文件中含有字符集和校对集


![sp170111_131344](http://oaxelf1sk.bkt.clouddn.com/sp170111_131344.png)

------------

- 查看数据库

1. 查看所有数据库`show databases;`

2. 查看指定的数据库：模糊查询   %匹配多个 _ 匹配耽搁字符；实例

   `SHOW DATABASES LIKE '_emo';`注意这个符号是'',如果要查询的是_那么则要添加\来转义

3. 查看数据库的创建语句`SHOW CREATE DATABASE demo;`系统在创建之后会优化语句

- 更新数据库

1. 数据库名字不可以更改
2. 数据库的修改仅仅是库选项（字符集和校对集）

实例代码：

```
ALTER DATABASE demo CHAR SET gbk;
```

![sp170111_142946](http://oaxelf1sk.bkt.clouddn.com/sp170111_142946.png)

校对集同时发生改变---->校对集依赖于字符集

- 删除数据库

```
DROP DATABASE demo;
```

------------

数据库的删除在服务器中的实质：

1. 在数据库的视窗中看不到数据库
2. 数据库对应的文件夹也被删除

### 表操作

- 新增

```
create table [if not exists] (字段名称 数据类型，字段名称 数据类型)[表选项]；
```

表选项：字符集---确定字段的字符集；校对集；存储引擎

![sp170111_160244](http://oaxelf1sk.bkt.clouddn.com/sp170111_160244.png)

示例代码：

```
USE demo;
CREATE TABLE class(name VARCHAR(10),age VARCHAR(10)) CHAR SET utf8;
```

-----------

新建表服务器发生的实质：

1. 指定数据库存在的表
2. 在数据库对应的文件夹中产生具体的文件，如class.frm（和具体的引擎有关）

![sp170111_160506](http://oaxelf1sk.bkt.clouddn.com/sp170111_160506.png)

- 查看数据表

1. 查看所有的表：`SHOW TABLES ;`
2. 查看部分表：模糊匹配（和数据库类似）  注意\g 等价于分号
3. 查看创建表的语句：`SHOW CREATE TABLE class`
4. 查看表中的信息，三种方式
   1. desc 表名字
   2. describe 表名字；
   3. show colums from 表的名字；

- 修改数据表

分成表本身和字段两种

表的名字一般是数据库的前两个_表名；

```
RENAME TABLE class TO de_class;
```

修改表选项：

```
ALTER TABLE de_class CHAR SET utf8;-- 改变字符集
SHOW CREATE TABLE de_class;   -- 显示字符集
```

**修改字段**

alter  table 表名 add 列明 数据结构 位置

```
ALTER TABLE de_class ADD id INT FIRST ;
DESC de_class;
```

位置可以是first（第一个）after +字段名 （在xx之后）

```sql
ALTER TABLE de_class ADD ids INT AFTER id;
DESC de_class;
```

---------------

修改字段：

alter table 表名 modify 字段名 数据类型 [属性\] \[位置]

其中属性表示是否可以为空等，位置和上面一样

```
ALTER TABLE de_class MODIFY ids INT AFTER age;
DESC de_class;
```

-----------------

重命名字段：

alter table 表名 change 旧字段名 新字段名 数据类型 [属性\]\[位置]

```
ALTER TABLE de_class CHANGE ids idds INT ;
DESC de_class;
```

-----------

删除字段：

alter table 表名 drop 字段；

```
ALTER TABLE de_class DROP idds;
DESC de_class;
```

会删除该字段的所有数据

-------------

删除数据表：

drop table 表名，表名；

```
DROP TABLE de_class;
USE demo;
SHOW TABLES ;
```

服务器的核心变化：

1. 在表空间没有指定的表
2. 在数据库对应的文件夹中删除文件

### 数据操作

#### 新增数据字段

两种方案：

![sp170111_164228](http://oaxelf1sk.bkt.clouddn.com/sp170111_164228.png)

```
INSERT INTO class2 VALUES ("10","dd"),("dd","pp"),("qq","oo");
```
![sp170111_164636](http://oaxelf1sk.bkt.clouddn.com/sp170111_164636.png)

```
INSERT INTO class2 (name, age) VALUES ("44","88");
SELECT *
FROM class2;
```
#### 查看字段

select */字段类表 from 表名 [where 条件];

查看所有数据字段：

```
SELECT *
FROM class2;
```

查看指定条件：

```
SELECT name,age FROM class2 WHERE name="44";
```

#### 更新字段

update 表名 set 字段 = 值 [where 条件]；

```
UPDATE class2 SET age = "iiiii" WHERE name = "10";
SELECT *
FROM class2;
```

更新不一定会成功

#### 删除字段

delete from 表名[where 条件]

```
DELETE FROM class2 WHERE age = "pp";
SELECT *
FROM class2;
```

