---
layout:     post
title:      GMT/Python 配置
subtitle:   更新 GMT/Python 配置的记录
date:       2018-10-17
author:     DLXIII
header-img: img/post-bg-hacker.jpg
catalog: true
tags:
    - gmt/python
---


## 前言

今年年初，曾经写过配置 GMT/Python 的文章，现在搜索 GMT/Python，可以在网络中看到年初的版本。如今，版本有所更新，恰逢最近需要使用，便重新做了一些配置。

 - 曾经写的配置方法（[英文][1]）（[中文][2]）

<!--more-->

原则上基本大同小异，详细可以参照其[官方文档][3]。

只有一处需要注意。（此处存疑，主要是还不太懂 conda。）

原文讲到：

> Activate the environment by running:
> `source activate gmt-python`

但是在使用过程中，使用以下方法才能正确配置，原因不知，且没时间研究，只做个记录。

> To activate this environment, use
> `conda activate gmt-python`
> To deactivate an active environment, use
> `conda deactivate`

测试

gmt.test()
Loaded libgmt:
binary dir: /home/usr0/n70110d/usr/local/anaconda3/envs/gmt-python/bin
cores: 72
grid layout: rows
image layout:
library path: /home/usr0/n70110d/usr/local/anaconda3/envs/gmt-python/lib/libgmt.so
padding: 2
plugin dir: /home/usr0/n70110d/usr/local/anaconda3/envs/gmt-python/lib/gmt/plugins
share dir: /home/usr0/n70110d/usr/local/anaconda3/envs/gmt-python/share/gmt
version: 6.0.0
========================== test session starts ==========================
platform linux -- Python 3.6.6, pytest-3.8.2, py-1.7.0, pluggy-0.8.0 -- /home/usr0/n70110d/usr/local/anaconda3/envs/gmt-python/bin/python3
cachedir: .pytest_cache
Matplotlib: 3.0.0
Freetype: 2.9.1
rootdir: /home/usr0/n70110d, inifile:
plugins: mpl-0.10
collected 150 items

另外，如果需要 spyder，可参照如下配置方法。

~~~bash
(gmt-python) [n70110d@ito-2 ~]$ conda install spyder
~~~

再另外，如果需要 jupyter notebook，可参照如下配置方法。

~~~bash
(gmt-python) [n70110d@ito-2 ~]$ conda install jupyterlab
~~~

  [1]: https://sites.google.com/edu.k.u-tokyo.ac.jp/dragonbaby/gmtpython?authuser=0
  [2]: https://cloud.tencent.com/developer/news/104728
  [3]: https://www.gmtpython.xyz/latest/install.html
