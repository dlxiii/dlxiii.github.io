---
layout:     post
title:      Fortran：有名 I/O 列表，READ 语句
subtitle:   学习 Fortran：有名 I/O 列表，READ 语句
date:       2018-08-09
author:     DLXIII
header-img: img/post-bg-fortran.jpg
catalog: true
tags:
    - namelist i/o
    - fortran
    - read statement
---


## 前言

表控有名列表的 READ 语句通用形式如下。

~~~ fortran
READ (UNIT = unit, NML = nl_group_name, [...] )
~~~

其中 Unit 是将要读取数据的 i/o 单元，而 nl_group_name 是被读的有名列表的名字。当执行表控有名列表的 READ 语句时，程序搜索带有 nl_group_name 的输入文件，它表示有名列表的开始。然后读取有名列表中所有数值，直到碰到字符 “/” 终止 READ。

输入列表的值可以出现在输入文件的任意一行，只要他们在标志 nl_group_name 与字符“/” 之间。根据输入列表给的名字，给列表中变量赋值。


<!--more-->


以下是一个读取的例子。

~~~ fortran
PROGRAM read_namelist

!  Purpose:
!    To illustrate a NAMELIST-directed READ statement.
!
IMPLICIT NONE

! Data dictionary: declare variable types & definitions
INTEGER :: i = 1, j = 2                       ! Integer variables
REAL :: a = -999., b = 0.                     ! Real variables
CHARACTER(len=12) :: string = 'Test string.'  ! Char variables
NAMELIST / mylist / i, j, string, a, b        ! Declare namelist

OPEN (7,FILE='input.nml',DELIM='APOSTROPHE')  ! Open input file.

! Write NAMELIST before update
WRITE (*,'(1X,A)') 'Namelist file before update: '
WRITE (UNIT=*, NML=mylist)

READ (UNIT=7,NML=mylist)                      ! Read namelist file.

! Write NAMELIST after update
WRITE (*,'(1X,A)') 'Namelist file after update: '
WRITE (UNIT=*, NML=mylist)

END PROGRAM read_namelist
~~~

如果 input.nml 包含以下数据

~~~
&MYLIST
I =         -111
STRING = 'Test 1.'
STRING = 'Different!'
B =     123456.
/
~~~

那么，变量b，i，string 会被重新赋值。

参考：Fortran 95/2003 程序设计
Chap14.4 指针和目标变量 p547-p549
