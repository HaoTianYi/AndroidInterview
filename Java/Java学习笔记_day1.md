# Java学习笔记_day1

[TOC]

## 计算机基础

常见键盘
Ctrl  kangchou   tab   teibou     shift      alt    aiout

常用快捷键
全选	Ctrl+A
		复制	Ctrl+C
		粘贴	Ctrl+V
		剪切	Ctrl+X
		撤销	Ctrl+Z
		保存	Ctrl+S

## 打开doc窗口的三种方式

1：开始--程序--附件--命令提示符
2：开始--运行—cmd—回车
3：win+r—cmd--回车

## 常用DOC命令

a)	d: 回车	c
b)	dir(directory):列出当前目录下的文件以及文件夹
c)	md (make directory) : 创建目录
d)	rd (remove directory): 删除目录
e)	cd (change directory)改变指定目录(进入指定目录)
f)	cd.. : 退回到上一级目录
g)	cd\: 退回到根目录
h)	del (delete): 删除文件,删除一堆后缀名一样的文件*.txt
i)	exit : 退出dos命令行
j)	cls : (clear screen)清屏a
l） notepad ：打开记事本
h） mspaint：打开画图    //可以用运行打开
g） calc：打开计算器

## java涉及的概念

​	因为有了JVM，所以同一个Java程序在三个不同的操作系统中都可以执行。这样就实现了Java程序的跨平台性。也称为Java具有良好的可移植性。
JRE(Java Runtime Environment    Java运行环境)
​	包括Java虚拟机(JVM Java Virtual Machine)和Java程序所需的核心类库等，如果想要运行一个开发好的Java程序，计算机中只需要安装JRE即可。
​	JDK(Java Development Kit    Java开发工具包)
​	JDK是提供给Java开发人员使用的，其中包含了java的开发工具，也包括了JRE。所以安装了JDK，就不用在单独安装JRE了。

其中的开发工具：编译工具(javac.exe)  打包工具(jar.exe)等
简单而言：使用JDK开发完成的java程序，交给JRE去运行，由JVM保证跨平台。

##JAVA环境作用
path环境变量的作用
	保证javac命令可以在任意目录下运行。
classpath环境变量的作用
	保证class文件可以在任意目录下运行