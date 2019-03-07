---
layout:     post
title:      Fortran：SAVE 语句
subtitle:   学习 Fortran：SAVE 语句
date:       2018-07-31
author:     DLXIII
header-img: img/post-bg-fortran90.jpg
catalog: true
tags:
    - fortran
    - save statement
---


## 前言

根据 Fortran 95/2003 的标准，当退出过程后，过程中的所有局部变量和数组的值都会成为未定义的值。
Fortran 提供了一种方式来保证在调用过程之间不修改保存的问部变量和数组：SAVE 属性。


<!--more-->


## SAVE 语句

SAVE 属性出现在类型声明语句中。
在调用过程间，任何一个用 SAVE 属性定义的局部变量都会被保存。
另外，Forttran 也提供了SAVE 语句。
它是一个位于过程的声明部分的非执行语旬， 跟在类型声明语句后面。
任何列在 SAVE 语句中的局部变量都会在调用过程间无改变地保存。
如果 SAVE 语句中没有变量，耶么所有的局部变量都会被无改变的保存。

**SAVE 语句的格式如下：**

~~~ fortran
SAVE :: var1, var2, ...
~~~

或简写为：

~~~ fortran
SAVE
~~~

任意共享数据的模块都应该使用 SAVE 语句，这样可以确保模块中的数值在调用过程间保持完整。
这些过程通过 USE 语句使用该模块。

参考：Fortran 95/2003 程序设计
chap 9.2 SAVE 属性和语句，p346-p347
