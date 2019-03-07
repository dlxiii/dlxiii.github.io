---
layout:     post
title:      安装 FVCOM 测试 Estuary
subtitle:   配置并编译 fvcom 并随后试算 Estuary 算例
date:       2018-04-12
author:     DLXIII
header-img: img/post-bg-nsequation.jpg
catalog: true
tags:
    - linux
    - centos
    - fvcom
    - fortran
---


## 前言


在安装 FVCOM 之前，要确保其依赖的软件是否正常工作。
分别检查 Intel Fortran Compiler 和 OpenMPI。

```shell
which icc # c 语言编译器
```

```shell
which ifort # fortran 编译器
```

```shell
which mpicc # 链接 c 语言的 mpi 程序
```

```shell
which mpif90 # 链接 fortran 的 mpi 程序
```

如果没有返回类似的地址，需要重新配置，可翻阅之前的笔记。[Fortran 篇](http://118.25.104.21/index.php/archives/1/ "Fortran 篇")，[OpenMPI 篇](http://118.25.104.21/index.php/archives/8/ "OpenMPI 篇")。

## 获得 FVCOM
由于 FVCOM（版本号：4.1）是几个月前申请的，当时的账号和下载链接现在已经失效，只能将它上传到服务器路径。

1. 通过终端新建远程连接（New Remote Connection）
2. 选择安全文件传输（Secure File Transfer）
3. 设置服务器与登录验证（Server，User）
4. 上传文件
5. 解压

```shell
scp -r user@XXX.XXX.XXX.XXX:/root/works/fvcom4.1.zip /Volumes/GoogleDrive/MyDrive/fvcom4.1.zip
```
```shell
cd /root/works
unzip fvcom4.1.zip
```
## 设置河口算例

* 将河口算例的 make.inc_example 文件拷贝到源代码目录，并修改名称为 make.inc

	```shell
	cp /root/works/FVCOM4.1/Examples/Estuary/make.inc_example /root/works/FVCOM4.1/FVCOM_source/make.inc
    cd /root/works/FVCOM4.1/FVCOM_source
	```
* 编辑make.inc
	* 修改第 51 行，源程序目录`TOPDIR=/root/works/FVCOM4.1/FVCOM_source`
	* 取消第 80，81行的注释符号 #

## 设置函数库

* 进入函数库文件夹，修改 makefile 文件

	```shell
	cd /root/works/FVCOM4.1/FVCOM_source/libs
	vi makefile
	```
* 修改 makefile 第 14 行，第 18 行
	* `cd proj && ./configure CC=$(CC) CFLAGS=-O3 CXX=$(CXX) CXXFLAGS=-O3 F77=$(FC) FFLAGS=-O3 --prefix=$(MYINSTALLDIR)`
	* `cd netcdf && ./configure CC=$(CC) CFLAGS=-O3 CXX=$(CXX) CXXFLAGS=-O3 F77=$(FC) F90=$(FC) FFLAGS=-O3 --prefix=$(MYINSTALLDIR) --build=$(MACHTYPE) --disable-cxx`

* 修改 metis 库

	```shell
	chmod 777 untar.sh
	./untar.sh metis
	cd metis
	vim rename.h
	```
* 修改 rename.h 中第 413 行

	`#define log2                            __intlog2`

## 编译

* 指定编译器后，开始编译

	```shell
	cd ..
    make CC=icc CXX=icpc FC=ifort
	```

* 编译完成检查库文件

	```shell
	cd /root/works/FVCOM4.1/FVCOM_source/libs/install/lib
	ls
	```

## libmetis.a 丢失错误

* 正常情况下，便已完成会生成如下 10 个库文件：

	1. `-rw-r--r-- 1 root root   18712 Mar 22 14:57 libfproj4.a`
	1. `-rw-r--r-- 1 root root  144200 Mar 22 14:58 libjulian.a`
	1. `-rwxr-xr-x 1 root root  712510 Mar 22 14:58 libmetis.a`
	1. `-rw-r--r-- 1 root root 1945686 Mar 22 14:57 libnetcdf.a`
	1. `-rwxr-xr-x 1 root root     935 Mar 22 14:57 libnetcdf.la`
	1. `-rw-r--r-- 1 root root  692816 Mar 22 14:57 libproj.a`
	1. `-rwxr-xr-x 1 root root     832 Mar 22 14:57 libproj.la`
	1. `lrwxrwxrwx 1 root root      16 Mar 22 14:57 libproj.so -> libproj.so.0.5.2`
	1. `lrwxrwxrwx 1 root root      16 Mar 22 14:57 libproj.so.0 -> libproj.so.0.5.2`
	1. `-rwxr-xr-x 1 root root  565120 Mar 22 14:57 libproj.so.0.5.2`

但是检查发现 libmetis.a 丢失，出现了错误，而编译过程中并没有报错，于是需要检查 makefile 中，是否开启 metis 的编译开关。

* 修改 makefile 第 21 行，删除 # 注释符号
	
   ```shell
              cd metis && make install
   ```

* 重新编译，问题解决。

##  ‘ld: cannot find -lnetcdff’ 错误
* 进入 FVCOM_source 文件夹，make 编译 FVCOM
* 发现错误：`ld: cannot find -lnetcdff``make: *** [fvcom] error 1`
	* 这是与 netcdf 相关的错误，先从 makefile 入手，进入 makefile

		```shell
		vi makefile
		```
	* 修改第 97 行，删除找不到的 `-lnetcdff` 为
	  
	  ```shell
	               IOLIBS       =   -lnetcdf #-lhdf5_hl -lhdf5 -lz -lcurl -lm
	  ```
* 重新 make 编译，展示生成的 fvcom 文件 `ll fvcom`，输出如下

	```shell
	-rwxr-xr-x 1 root root 9604248 Apr 11 18:44 fvcom
	```
## 测试河口算例

* 进入河口算例目录

	```shell
	cd /root/works/FVCOM4.1/Examples/Estuary/run
	```
* 链接 fvcom 到 该目录

	```shell
	ln -s ../../../FVCOM_source/fvcom
	```
	可见

	```shell
	0 lrwxrwxrwx 1 root root       27 Mar 22 15:04 fvcom -> ../../../FVCOM_source/fvcom
	```
* 运行算例

	* 1 个物理核心
	
		```shell
		./fvcom --casename=tst
		```
	* 2 个物理核心
	
		```shell
		mpirun -np 2 ./fvcom --casename=tst
		```
* 查看生成结果文件

	```shell
	-rw-r--r-- 1 root root  4096 Apr 11 19:01 tst_0001.nc
	```	
	
