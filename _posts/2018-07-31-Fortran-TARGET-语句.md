---
layout:     post
title:      Fortran：TARGET 语句
subtitle:   学习 Fortran：TARGET 语句
date:       2018-08-01
author:     DLXIII
header-img: img/post-bg-hacker.jpg
catalog: true
tags:
    - fortran
    - target statement
---


## 前言

欲将 Fortran 变量声明为指针型，可以在变量声明语句中包含 POINTER 属性来实现，也可以将它单独列在一个 POINTER 语句中。
例如，下面的语句都声明了指针 p1，它必须指向一个实型变量。

~~~ fortran
REAL, POINTER :: p1
~~~

或者

~~~ fortran
REAL :: p1
POINTER :: p1
~~~


<!--more-->


即使指针不包含任何类型的数据，也必须声明指针的类型。它包含的是某个某种类型的变量的地址。只允许指针指向与其声明类型一致的变量。任何将指针指向不同类型变量的做法都会产生编译错误。
指向派生数据类型变量的指针同样也需要声明，例如。

~~~ fortran
TYPE (vector) , POINTER :: vector_pointer
~~~

声明了指向派生数据类型 vector 的变量。指针同样也可指向数组。使用预定义结构数组说明声明数组指针，意思是已经制定了数组的维度，但是数组每维的宽度用冒号指明。
如指向数组的两个指针。

~~~ fortran
INTEGER, DIMENSION ( : ) , POINTER :: ptr1
REAL, DIMENSION ( : , : ) , POINTER :: ptr2
~~~

第一个指针可以指向任何一维的整型数组，第二个指针能指向任何二维的实型数组。

## TARGET 语句

指针可以指向任何同类型的变量或数组，只要该变量或数组被声明为目标变量即可。TARGET 是一种数据对象，其地址在使用指针时可用。可以通过在 Fortran 变量或数组的类型定义语句中包括 TARGET 属性来将其声明为目标变量，也可以将变量或者数组列在单独的 TARGET 语句中。
例如，下列语句集都声明了两个能指向目标变量的指针。

~~~ fortran
REAL, TARGET :: a1 = 7
INTEGER, DIMENSION (10) , TARGET :: int_array
~~~

或者

~~~ fortran
REAL :: a1 = 7
INTEGER, DIMENSION (10)  :: int_array
TARGET :: a1, int_array
~~~

它们声明了一个实型的标量 a1 和一个一维的整型数组 int_array。可以使用任何实型标量指针（如上名声明的 p1）指向变量 a1，使用任何类型的一维指针（如上面声明的 ptr1）指向int_array。

参考：Fortran 95/2003 程序设计
Chap15.1 指针和目标变量 p574-p575