---
layout: draft
title: xcode中的一些编译选项介绍
date: 2016-12-29 18:58:23
tags: 小知识
---
整理下xcode中一些编译选项有关内容。
<!--more-->
Generate Debug Symbols

>这个选项改为NO的话编译出来的可执行文件会小很多。但是代码中设置的有断点的话，断点不会起作用。

Write Link Map File

>功能介绍: 通过配置参数，输出项目可执行文件。根据可执行文件可以分析出：文件构成元素、大小等。

>如何配置：XCode -> Project -> Build Settings -> 搜map -> 把Write Link Map File选项设为yes，并指定好linkMap的存储位置
