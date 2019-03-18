---
layout:     post
title:      Fortran：GENERIC 语句
subtitle:   学习 Fortran：GENERIC 语句
date:       2019-03-18
author:     DLXIII
header-img: img/post-bg-fortran90.jpg
catalog: true
tags:
    - fortran
    - generic statement
    - module procedure
    - generic bound procedures
---


## 前言

派生数据类型所绑定的 Fortran 2003 过程同样也可以通用，使用 GENERIC 语句声明这些过程。

~~~ fortran
TYPE point
    REAL :: x
    REAL :: y
CONTA INS
    GENERIC :: add = > point_plus_point, point_plus_scalar
END TYPE point
~~~

通用过程 add 既能识别通过这一绑定声明的两个过程 point_plus_point 和 point_plus_scalar，也能使用元素操作符访问两个过程：p%add()。


## GENERIC 语句
用绑定的通用过程 add 创建一个向量类型。应该有两个特定的过程和通用过程关联，一个用于将两个向量相加，一个用于将向量和标量相加。

用带有绑定通用子程序的模块将向量或标量与另一个向量相加，如下所示。

~~~ fortran
MODULE generic_procedure_module
!
!  Purpose:
!    To define the derived data type for 2D vectors,  
!    plus two generic bound procedures.
!
!  Record of revisions:
!       Date       Programmer          Description of change
!       ====       ==========          =====================
!     12/27/06    S. J. Chapman        Original code
!
IMPLICIT NONE

! Declare type vector
TYPE :: vector
   REAL :: x                         ! X value
   REAL :: y                         ! Y value
CONTAINS
   GENERIC :: add => vector_plus_vector, vector_plus_scalar
   PROCEDURE,PASS :: vector_plus_vector
   PROCEDURE,PASS :: vector_plus_scalar
END TYPE vector

! Add procedures
CONTAINS

   TYPE (vector) FUNCTION vector_plus_vector ( this, v2 )
   !
   !  Purpose:
   !    To add two vectors.
   !
   !  Record of revisions:
   !       Date       Programmer          Description of change
   !       ====       ==========          =====================
   !     12/27/06    S. J. Chapman        Original code
   !
   IMPLICIT NONE
   
   ! Data dictionary: declare calling parameter types & definitions
   CLASS(vector),INTENT(IN) :: this         ! First vector
   CLASS(vector),INTENT(IN) :: v2           ! Second vector

   ! Add the vectors
   vector_plus_vector%x = this%x + v2%x
   vector_plus_vector%y = this%y + v2%y

   END FUNCTION vector_plus_vector

   TYPE (vector) FUNCTION vector_plus_scalar ( this, s )
   !
   !  Purpose:
   !    To add a vector and a scalar.
   !
   !  Record of revisions:
   !       Date       Programmer          Description of change
   !       ====       ==========          =====================
   !     12/27/06    S. J. Chapman        Original code
   !
   IMPLICIT NONE
   
   ! Data dictionary: declare calling parameter types & definitions
   CLASS(vector),INTENT(IN) :: this         ! First vector
   REAL,INTENT(IN) :: s                     ! Scalar

   ! Add the points
   vector_plus_scalar%x = this%x + s
   vector_plus_scalar%y = this%y + s

   END FUNCTION vector_plus_scalar

END MODULE generic_procedure_module
~~~

以上的二维向量模块使用绑定通用过程。

~~~ fortran
PROGRAM test_generic_procedures
!
!  Purpose:
!    To test generic bound procedures.
!
!  Record of revisions:
!       Date       Programmer          Description of change
!       ====       ==========          =====================
!     12/27/06    S. J. Chapman        Original code
!
USE generic_procedure_module 
IMPLICIT NONE

! Enter first point
TYPE(vector) :: v1              ! First vector
TYPE(vector) :: v2              ! Second vector
REAL :: s                       ! Scalar

! Get the first vector
WRITE (*,*) 'Enter the first vector (x,y):'
READ (*,*) v1%x, v1%y

! Get the second vector
WRITE (*,*) 'Enter the second vector (x,y):'
READ (*,*) v2%x, v2%y

! Get a scalar
WRITE (*,*) 'Enter a scalar:'
READ (*,*) s

! Add the vectors
WRITE (*,1000) v1%add(v2)
1000 FORMAT(1X,'The sum of the vectors is (',F8.2,',',F8.2,')')

! Subtract the points
WRITE (*,1010) v1%add(s)
1010 FORMAT(1X,'The sum of the vector and scalar is (',F8.2,',',F8.2,')')

END PROGRAM test_generic_procedures
~~~

## 参考

* [Fortran：CONTAINS 语句](https://dlxiii.top/2018/07/31/Fortran-CONTAINS-%E8%AF%AD%E5%8F%A5/)
* Fortran 95/2003 程序设计：Chap7.3 模块过程 p271，Chap12.9 类型绑定过程 p449-p453，Chap13.5.3 通用绑定过程 p484-p486
