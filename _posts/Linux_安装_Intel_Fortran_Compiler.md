---
layout:     post
title:      Linux 安装 Intel Fortran Compiler
subtitle:   在 CentOS 上安装 Fortran 编译器
date:       2018-04-10
author:     DLXIII
header-img: img/post-bg-hacker.jpg
catalog: true
tags:
    - linux
    - centos
    - fortrian
    - intel
---


## 前言

不出意外，在配置 CentOS 系统后将要安装 Fortran 编译器。

## 安装前配置

编写程序块时，可参阅 [Markdown 语法高亮](http://www.cnblogs.com/qyf404/p/5019631.html)。
### 升级系统并安装 gcc 环境

```shell
yum update
yum install gcc-c++
```
<!--more-->

### 在根目录下新建文件夹
分别新建文件夹并命名为 downloads，tests，和 works。

```shell
mkdir downloads
mkdir tests
mkdir works
```
### 下载 Intel Fortran Compiler 并解压

* iFort 序列号是以学生身份申请并免费使用。
* 下载到 `downloads/` 目录。
* WGET：Linux上的下载命令，`wget 链接`。[^1]
[^1]: Linux用户宝典：用于下载的十大命令行工具: [http://os.51cto.com/art/201605/511423.htm]("Linux用户宝典：用于下载的十大命令行工具")

* 执行以下命令：

```shell
wget http://registrationcenter-download.intel.com/akdlm/irc_nas/tec/12374/parallel_studio_xe_2018_update1_cluster_edition.tgz
```

* 下载完成后移动到 `downloads` 目录：

```shell
mv parallel_studio_xe_2018_update1_cluster_edition.tgz downloads
```
### 安装 Intel Fortran Compiler

* 解压 `*.tgz ` 文件，并安装。

```shell
cd downloads/
tar -xzvf parallel_studio_xe_2018_update1_cluster_edition.tgz
cd parallel_studio_xe_2018_update1_cluster_edition/
./install.sh
```
* 激活过程中，如果手动输入激活码后因网络不佳 time out， 则需要下载并导入激活文件，并激活。

```shell
/root/downloads/parallel_studio_xe_2018_update1_cluster_edition.lic
```

* 选择部分 Intel 产品即可，可通过自定义安装节省空间。
	* 架构为 Intel(R) 64，安装目录默认`/opt/intel/parallel_studio_xe_2018`；
	* Intel(R) Fortran Compiler
	* Intel(R) C++ Compiler
	* Intel(R) Math Kernel Library
* 进入 `bin` 目录，检查 `icc`，`icpc`和`ifort` 。
* 进行环境变量配置，将其所在目录添加到 `path` 环境变量中。

```shell
vim ~/.bashrc
```
* 进入 vi 后，o 键开启插入模式，插入:

```shell
source /opt/intel/parallel_studio_xe_2018/bin/compilervars.sh intel64
```
* `: `键进入命令行模式，退出并保存: `wq`
* 新建terminal，检查 `which icc`，`which icpc`和`which ifort` 。

```shell
[root@VM_0_8_centos ~]# which icc
/opt/intel/parallel_studio_xe_2018/compilers_and_libraries_2018.1.163/linux/bin/intel64/icc
[root@VM_0_8_centos ~]# which icpc
/opt/intel/parallel_studio_xe_2018/compilers_and_libraries_2018.1.163/linux/bin/intel64/icpc
[root@VM_0_8_centos ~]# which ifort
/opt/intel/parallel_studio_xe_2018/compilers_and_libraries_2018.1.163/linux/bin/intel64/ifort
```

* 实验 Hello World！
	
	```shell
	cd test/
	vim hello.f90
	```

	```shell 
	program hello
	print *,"Hello, World!"
	end program hello
	```
	
	```shell 
	ifort hello.f90
	```
	
	```shell 
	./a.out
	```
* 注意：其他用户使用时，要重新配置环境变量。