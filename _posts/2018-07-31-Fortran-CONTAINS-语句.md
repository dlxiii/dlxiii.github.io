---
layout:     post
title:      Fortran：CONTAINS 语句
subtitle:   学习 Fortran：CONTAINS 语句
date:       2018-07-31
author:     DLXIII
header-img: img/post-bg-fortran.jpg
catalog: true
tags:
    - fortran
    - contains statement
    - module procedure
---


## 前言

除了数据，模块还含有完整的子程序和函数，它们被称作模块过程（module procedure）。
这些过程被作为模块的一部分进行编译，并且通过在程序单元中使用包含模块名的 USE 语句，从而可以使模块过程在程序单元中有效。
包含在模块中的过程必须跟在模块的数据声明之后，前面加上 CONTAINS 语句。


<!--more-->


## CONTAINS 语句
CONTAINS 语句告诉编译器下面的语句被包含在过程中。

一个简单的模块过程例子，其中子程序 sub1 在模块 my_subs中。

~~~ fortran
MODULE my_subs
IMPLICT NONE

(Declare shated data here)

CONTAINS
    SUBROUTINE sub1 (a, b, c, x, error)
    IMPLICT NONE
    REAL, DIMENSION (3), INTENT (IN) :: a
    REAL, INTENT (IN) :: b, c
    REAL, INTENT (OUT) :: errer
    ...
    END SUBROUTINE sub1
END MODULE my_subs
~~~

如果程序单元的第一个非注释语句是 USE my_subs，那么调用程序单元中可以有效使用子程序 sub1。
像如下代码一样，可以用标准 CALL 语句调用子程序。

~~~ fortran
PROGRAM main_prog
USE my_subs
IMPLICT NONE
...
CALL sub1 (a, b, c, x, error)
...
END PROGRAM main_prog
~~~

## 类型绑定过程

Fortran 2003 允许过程被专门关联于某个派生数据类型，并通过引用派生数据类型来调用这些过程。
通过在类型定义中添加 CONTAINS 语句和在语句中声明绑定，创建类型与 Fortran 过程的绑定。
比如，假设想要开发一个函数，它实现 point 类型两个数据项的加法。
那么可以按下面的方法定义类型。

~~~ fortran
TYPE : point
    REAL :: x
    REAL :: y
CONTAINS
    PROCEDURE, PASS :: add
END TYPE
~~~

这个定义声明了一个过程，被称为 add，它关联于这一数据类型。
如果 p 是 point 类型变量，那么 add 过程的引用就可以写作 p%add (...)，这和 p%x 的访问规则一样。
PASS 属性指明调用此过程的 point 类型变量会被当成第一调用参数自动传递到这一过程，不论调用时发生在什么时候。
然后，过程 add 需要当作为类型定义语句在同一模块中定义。
下面给出一个模块，它含有声明 point 类型和过程 add。

~~~ fortran
MODULE point_module
IMPLICIT NONE

! Type definition
TYPE :: point
    REAL :: x
    REAL :: y
CONTAINS
    PROCEDURE, PASS :: add
END TYPE

CONTAINS
    TYPE (point) FUNCTION add (this, another_point)
    CLASS (point) :: this, another_point
    add%x = this%x + another_point%x
    add%y = this%y + another_point%y
    END FUNCTION add
END MODULE point_module
~~~

函数 add 有两个参数： this 和 another_point。
其中参数 this 是用来调用过程的变量，当调用 add 时，当参数 another_point 出现在调用参数表中时， this 自动传递到过程，而无需在调用中对它进行显式地说明。


参考：Fortran 95/2003 程序设计
Chap7.3 模块过程 p271
Chap12.9 类型绑定过程 p449-p453
