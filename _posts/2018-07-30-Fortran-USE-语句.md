---
layout:     post
title:      Fortran：USE 语句
subtitle:   学习 Fortran：USE 语句
date:       2018-07-30
author:     DLXIII
header-img: img/post-bg-fortran.jpg
catalog: true
tags:
    - fortran
    - use statement
---


## 前言

Fortran 程序或子程序以及函数可以通过模块来交换数据。
模块（module）是一个独立编译的程序单元，包含了共享数据的定义和初始值。
如果程序单元中使用了包含模块名的 USE 语句，即可在程序单元中使用模块定义的数据。


<!--more-->


## 模块

模块以 MODULE 语句来表示开始，语句指明了模块的名字。
模块名最大长度可到 31 个字符（Fortran 2003 中可到 63 个字符），且遵循标准 Fortran 命名规则。
模块以 END MODULE 语句结束，结束语句中可以选择写或不写模块名称。
开始和结束语句之间的声明为共享数据。

如下例子：一个用于程序之间共享数据的简单模块。

~~~ fortran
MODULE shared_data
!
!  Purpose:
!    To declare data to share between two routines.

IMPLICIT NONE
SAVE
INTEGER, PARAMETER :: num_vals = 5         ! 数组维度最大值
REAL, DIMENSION(num_vals) :: values        ! 数值

END MODULE share_data
~~~

## USE 语句

USE 语句必须出现在程序单元中的其他语句之前（除PROGRAM 或 SUBROUTTNE 语句）。
用 USE 语句来访问模块中的数据称为 USE 关联。

**USE 语句的格式如下：**

~~~ fortran
USE module_name
~~~

如下例子：使用`模块 shared_data`在主程序和子程序之间共享数据。

~~~ fortran
PROGRAM test_module
!
!  Purpose:
!    To illustrate sharing data via a module.
!
USE shared_data               ! 使共享数据可见
IMPLICIT NONE

REAL, PARAMETER :: PI = 3.141592  ! Pi

values = PI * (/ 1., 2., 3., 4., 5. /)

CALL sub1                     ! 调用子程序 sub1

END PROGRAM test_module

!*****************************************************************
!*****************************************************************

SUBROUTINE sub1
!
!  Purpose:
!    To illustrate sharing data via a module.
!
USE shared_data               ! 使共享数据可见
IMPLICIT NONE

WRITE (*,*) values

END SUBROUTINE sub1
~~~

`模块 shared_data`的内容被主程序子程序共享。

模块时于在很多个程序单元间共享大量数据是非常有用的。
模块中 SAVE 语句来确保使用时不修改模块的内容。

## USE 语句的高级选项

当程序单元通过使用 USE 关联访问模块时，默认的，该程序能访问的模块中的每个数据项、接口和过程。
可以跑过持某些数据项声明为 PRIVATE 来限定程序的访问。
除了这种方法之外，还可以对使用模块的程序单元进一步限定所使用的数据项表，并且修改这些数据项的名字。

为了限制对模块中特定数据项的访问， 可以将 ONLY 子句添加到 USE 语句中。
**ONLY 子句形式如下：**

~~~ fortran
USE module_name, ONLY: only_list
~~~

其中 module_name 是模块名，only_Iist 是模块中要使用的数据项表，如果有多个数据项，之间用逗号分开。


参考：Fortran 95/2003 程序设计
chap 7.2 用模块共享数据，p264-p266
chap 13.9 USE 语句的高级选项，p501-p502
