---
layout:     post
title:      CentOS 创建 calibre-server
subtitle:   试图把 calibre 搬迁到远程计算机上
date:       2018-04-20
author:     DLXIII
header-img: img/post-bg-hacker.jpg
catalog: true
tags:
    - centos
    - calibre-server
---


## 安装 Calibre

~~~ bash
sudo -v && wget -nv -O- https://raw.githubusercontent.com/kovidgoyal/calibre/master/setup/linux-installer.py | sudo python -c "import sys; main=lambda:sys.stderr.write('Download failed\n'); exec(sys.stdin.read()); main()"
~~~

因为系统没有图形界面，安装最后会出现警告。
这些警告可以忽略。

<!--more-->

## 安装依赖环境

系统升级 > xvfb > ImageMagick

~~~ bash
yum update
~~~

~~~ bash
yum install xvfb
~~~

~~~ bash
yum install ImageMagick
~~~

## 创建书库

创建目录如下：
~~~ bash
mkdir ~/calibre-library
mkdir ~/calibre-library/toadd
~~~

下载两本书到目录中：

~~~ bash
cd ~/calibre-library/toadd
wget http://www.gutenberg.org/ebooks/1342.kindle.noimages -O pride.mobi
wget http://www.gutenberg.org/ebooks/46.kindle.noimages -O christmascarol.mobi
~~~

将图书添加到 calibre 的数据库中

~~~ bash
xvfb-run calibredb add ~/calibre-library/toadd/* --library-path ~/calibre-library
~~~
在 `toadd` 中的图书将被添加到数据库中，地址是 `calibre-library`。

## calibre-server 帮助命令

~~~ bash
calibre-server --help
~~~

## 参考
- https://www.digitalocean.com/community/tutorials/how-to-create-a-calibre-ebook-server-on-ubuntu-14-04
- https://blog.windisco.com/calibre-web-server/
- https://www.howtoing.com/how-to-create-a-calibre-ebook-server-on-ubuntu-14-04
- http://gccpacman.com/2017/12/20/calibre-server