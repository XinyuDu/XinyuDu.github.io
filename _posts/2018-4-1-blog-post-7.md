---
title: 'ubuntu 16.04下不同版本python的切换'
date: 2018-4-17
permalink: /posts/2018/4/blog-post-1/
tags:
  - python
  - ubuntu
  - tensorflow
---

ubuntu 16.04自带python 2.7和python 3.5两个版本，两个版本的python切换的命令如下：

`sudo update-alternatives --config python`

  ```
glacier@ubuntu:~$ sudo update-alternatives --config python
[sudo] password for glacier: 
There are 2 choices for the alternative python (providing /usr/bin/python).

  Selection    Path              Priority   Status
------------------------------------------------------------
* 0            /usr/bin/python3   150       auto mode
  1            /usr/bin/python2   100       manual mode
  2            /usr/bin/python3   150       manual mode

Press <enter> to keep the current choice[*], or type selection number: 1
update-alternatives: using /usr/bin/python2 to provide /usr/bin/python (python) in manual mode

  ```

用下面命令查看当前python版本：

`python --version`