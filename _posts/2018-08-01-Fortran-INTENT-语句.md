---
layout:     post
title:      Fortran：INTENT 语句
subtitle:   学习 Fortran：INTENT 语句
date:       2018-08-01
author:     DLXIII
header-img: img/post-bg-hacker.jpg
catalog: true
tags:
    - fortran
    - intent statement
---


## INTENT属性

子程序的形参可以与一个 INTENT 属性联合使用。INTENT 属性与类型声明语句联合使用，来声明每个形参的类型。这个属性可以写成3钟格式中的任何一种。

~~~ fortran
INTENT (IN)                 ! 形参仅用于向子程序传递输入数据
INTENT (OUT)                ! 形参仅用于将结果返回给调用程序
INTENT (INOUT)              ! 形参既用来向子程序输入数据，也用来向调用程序返回结果
或 INTENT (IN OUT)
~~~


<!--more-->


INTENT 属性的作用是告诉编译器，打算如何使用形参。一些参数的作用可能是用来给子程序提供程序输入，另一些可能只是用来从子程序返回结果。当然还有一些参数可能即用来传递数据，也用来返回结果。对于每一个参数来说，都应该声明一个合适的 INTENT 属性。

一旦编译器知道了打算如何使用每个形参，在编译的时候，他就可以利用这些信息来捕捉编程中出现的错误。例如，假设一个子程序偶然改动了输入数据，这种对输入参数的修改会影响调用程序中对应变量的值，且变动过的值将用于所有后序处理中。

一个简单的例子，子程序 sub1 计算一个输出值，同时它“偶然”修改了输入值。

~~~ fortran
SUBROUTINE sub1(input,output)

REAL, INTENT(IN) :: input
REAL, INTENT(OUT) :: output

output = 2. * input
input = -1.

END SUBROUTINE sub1
~~~

通过声明每个形参的用途，编译器可以在编译的时候发现这种错误。

Error: There is an assignment to adummy sysmble with the exolicit INTENT (IN) attribute [INPUT]

## INTENT语句


~~~ fortran
INTENT (intent_type) :: name1, name2, ...
~~~

例如：

~~~ fortran
INTENT (IN) :: a, b
INTENT (OUT) :: result
~~~

该语句声明制定过程中形参的作用，intent_type 可以取值 IN，OUT和 INOUT。仅有形参可以出现在 INTENT 语句中。

参考：Fortran 95/2003 程序设计
Chap7.1.2 INTENT属性 p251-p253