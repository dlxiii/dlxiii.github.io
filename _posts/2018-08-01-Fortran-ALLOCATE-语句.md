---
layout:     post
title:      Fortran：ALLOCATE 语句
subtitle:   学习 Fortran：ALLOCATE 语句
date:       2018-08-01
author:     DLXIII
header-img: img/post-bg-hacker.jpg
catalog: true
tags:
    - fortran
    - allocate statement
---


## Fortran 95 可分配数组

Fortran 95 在类型声明语句中使用 ALLOCATABLE 属性来声明动态分配内存的数组，它使用 ALLOCATE 语句实际分配内存。当程序使用完内存后，应该使用 DEALLOCATE 语句释放内存，以供其他用户使用。一个典型的用 ALLOCATABLE 属性定义数组的结构如下。

~~~ fortran
REAL, ALLOCATABLE, DIMENSION ( : , : ) :: arr1
~~~


<!--more-->


因为不知道数组的实际大小，所以在声明中使用冒号作为占位符。在类型声明语句中定义的是数组的维数而不是数组的大小。
当执行程序的时候， 数组的实际大小通过 ALLOCATE 语句来指定。ALLOCATE 语句的格式如下。

~~~ fortran
ALLOCATE (list of arrays to allocate, STAT = status)
~~~

一个典型的例子：

~~~ fortran
ALLOCATE (arr1 (100, 0 : 10), STAT = status)
~~~

这条语句在执行时分配数组 arr1 为一个 100*11的数组。 STAT 语句可选。

在使用可分配数组的程序或过程的最后应释放内存，使得这些内存可以再次使用。
通过 DEALLOCATE 语句可以实现。

~~~ fortran
DEALLOCATE (list of arrays to allocate, STAT = status)
~~~

## Fortran 2003 可分配数组

Fortran 2003 的 ALLOCATE 语句格式如下。

~~~ fortran
ALLOCATE (list of arrays to allocate, STAT = status, ERRMSG = err_msg)
ALLOCATE (array to allocate, SOURCE = source_expr, STAT = status, ERRMSG = string)
~~~

第二种 ALLOCATE 语句的一个实例。

~~~ fortran
ALLOCATE (list of arrays to allocate, STAT = status, ERRMSG = err_msg)
ALLOCATE (arr1, arr2, STAT = status, ERRMSG = err_msg)
~~~

这条语句为数组 arr1 分配和数组 arr2 同样大小和结构的空间，arr1 的初始化值和 arr2 相同。这种格式的 ALLOCATE 语句一次只能分配一个数组，被分配的数组必须和原数组或表达式相一致。在这种情况下，状态和错误信息子句与其他格式的 ALLOCATE 语句是相同的。
因为被分配的数组和表达式是一致的，所以下面的代码有效。

~~~ fortran
REAL, DIMENSION ( : , : ), ALLOCATABLE :: arr1
REAL, DIMENSION (2, 2), :: arr2 = RESHAPE((/1, 2, 3, 4/), (/2, 2/))
INTEGER :: istat
CHAEACTER (len = 80) :: err_msg
...
ALLOCATE (arr1, SOURCE = arr2 + 2, STAT = istat, ERRMSG = err_msg)
~~~

表达式 arr2 + 2 是一个 2*2 数组，它的值如下。
[3 5]
[4 6]
所以 arr1 分配为一个 2* 2数组，初始化为上述的值。

## 在赋值语句中使用 Fortran 2003 的可分配数组

因为可以在正常的程序语句中自动分配和释放，所以 Fortran 2003 的可分配数组要比早期版本更加复杂也有用。
加入要将一个表达式赋给一个同样维数的 Fortran 2003 可分配数组，如果该数组没有分配，那么将自动为数组分配正确的结构，或者如果该数组先分配的结构和要求不一致，系统将会自动地释放空间，并重新分配给他一个正确的结构。
例如，研究下面语句。

~~~ fortran
REAL, DIMENSION ( : ), ALLOCATABLE :: arr1
REAL, DIMENSION (8), :: arr2 = (/1., 2., 3., 4., 5., 6., 7., 8./)
REAL, DIMENSION (3), :: arr3 = (/1., -2., 3./)
...
arr1 = 2. * arr3
WRITE (*, *) arr1
arr1 = arr2 (1: 8: 2)
WRITE (*, *) arr1
arr1 = arr2 (1: 4)
WRITE (*, *) arr1
~~~

当执行第一条赋值语句的时候，arr1 并没有分配空间，系统自动的为它分配一个3元素的数组，值为 [2. -4. 6.]。
当执行第二条赋值语句的时候，因为 arr1 是一个3元素的数组，大小是错误的，所以 arr1 的空间被释放掉，重新分配一个4元素的数组，存入的值为 [1. 3. 5. 7.]。
当执行第三条赋值语句的时候，arr1 已经是被分配4元素的数组，这个大小是正确的，所以不再为数组重新分配空间，新的数值将被放入数组空间中。
可分配变量和将要赋值给它的表达式有相同的维数，才会自动进行分配和释放的操作。如果维数不同，赋值语句会产生编译错误。

## 派生数据类型的动态内存分配

在 Fortran 2003 中， 声明派生数据类型的变量或数组时可以使用 ALLOCATABLE 属性，也即可以得到动态分配和回收。例如，假设使用如下语句定义了一个派生数据类型。


~~~ fortran
TYPE :: personal_info
    CHARACTER (len = 12) :: first            ! First name
    CHARACTER                  :: mi              ! Middle name
    CHARACTER (len = 12) :: last             ! Last name
    CHARACTER (len = 26) :: street         ! Street address
    CHARACTER (len = 12) :: city             ! City
    CHARACTER (len = 2)   :: state           ! State
    INTEGER                       :: zip              ! Zip code
END TYPE personal_info
~~~

那么就可以用如下的方式声明能分配内存的此类型变量。

~~~ fortran
TPYE (personal_info) , ALLOCATABLE :: person
~~~

可以用如下语句分配内存。

~~~ fortran
ALLOCATE (PERSON, STAT = istat)
~~~

同样，可以用如下的方式声明能分配内存的此类型数组。

~~~ fortran
TPYE (personal_info) , DIMENSION ( : ) , ALLOCATABLE :: people
~~~

可以用如下语句分配内存。

~~~ fortran
ALLOCATE (people (1000), STAT = istat)
~~~

## 使用指针的动态内存分配

指针能够在需要的时候动态创建变量或数组，同时在使用完毕后释放动态数组或变量所使用的空间。
ALLOCATE 语句的形式和可分配数组的 ALLOCATE 语句形式相同。

~~~ fortran
ALLOCATE (pointer (size), [...,] STAT = status)
~~~

其中，pointer 是指向所创建的变量或数组的指针名，如果创建的对象是数组，那么 size 是维度，status 是操作的结果。如果分配成功，那么为 0，如果失败，那么值会是一个与处理机相关的正数。

参考：Fortran 95/2003 程序设计
Chap8.6 可分配数组 p317-p323
Chap12.6 派生数据类型的动态内存分配 p446-p447
Chap15.4 使用指针的动态内存分配 p582-p585