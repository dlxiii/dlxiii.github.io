---
layout:     post
title:      GTP partition style 问题
subtitle:   帮助研究室小哥处理笔记本电脑问题，只因为这台笔记本是中国产
date:       2018-04-19
author:     DLXIII
header-img: img/post-bg-hacker.jpg
catalog: true
tags:
    - GTP partition
    - WinPE
    - diskpart
    - Acer
---


## GPT partition

某 Acer 笔记本安装 Windows 10 教育版。

安装时，发现错误如下。

**Windows cannot be installed to this disk. The selected disk is of the gpt partition style.**
<!--more-->
参考油管，解决方案如下。

- 在选择完安装语言后，显示安装按钮时，按下 `Shift` + `F10`，弹出命令窗；
- 在 `X:\Sources>`后，输入 `diskpart`；
- 在 `DISKPART>`后，输入 `list`；
- 在 `DISKPART>`后，输入 `select disk 0`；
- 在 `DISKPART>`后，输入 `clean`。

全部磁盘格式化，然后就可以了。

备份系统，可以用 Windows PE。

https://docs.microsoft.com/en-us/windows-hardware/manufacture/desktop/winpe-intro

解决方案由油管提供。

https://www.youtube.com/watch?v=DQf9YqbD8WI
