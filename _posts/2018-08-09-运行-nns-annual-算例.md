---
layout:     post
title:      运行 nns_annual 算例
subtitle:   运行 fabm-gotm 耦合模型的 nns_annual 算例
date:       2018-08-09
author:     DLXIII
header-img: img/post-bg-hacker.jpg
catalog: true
tags:
    - fabm
    - gotm
    - nns_annual
    - namelist
    - editscenario
---


## 前言

nns_annual算例是 fabm-gotm 中的一个经典算例。该算例原本是 gotm 中已经包含的算例，后 NPZD 模块从中分离，在 fabm 平台下耦合计算的实例。该算例在 gotm 官方网站上已有简介：是位于联合王国与挪威之间的北海一测站的年度模拟，相关细节已发布在一下文章中，见：Bolding et al. (2002) （https://doi.org/10.1016/S0278-4343(02)00122-X）。


<!--more-->


## nns_annual算例

nns_annual算例简介：
* 该模型实际地点为：东经1°17′，北纬59°20′。
* 研究对象：1998 年间，110米水深的一维纵向水体。
* 驱动因素：气象观测（风，温度，湿度，空气压力和云层覆盖），其他3d模型生成的水温与盐度初始值，潮汐（海床上方 1m 处施加水平外部压力梯度以表示潮汐的影响）。
* 模拟结果：沿时间变化的盐度与温度值剖面等。
* GOTM模型：一部分默认设置（例如：使用具有二阶闭合的k-ɛ湍流模型），重新设置的最小湍流动能，底部粗糙度。
* NPZD模型：用于配置波罗的海浮游生物食物网的标准参数集，1998年间大西洋上空二氧化碳的大气分压值，溶解无机碳的初始值。

nns_annual算例结果：
* 案例体现了物理，生物与碳酸盐系统的相互作用关系。
* 发生在5月的海水分层（水温与盐度的变化）引发了浮游植物的爆发，这导致了强光区中无机碳的浓度降低（NPZD的变化），最终使pH值升高。
* 在夏季，水体分层主要是温度上升与地表水流入减小所致。


在配置好 fabm 与 gotm 后，既可以用 nns_annual 算例进行测试，在进行测试之前还有许多工作准备，此处耗费了近两周的时间，故做记录。

## nns_annual算例文件

其算例文件可从 github 下载，地址为：https://github.com/gotm-model/cases。
在根目录中的 READY_CASES 文件中可知，该算例目前已经准备完毕，可以进行测试。
nns_annual算例文件夹内文件列表如下。

~~~
.
├── bio_prof_file.dat
├── bioprofs.dat
├── bio_test2.prof
├── ext_press_file.dat
├── filelist
├── Makefile
├── meteo.dat
├── meteo_file.dat
├── meteonns.dat
├── nns_annual.xml
├── output.yaml
├── pressure.dat
├── README
├── sprof.dat
├── s_prof_file.dat
├── sst.dat
├── sst_file.dat
├── timestamp
├── tprof.dat
├── t_prof_file.dat
├── zeta.dat
└── zeta_file.dat
~~~

虽然已经准备完毕，但是通过 HOST 调用的配置文件并不包含在文件列表中。这是第一次接触 gotm 时遇到的问题。在读取配置文件说明与论坛时发现，需要将 make namelist 以释放 nml 文件，而这些 nml 文件正是目前缺少的。

## namelist 文件

在该算例中 nml 文件需要从 xml 文件中释放。
作为一种广泛使用的可扩展标记语言，我想从中释放数据应该不难，不过还是遵循了模型开发者的意见，使用了其推荐的 editscenario。

目前 editscenario 仅为 0.11 版本，而且貌似只支持 python 2 ，不过可以通过 pip 安装。安装方法可参考其 github 页面。

https://github.com/BoldingBruggeman/editscenario/releases/tag/v0.11

运行一下命令即可释放文件。

~~~ bash
make namelist
~~~ 

如果出现错误，原因可能有二：

第一，需要设定 gotm 目录如下。

~~~ bash
export GOTMDIR=$HOME/tools/gotm/code
~~~

第二，升级一下 python 库。

~~~ bash
pip2 install xmlplot --upgrade
pip2 install xmlstore --upgrade
pip2 install editscenario --upgrade
~~~

还有可能出现其他错误，如下所示。

~~~
editscenario --schemadir=/home/usr0/n70110d/tools/gotm/code/schemas --targetversion=gotm-5.3 nns_annual.xml -e nml .
Traceback (most recent call last):
  File "/home/usr0/n70110d/.local/bin/editscenario", line 7, in <module>
    from editscenario.editscenario import main
  File "/home/usr0/n70110d/.local/lib/python2.7/site-packages/editscenario/editscenario.py", line 13, in <module>
    import xmlstore.xmlstore,gotmgui.core.scenario
ImportError: No module named gotmgui.core.scenario
make: *** [namelist] Error 1
~~~

使用同样的方法安装`gotmgui-0.1.1-py2-none-any.whl`即可。

成功后会出现类似如下代码。

~~~
editscenario --schemadir=/home/usr0/n70110d/tools/gotm/code/schemas --targetversion=gotm-5.3 nns_annual.xml -e nml .
Operating on values file with version gotm-5.3.
Scenario validated successfully.
Exporting values to namelist file(s) ....
~~~

## 配置文件并计算

安装 fabm 设置说明配置 nml 文件与 yaml 文件。
最终计算，输出nc 文件。

## 处理数据

可使用 py 文件进行处理。我的 github 上分享了一份官方分享的代码。
https://github.com/dlxiii/nns_annual/blob/master/nns_annual.ipynb
经过修改，效果与官网所示无异。
https://github.com/dlxiii/nns_annual/blob/master/nns_annual.png

![nns_annual][1]


  [1]: https://github.com/dlxiii/nns_annual/blob/master/nns_annual.png?raw=true