---
layout:     post
title:      python 求解偏微分方程：scipy.integrate.odeint
subtitle:   学习 python 求解偏微分方程：scipy.integrate.odeint
date:       2018-08-13
author:     DLXIII
header-img: img/post-bg-hacker.jpg
catalog: true
tags:
    - python
    - scipy.integrate.odeint
---


## 前言

调用的方法为如下：

~~~ python
odeint(func, y0, t, args=(), Dfun=None, col_deriv=0, full_output=0,
       ml=None, mu=None, rtol=None, atol=None, tcrit=None, h0=0.0,
       hmax=0.0, hmin=0.0, ixpr=0, mxstep=0, mxhnil=0, mxordn=12,
       mxords=5, printmessg=0, tfirst=False)
~~~

正如说明中所述

> Integrate a system of ordinary differential equations.


<!--more-->


其中，常用参数含义如下：
    func : callable(y, t, ...) or callable(t, y, ...)
        Computes the derivative of y at t.
        If the signature is ``callable(t, y, ...)``, then the argument
        `tfirst` must be set ``True``.
    y0 : array
        Initial condition on y (can be a vector).
    t : array
        A sequence of time points for which to solve for y.  The initial
        value point should be the first element of this sequence


返回值如下：
    y : array, shape (len(t), len(y0))
        Array containing the value of y for each desired time in t,
        with the initial value `y0` in the first row.


例子：求解 θ 的二阶微分方程

        theta''(t) + b*theta'(t) + c*sin(theta(t)) = 0

    其中`b`和`c`是正常数，而 ' 表示微分符号。  
    要用`odeint`来求解这个二阶偏微分函数，我们必须首先将它转换为一阶方程组。  
    定义 ``omega(t) = theta'(t)``, 则有：

        theta'(t) = omega(t)
        omega'(t) = -b*omega(t) - c*sin(theta(t))

    定义 `y` 为向量 [`theta`, `omega`].  
    则有：

~~~ python
def pend(y, t, b, c):
     theta, omega = y
     dydt = [omega, -b*omega - c*np.sin(theta)]
     return dydt
~~~
   
    令 `b` = 0.25 ， `c` = 5.0：

~~~ python
b = 0.25
c = 5.0
~~~

    设置其初始条件为 `theta(0)` = `pi` - 0.1, 与此同时， `omega(0)` = 0.  
    则初始向量为：

~~~ python
y0 = [np.pi - 0.1, 0.0]
~~~

    设置定义域区间如下：
    0 <= `t` <= 10.  间隔为0.1：

~~~ python
t = np.linspace(0, 10, 101)
~~~

    最后调用 `odeint` 求解.  
    使用 `args` 语句来向 `pend` 传递参数 `b` 和 `c`：

~~~ python
from scipy.integrate import odeint
sol = odeint(pend, y0, t, args=(b, c))
~~~

    sol 维度为 (101, 2).  其中第一列为 `theta(t)`, 第二列为 `omega(t)`.  
    可作图如下：

~~~ python
import matplotlib.pyplot as plt
plt.plot(t, sol[:, 0], 'b', label='theta(t)')
plt.plot(t, sol[:, 1], 'g', label='omega(t)')
plt.legend(loc='best')
plt.xlabel('t')
plt.grid()
plt.show()
~~~

![请输入图片描述][1]


  [1]: https://docs.scipy.org/doc/scipy/reference/_images/scipy-integrate-odeint-1.png