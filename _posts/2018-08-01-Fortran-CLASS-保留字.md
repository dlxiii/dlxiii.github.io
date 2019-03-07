---
layout:     post
title:      Fortran：CLASS 保留字
subtitle:   学习 Fortran：CLASS 保留字
date:       2018-08-01
author:     DLXIII
header-img: img/post-bg-hacker.jpg
catalog: true
tags:
    - fortran
    - class keyword
---


## 前言

CLASS 保留字是 TYPE 保留字的变形。TYPE 保留字用来添加特殊属性，对面向对象编程极为重要。在常规的 Fortran 程序中，函数中形参的类型和调用时相应的实参应该完全一致，否则将会引发错误。类似地，指针类型和它所指向的类型也应当一致，否则，也会有错误发生。可分配变量和相应的数据也应是匹配的。
CLASS 保留字以一种特殊的方式放松了这个要求。如果是一个可分配数据项，指针或者形参用 CLASS 保留字声明，而 type 是一个派生数据类型，那么数据项将与数据类型或数据类型的所有扩展相匹配。
举例来说，假设声明了下面两个数据类型。


<!--more-->


~~~ fortran
TYPE :: point
    REAL :: x
    REAL :: y
END TYPE

TYPE, EXTENDS (point) :: point_3d
    REAL :: z
END TYPE
~~~

然后又声明了一个指针

~~~ fortran
TYPE (point) , POINTER :: p
~~~

那么它只能接受 point 类型的数据，而如果是以

~~~ fortran
CLASS (point) , POINTER :: p
~~~

这样的方式声明的指针，无论是 point 类型还是 point_3d 这样的 point 扩展类型的数据都可被接受。

以 CLASS 保留字声明的指针或者形参类型，成为指针或者形参类型的声明类型，而分配给指针或者形参的实际对象被称为动态类型。
因为用 CLASS 保留字声明的数据项可以和一种以上的数据类型相匹配，所以被认为是多态的。
多态指针或形参有一特殊的限制：仅能用他们来访问声明类型的数据项。扩展的数据项不能用多态指针访问。例如，如下的类型定义：

~~~ fortran
CLASS (point) , POINTER :: p
TYPE (point) , TARGET :: p1
TYPE (point_3d) , TARGET :: p2
~~~

变量 p1 和 p2 都能赋值给 p，而且指针 p 还能用来访问 p1 和 p2 中的 x 和 y 分量。但是，p 不能访问 z 分量，原因是 z 变量没有在指针的声明类型中定义。
为了更好理解，可以看如下代码。

~~~ fortran
p => p1
WRITE ( * , * ) p1%x, p1%y               ! These two lines produce the same output
WRITE ( * , * ) p%x, p%y                 ! These two lines produce the same output
p => p2
WRITE ( * , * ) p2%x, p2%y, p2%z         ! Legal
WRITE ( * , * ) p%x, p%y, p%z            ! Illegal - can not access z
~~~

第一行中，指针 p 指向目标变量 p1，第二行和第三行中分别通过原来的变量名和指针来访问 p 的分量，这两行代码都能正常输出。
在接下来第四行中，p 指向目标变量 p2，p2 属于 point_3d 类型。第五行和第六行代码分别使用原来的目标变量名和指针访问 p 的各分量。第五行的代码可以正常输出，但是第六行的代码会产生错误。不能通过一个 point 类的指针来访问分量 z，因为 z 没有在派生类型中定义。

参考：Fortran 95/2003 程序设计
Chap16.3 CLASS 保留字 p633-p634