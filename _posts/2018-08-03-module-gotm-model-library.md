---
layout:     post
title:      module gotm_model_library
subtitle:   学习 module gotm_model_library
date:       2018-08-03
author:     DLXIII
header-img: img/post-bg-hacker.jpg
catalog: true
tags:
    - fortran
    - module gotm_model_library
    - fabm
    - gotm
---


## 前言

在 FABM 模型中，`src/models` 是包含众多耦合模型信息的路径。
对于任何一个模型，其中必不可少的是 INSTITUTE_model_library.F90。
创建模型时，该文件是 FABM 的起点。


<!--more-->


~~~ fortran
module INSTITUTE_model_library

   use fabm_types, only: type_base_model_factory,type_base_model

   ! Add use statements for new models here

   implicit none

   private

   type,extends(type_base_model_factory) :: type_factory
      contains
      procedure :: create
   end type

   type (type_factory),save,target,public :: INSTITUTE_model_factory

contains

   subroutine create(self,name,model)

      class (type_factory),intent(in) :: self
      character(*), intent(in) :: name
      class (type_base_model),pointer :: model

      select case (name)
      ! Add case statements for new models here
      end select

   end subroutine create

end module
~~~

构建模型依赖其中的 `INSTITUTE_model_library` 和 `INSTITUTE_model_factory` 。对于 GOTM 模型，`INSTITUTE`需要修改为 `gotm`，即  `gotm_model_library` 和 `gotm_model_factory`。

`module gotm_model_library`代码如下。


~~~ fortran
module gotm_model_library

   use fabm_types, only: type_base_model_factory,type_base_model

   implicit none

   private

   type,extends(type_base_model_factory) :: type_factory
      contains
      procedure :: create
   end type

   type (type_factory),save,target,public :: gotm_model_factory

contains

   subroutine create(self,name,model)

      use gotm_npzd
      use gotm_fasham
      use gotm_ergom
      use gotm_light
      ! Add new GOTM models here

      class (type_factory),intent(in) :: self
      character(*),        intent(in) :: name
      class (type_base_model),pointer :: model

      select case (name)
         case ('npzd');   allocate(type_gotm_npzd::model)
         case ('fasham'); allocate(type_gotm_fasham::model)
         case ('ergom');  allocate(type_gotm_ergom::model)
         case ('light');  allocate(type_gotm_light::model)
         ! Add new GOTM models here
      end select

   end subroutine

end module
~~~

（有份铅笔字迹的笔记，附在程序后面，其中疑惑的地方记得之后再慢慢完善。）