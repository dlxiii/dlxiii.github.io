---
layout:     post
title:      Linux 安装 OpenMPI
subtitle:   在 CentOS 上安装 OpenMPI
date:       2018-04-10
author:     DLXIII
header-img: img/post-bg-hacker.jpg
catalog: true
tags:
    - linux
    - centos
    - openmpi
---


## 前言

配置 CentOS 系统后要安装 OpenMPI。

## 下载 OpenMPI 并解压

* OpenMPI 版本号为3.0。
* 下载到 `downloads/` 目录。
* WGET：Linux上的下载命令，`wget 链接`。[^1]
[^1]: Linux用户宝典：用于下载的十大命令行工具: [http://os.51cto.com/art/201605/511423.htm]("Linux用户宝典：用于下载的十大命令行工具")

* 执行以下命令：

```shell
wget https://www.open-mpi.org/software/ompi/v3.0/downloads/openmpi-3.0.0.tar.gz
```


* 下载完成后移动到 `downloads` 目录：

```shell
mv openmpi-3.0.0.tar.gz downloads
```

## 安装 OpenMPI

* 解压 `*.tar.gz ` 文件，并安装，这是基于源代码的安装方式。

```shell
cd downloads/
tar -xzvf openmpi-3.0.0.tar.gz
cd openmpi-3.0.0
./configure --prefix=/opt/openmpi/3.0.0/intel CC=icc CXX=icpc FC=ifort F77=ifort
make
make install
```
* `make` 后，编译时间较长；`make install` 安装。
* 进入 `bin` 目录，检查 `mpicc`和`mpif90` 。
* 进行环境变量配置，将其所在目录添加到 `path` 环境变量中。

```shell
OPENMPI=/opt/openmpi/3.0.0/intel
export PATH=$OPENMPI/bin:$PATH
export LD_LIBRARY_PATH=$OPENMPI/lib:$LD_LIBRARY_PATH
export MANPATH=$OPENMPI/share/man:$MANPATH
```

* 新建terminal，检查 `which mpicc`和`which mpif90` 。


```shell
[root@VM_0_8_centos ~]# which mpicc
/opt/openmpi/3.0.0/intel/bin/mpicc
[root@VM_0_8_centos ~]# which mpif90
/opt/openmpi/3.0.0/intel/bin/mpif90
```

* 实验 pi.c，上传至 `test`。
	
	```shell
	cd test/
	mpicc -o pi pi.c
	mpirun -np 2 ./pi
	```
* pi.c 代码：	
	
	```cpp
	#include "mpi.h"
	#include <stdio.h>
	#include <math.h>
	
	double f(double a)
	{
	    return (4.0 / (1.0 + a*a));
	}
	
	int main(int argc,char *argv[])
	{
	    int    n, myid, nprocs, i;
	    double mypi, pi, dx, sum, x;
	    double startwtime = 0.0, endwtime;
	    int    namelen;
	    char   processor_name[MPI_MAX_PROCESSOR_NAME];
	
	    MPI_Init(&argc,&argv);
	    MPI_Comm_size(MPI_COMM_WORLD,&nprocs);
	    MPI_Comm_rank(MPI_COMM_WORLD,&myid);
	    MPI_Get_processor_name(processor_name,&namelen);
	
	    printf("Process %d of %d is on %s\n", myid, nprocs, processor_name);
	
	    n = 100000000;			/* default # of rectangles */
	    if (myid == 0)
	    {
		    startwtime = MPI_Wtime();
	    }
	    
	    MPI_Bcast(&n, 1, MPI_INT, 0, MPI_COMM_WORLD);
	    
	    dx = 1.0 / (double) n;
	    sum = 0.0;
	    
	    for(i = myid + 1; i <= n; i += nprocs)
	    {
		      x = dx * ((double)i - 0.5);
		      sum += f(x);
	    }
	    mypi = dx * sum;
	
	    MPI_Reduce(&mypi, &pi, 1, MPI_DOUBLE, MPI_SUM, 0, MPI_COMM_WORLD);
	    
	    if (myid == 0)
	    {
		      endwtime = MPI_Wtime();
		      printf("pi is approximately %.16f\n", pi);
		      printf("wall clock time = %f seconds\n", endwtime-startwtime);
	    }
	
	    MPI_Finalize();
	    return 0;
	}
	```

* 注意：其他用户使用时，要重新配置环境变量。


