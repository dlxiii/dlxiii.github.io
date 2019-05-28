---
layout:     post
title:      PyFVCOM 安装中的错误解决
subtitle:   mpl_toolkits.basemap 的 KeyError 错误
date:       2019-05-28
author:     DLXIII
header-img: img/post-bg-python.jpg
catalog: true
tags:
    - python
    - PyFVCOM
    - mpl_toolkits.basemap
    - PROJ_LIB
---


## 前言

* 测试 PyFVCOM。

因 PyFVCOM pakage 的 plot.py 中，因未能正确加载 mpl_toolkits.basemap 而引发的错误。

~~~python
have_basemap = True
try:
    from mpl_toolkits.basemap import Basemap
except ImportError:
    warn('No mpl_toolkits found in this python installation. Some functions will be disabled.')
    Basemap = None
    have_basemap = False
~~~

事实上，mpl_toolkits.basemap 的加载出现了错误。

~~~python
In [1]  from mpl_toolkits.basemap import Basemap

Out[1]  ---------------------------------------------------------------------------
        KeyError                                  Traceback (most recent call last)
        <ipython-input-8-d9467465a3b6> in <module>
        ----> 1 from mpl_toolkits.basemap import Basemap

        ~/anaconda3/lib/python3.7/site-packages/mpl_toolkits/basemap/__init__.py in <module>
            153 
            154 # create dictionary that maps epsg codes to Basemap kwargs.
        --> 155 pyproj_datadir = os.environ['PROJ_LIB']
            156 epsgf = open(os.path.join(pyproj_datadir,'epsg'))
            157 epsg_dict={}

        ~/anaconda3/lib/python3.7/os.py in __getitem__(self, key)
            676         except KeyError:
            677             # raise KeyError with the original key value
        --> 678             raise KeyError(key) from None
            679         return self.decodevalue(value)
            680 

        KeyError: 'PROJ_LIB'
~~~

<!--more-->

## 方案

引用：https://github.com/conda-forge/basemap-feedstock/issues/30

Export PROJ_LIB in the env:

~~~bash
export PROJ_LIB=/home/your_user_name/anaconda3/share/proj/
~~~