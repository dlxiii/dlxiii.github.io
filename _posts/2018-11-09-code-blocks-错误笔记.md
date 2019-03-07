---
layout:     post
title:      code::blocks 错误笔记
subtitle:   code::blocks 错误 signal SIGSEGV: Segmentation fault - invalid memory reference
date:       2018-11-09
author:     DLXIII
header-img: img/post-bg-hacker.jpg
catalog: true
tags:
    - gfortran
    - fortran
    - code::blocks
    - segmentation fault
---


## 前言

在代码正确的情况下读取文件出现如上错误，是 GNU Fortran 的问题。

> Please state the GCC and/or FORTRAN compiler version for both working
> and non-working versions. Some GCC Compilers have know FORTRAN bugs!
> 
> "GNU Fortran (tdm-1) 5.1.0" is the bad FORTRAN compiler（mingw32-gfortran.exe "GNU Fortran (tdm-1) 5.1.0"）.

目前code::blocks 版本使用的正是该版本。


<!--more-->


可以使用新版本替换。

方法如下：

https://sourceforge.net/projects/mingw/files/MinGW/Base/gcc/Version6/ with this replaced compiler.

(1) download small installer from the above link and install it in c:\mingw
(2) select the fortran package from the fresh installer and install
(3) change in the setting-complier for GNU fortran path to c:\mingw

参考：http://forums.codeblocks.org/index.php?topic=22683.0