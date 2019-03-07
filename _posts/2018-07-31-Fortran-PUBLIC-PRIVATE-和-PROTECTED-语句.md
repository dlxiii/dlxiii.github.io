---
layout:     post
title:      Fortran：PUBLIC, PRIVATE 和 PROTECTED 语句
subtitle:   学习 Fortran：PUBLIC, PRIVATE 和 PROTECTED 语句
date:       2018-07-31
author:     DLXIII
header-img: img/post-bg-hacker.jpg
catalog: true
tags:
    - fortran
    - save statement
---


## 前言

当使用 USE 关联访问模块时，默认情况下，模块中定义的所有实体时于含有 USE 语句的程序单元都可使用。对于一个被关联的模块，程序可以自由操作处理其中的变量，也可以调用其中的函数。而且，一些在模块中被生命的过程名，它们有可能与程序中的过程名向冲突。
总之，比较好的做法是，限制仅有那些必须知道它们的模块所在程序可以对过程或数据实体进行访问。访问限制越多，错误使用或修改他们的可能性就越小。访问限制使程序更具模块化，也更容易理解和维护。


<!--more-->


如果对某个项使用了 PUBLIC 属性，那么该项对于模块外程序也可访问。
如果对某个项使用了 PRIVATE 属性，那么该项对于模块外程序单元就不可用，但是对于模块中的过程是可访问的。
如果使用 PROTRCTED 属性，那么该项对于模块外程序单元只可读。
任何除了定义该项之外的其他模块试图修改 PROTECTED 变量值都会引起编译错误。（Fortran 2003新特性）

数据项或过程的 PUBLIC, PRIVATE 和 PROTECTED 状态可以用两种方法声明。
**可以在类型定义语句中将此状态作为属性指明，也可以使用独立的 Fortran 语句声明。**

作为类型定义语句的一笔分来声明的例子（此类声明可用于数据项和函数，不能用于子程序）：

~~~ fortran
INTEGER, PRIVATE :: count
REAL, PUBLIC :: voltage
REAL, PROTECTED :: my_data
TYPE (vector), PRIVATE :: scratch_vector
~~~

指明数据项，函数和子程序的语句写法例子：

~~~ fortran
PUBLIC :: list of public item
PRIVATE :: list of private item
PROTECTED :: list of private items
~~~

如果一个模块包括 PRIVATE 语句而没有具体的内容列表，那么默认状态下，模块中的所有数据项和过程都是私有的，任何公用项都必须使用独立的 PUBLIC 语句显式声明。
推荐使用这种方法来设计模块，因为这样仅可以使程序真正需要使用的信息暴露出来。

如下例子（vectors 模块的一部分）所示，vectors 模块需要定义 vector 类型变量，需要执行变量所涉及的操作。
但是关联模块的程序不需要直接访问模块中的子程序或函数。
这里隐藏所有对外部程序单元不必要的项。

~~~ fortran
MODULE vectors
!
!  Purpose:
!    To define a derived data type called vector, and the
!    operations which can be performed on it.  The module
!    defines 8 operations which can be performed on vectors:
!
!                  Operation                     Operator
!                  =========                     ========
!      1.  Creation from a real array               =
!      2.  Conversion to real array                 =
!      3.  Vector addition                          +
!      4.  Vector subtraction                       -
!      5.  Vector-scalar multiplication (4 cases)   *
!      6.  Vector-scalar division (2 cases)         /
!      7.  Dot product                            .DOT.
!      8.  Cross product                            *
!
!    It contains a total of 12 procedures to implement those
!    operations:  array_to_vector, vector_to_array, vector_add,
!    vector_subtract, vector_times_real, real_times_vector,
!    vector_times_int, int_times_vector, vector_div_real,
!    vector_div_int, dot_product, and cross_product.  These
!    procedures are private to the module; they can only be
!    accessed from the outside via the defined operators.
!
!  Record of revisions:
!       Date       Programmer          Description of change
!       ====       ==========          =====================
!     12/15/06    S. J. Chapman        Original code
! 1.  12/16/06    S. J. Chapman        Modified to hide non-
!                                        essential items.
!
IMPLICIT NONE

! Declare all items to be private except for type vector and
! the operators defined for it.
PRIVATE
PUBLIC :: vector, assignment(=), operator(+), operator(-), &
          operator(*), operator(/), operator(.DOT.)

! Declare vector data type:
TYPE :: vector
   REAL :: x
   REAL :: y
   REAL :: z
END TYPE
~~~

**适用于模块中派生数据类型的 PUBLIC 和 PRIVATE 声明。**

（1）模块中声明的派生数据类型元素对于模块外的程序单元可以设置为不可访问，方法是在派生数据类型中加入 PRIVATE 语句。
派生数据类型作为整体仍然可以被外部程序单元访问，但是不能单独访问它的元素。
外部程序单元可以随时声明派生数据类型变量，但是不能单独使用这些变量中的元素。
一个带有私有元素的派生数据类型的例子：

~~~ fortran
TYPE vector
    PRIVATE
    REAL :: x
    REAL :: y
END TYPE
~~~

（2）与上面的情况相反，可以将派生数据类型整体声明为私有。
这种情况下，数据类型 vector 不能被使用模块的任何程序单元访问。
这与前面的例子不同：前面的例子中，数据类型可用，但是元素不能被单独访问。
这种派生数据类型仅适用于模型内部的运算。

~~~ fortran
TYPE, PRIVATE :: vector
    REAL :: x
    REAL :: y
END TYPE
~~~

（3）在 Fortran 2003 中，既可以将派生数据类型的单个元素声明为私有，也可以生命为公有。
在这种情况下，当派生数据类型定义所在的模块位于程序单元之外时，外部程序单元可以随意声明 vector 类型的变量，也可以自由访问元素 x，但是不能访问元素 y。

~~~ fortran
TYPE :: vector
    REAL, PUBLIC :: x
    REAL, PRIVATE :: y
END TYPE
~~~

（4）即使判派生数据类型本身是公有的，仍然可以将该类型的某个变量声明为私有。
在这种情况下，派生数据类型 vector 是公有的，可以被使用模块的程序单元访问。
但是 vec_1 却只能在模块内部使用。
这种类型的声明对于模块内变量的内部运算会有用。

~~~ fortran
TYPE :: vector
    REAL :: x
    REAL :: y
END TYPE
TYPE (vector), PRIVATE :: vec_1
~~~

参考：Fortran 95/2003 程序设计
Chap13.8 限制对模块内容的访问 p498-p501