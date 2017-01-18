---
title: 记录一下hexo的安装
date: 2016-11-05 14:12:24
tags:
---
![header](记录一下hexo的安装/header.png)
>前几天刚开通了blog,趁着现在还记起来步骤把它纪录下来。差点忘了说，我安装的平台是在mac上进行的。

开始
<!--more-->

* [安装node.js](#安装node.js)
* [安装hexo](#安装hexo)
* [hexo初始化](#hexo初始化)
* [安装依赖和插件](#安装依赖和插件)
* [同步hexo到github](#同步hexo到github)
* [主题更换](#主题更换)
* [一些配置项修改](#一些配置项修改)

安装node.js
>使用下面的命令(直接在终端输入):
```
$ brew install node
```
安装hexo
------
>这里我直接用的淘宝NPM镜像
```
$ npm install -g cnpm --registry=https://registry.npm.taobao.org
```
hexo初始化
------
>安装hexo完毕后，在自己的用户目录下建立一个文件夹，名字可以随便的起。在终端:
```
$ cd 建立的文件夹下
$ hexo init
```

安装依赖和插件
------
>终端执行
```
$ npm install
$ npm install hexo-deployer-git --save
```
>这样我们的本地环境就搭建好了，我们测试预览一下，在终端输入:
```
$ hexo server 
```
>在浏览器中输入http://localhost:4000/。正常的情况下就可以看到blog页面了。
>如果想在blog中插入图片的话，更改_config.yml中的post_asset_folder: true;然后在根目录下执行:
```
$ npm install https://github.com/CodeFalling/hexo-asset-image --save
```
>这样就可以看到在根目录的source/_posts下创建了一个跟md文件同名的文件夹如下图所示：

![yml](yml.png)
>在blog中使用图片的时候就在这个文件下建立需要的图片，在.md文件中引用即可,引用方式:
```
![图片](文件夹名字/xx.png)'
```
同步hexo到github
-----
>本地安装完毕，接下来就要在github上建立一个仓库用来存储我们日志的静态文件等。新建一个仓库取名为xx.github.io。这里切记'.github.io'不能写错。创建仓库完毕。

>在本地_config.yml中更改 delop项：
```
type: git
repository: https://github.com/xx/xx.github.io.git
branch: master
```
>使用组合命令同步到github
```
$ hexo d -g
```
>在浏览器中输入'http://xx.github.io'。看一下是否同步成功。至此blog的搭建工作已经差不多了。
主题更换
-----
>可以看到默认的blog的出题有点丑，这里我用的是基于hexo的yilia主题。
```
$ cd  到themes文件夹下
$ git clone https://github.com/litten/hexo-theme-yilia.git themes/yilia
```
>Clone完毕之后，更改_config.yml配置中的theme: yilia。执行:
```
$ hexo d- g
```
>这样我们就完成了默认主题的更换。在浏览器中输入'http://xx.github.io'。看一下主题是否更换成功。
一些配置项修改
-----
[可以参考这里](https://github.com/litten/hexo-theme-yilia "github")
