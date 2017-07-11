---
layout: draft
title: xcode中的一些编译选项介绍
date: 2016-12-29 18:58:23
tags: 小知识
---
整理下xcode中一些编译选项有关内容。
<!--more-->
官方介绍地址

>https://developer.apple.com/legacy/library/documentation/DeveloperTools/Reference/XcodeBuildSettingRef/0-Introduction/introduction.html

Generate Debug Symbols

>这个选项改为NO的话编译出来的可执行文件会小很多。但是代码中设置的有断点的话，断点不会起作用。

Write Link Map File

>功能介绍: 通过配置参数，输出项目可执行文件。根据可执行文件可以分析出：文件构成元素、大小等。

>如何配置：XCode -> Project -> Build Settings -> 搜map -> 把Write Link Map File选项设为yes，并指定好linkMap的存储位置

2017-7-11 新增

Strip相关

Deployment Postprocessing

>功能介绍：strip所有选项的总开关，如果选NO，以下选项均无效

>如何配置：省略

Strip Debug Symbols During Copy

>功能介绍：文件拷贝编译阶段时是否进行strip，你的工程中有CopyFilesBuildPhase才有意义

>如何配置：省略

Strip Linked Product

>功能介绍：这个选项才对最后生成的二进制文件进行strip

>如何配置：省略

Strip Style

>功能介绍：all，non-global，debugging strip程度依次降低：all一般用于最后生成.app的工程；non-global用于bundle和framework，debugging一般都可以。虽然all是strip最多的选项，但是选择错误会导致strip失败

>如何配置：省略

Dead Code Stripping

>功能介绍：用于删除对象文件中不需要加载的符号，减小二进制文件大小

>如何配置：省略
