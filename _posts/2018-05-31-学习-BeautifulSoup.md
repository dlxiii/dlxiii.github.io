---
layout:     post
title:      学习 BeautifulSoup 
subtitle:   学习 Python 库的使用方法：BeautifulSoup
date:       2018-05-31
author:     DLXIII
header-img: img/post-bg-hacker.jpg
catalog: true
tags:
    - python
    - crawler
    - beautifulsoup
---


## 前言

Beautiful Soup是一个Python包，功能包括解析HTML、XML文档、修复含有未闭合标签等错误的文档。这个扩展包为待解析的页面建立一棵树，以便提取其中的数据，这在网络数据采集时非常有用[^BeautifulSoup]。可以称作Python的文本解析库[^解析]。

<!--more-->

> Beautiful Soup is a Python library designed for quick turnaround projects like screen-scraping. Three features make it powerful [^介绍]:
> * Beautiful Soup provides a few simple methods and Pythonic idioms for navigating, searching, and modifying a parse tree: a toolkit for dissecting a document and extracting what you need. It doesn't take much code to write an application
> * Beautiful Soup automatically converts incoming documents to Unicode and outgoing documents to UTF-8. You don't have to think about encodings, unless the document doesn't specify an encoding and Beautiful Soup can't detect one. Then you just have to specify the original encoding.
> * Beautiful Soup sits on top of popular Python parsers like lxml and html5lib, allowing you to try out different parsing strategies or trade speed for flexibility.


## Beautiful Soup 安装

推荐使用Beautiful Soup 4。
安装方法有很多，推荐使用pip。

~~~bash
pip install beautifulsoup4
~~~

## Beautiful Soup 使用

~~~python
>>> import requests
>>> from bs4 import BeautifulSoup
~~~

## 例子

~~~python
>>> url = "https://blog.goo.ne.jp/0424725533"
>>> response = requests.get(url)
>>> soup = BeautifulSoup(response.content, "html.parser")
>>> div = soup.find('div',id="mod-categories",class_='module')
>>> list = div.find_all('span',class_='mod-cat-count')
>>> for span in list:
        num = float(int(span.string.replace('(', '').replace(')', '')))
~~~

## 说明

 - 第3行：构建一个 BeautifulSoup 对象需要两个参数，第一个参数是将要解析的 HTML 文本字符串，第二个参数告诉 BeautifulSoup 使用哪个解析器来解析 HTML。[^方法] 
 - 第4行：寻找网页右边栏 “カテゴリー”。

~~~html
<div id="mod-categories" class="module">
   <div class="module-top"></div>
   <h4>カテゴリー</h4>
   <div class="module-body">
      <ul>
         <li><a href="/0424725533/c/d5edcafdf927a5d2f2225a60d51092d8"><span class="mod-cat-name">その他</span></a><span class="mod-cat-count">(7)</span></li>
         <li><a href="/0424725533/c/81c4f34f8951c06c98eb951ab3313255"><span class="mod-cat-name">国語の話</span></a><span class="mod-cat-count">(8)</span></li>
         <li><a href="/0424725533/c/35e963ac9803b935269456e22f27f7ab"><span class="mod-cat-name">社会の話</span></a><span class="mod-cat-count">(42)</span></li>
         <li><a href="/0424725533/c/b343ed5363825227091ebf188abf041d"><span class="mod-cat-name">理科の話</span></a><span class="mod-cat-count">(20)</span></li>
         <li><a href="/0424725533/c/f02a88062faad71d53ac36862664835f"><span class="mod-cat-name">英語の話</span></a><span class="mod-cat-count">(239)</span></li>
         <li><a href="/0424725533/c/e5b5c42c4b9d2f14213723fe06884efc"><span class="mod-cat-name">数学・算数の話</span></a><span class="mod-cat-count">(1065)</span></li>
         <li><a href="/0424725533/c/9f98347097d36d26b15d006e9bfe469c"><span class="mod-cat-name">学習塾塾長の日記</span></a><span class="mod-cat-count">(384)</span></li>
         <li><a href="/0424725533/c/018a9f6d7d73f5fd0d57e8651c971a50"><span class="mod-cat-name">高校受験</span></a><span class="mod-cat-count">(46)</span></li>
         <li><a href="/0424725533/c/b00c18b7a3d890f51b40561e66b9aa19"><span class="mod-cat-name">中学受験</span></a><span class="mod-cat-count">(15)</span></li>
         <li><a href="/0424725533/c/7b79cc9217e7b0c878125c5f5c281dff"><span class="mod-cat-name">勉強のやり方</span></a><span class="mod-cat-count">(93)</span></li>
         <li><a href="/0424725533/c/b41cf851e7960bf994082d5caf21730f"><span class="mod-cat-name">東久留米の話題</span></a><span class="mod-cat-count">(1)</span></li>
      </ul>
      <div class="clearboth"></div></div>
   <div class="module-bottom"></div>
</div>
~~~

 - 第5行：寻找 “カテゴリー”的具体内容文本，如 '<span class="mod-cat-count">(1065)</span>'。
 - 第6, 7行：获取文章数量。

[^BeautifulSoup]: [Beautiful Soup](https://zh.wikipedia.org/wiki/Beautiful_Soup)

[^解析]: [FOOFISH-PYTHON之禅](https://foofish.net/crawler-beautifulsoup.html)

[^介绍]: [Beautiful Soup 官网](https://www.crummy.com/software/BeautifulSoup/#Download)

[^方法]: [内置标准库](https://cuiqingcai.com/1319.html)