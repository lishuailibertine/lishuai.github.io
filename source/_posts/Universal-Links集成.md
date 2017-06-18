layout: post
title: Universal Links集成
comment: true
tags: [工作中点滴]
date: 2017-06-18 18:11:46
updated: 2017-06-18 18:11:46
---
<!--more-->
写这篇文章的目的是记录下在项目中集成`Universal Links`所遇到的问题。
在介绍遇到的问题之前先来说下`Universal Links`

what's this?
官方介绍;
>When you support universal links, iOS users can tap a link to your website and get seamlessly redirected to your installed app without going through Safari. If your app isn’t installed, tapping a link to your website opens your website in Safari.

`Universal Links`是苹果iOS9推出的功能。如果你的项目中支持了`Universal Links`，当用户安装过后，点击你的链接地址(标准的HTTP或HTTPS)不用使用safari就可以无缝的跳转到你的APP中；如果用户没有安装，那么点击你的链接地址将会在Safari中打开。

有时候我们会遇到这种需求：分享一个页面到微信，当用户在微信中打开这个分享链接的时候会有一个打开按钮，引导并告诉用户点击按钮可以跳转到对应的APP，但是有个条件就是不能借助于safari浏览器跳转。那么我们就需要用到`Universal Links`技术了。

项目如何支持
>只需要简单几步你的项目就可以拥有这个强大的功能；
1，创建`apple-app-site-association`文件，这个文件的内容是json格式的，但是这个文件一定不要有`.json`后缀；
2，上传第一步创建的配置文件到你的`HTTPS`web服务器。苹果提供了指定的目录:你的web服务器的根目录或者是`.well-known`这个目录；
3，做完上面两件事接下来需要在你的`xcode`中配置`entitlement`文件，相关配置的内容可以看官方提供的介绍；

好了这就支持了！！

=.= 其实步骤是挺简单的，但是我却集成了三天多，尼玛！

为什么我会集成三天多呢？除去让发邮件让服务端配置上传那个配置文件用了一天= .=;剩下的时间我在干嘛了：调试啊，为什么就是点击链接跳转不了。
如果我们在集成`Universal Links`功能时出现问题是不太容易定位问题所在的。官方提供的测试地址`https://search.developer.apple.com/appsearch-validation-tool/`可以帮助我们检验`apple-app-site-association`文件路径是不是配置正确，当然还有其他的作用。这不废话吗，=。=
测试中思考几种无法跳转的原因(结合大量的google):
1，点击跳转的链接的域名与上一个加载的链接的域名必须不一样，也就是跨域才能让跳转生效；
2，我上传在web服务器上的配置文件格式或者内容错了；
3，我放到web服务器的路径错了；
4，xcode中配置的`entitlement`的信息有问题；
5,`apple-app-site-association`文件没有下载到手机上。
一一排除：
1，这个很容易检测，搞两个不同的域名进行测试就ok;
2，我是完全按照官网来的应该不会出错；
3，这个经过与运维人员交流并没有出现问题；
4，我是一次两次再次的看这个文件也没有发现问题；
5，这个`apple-app-site-association`文件我该怎么知道是否下载到手机了呢?况且我也不知道它下载到哪里了。

到这里我要提醒下我们在测试过程中出现跳转问题时，自己一定要抓包！抓包！抓包！这样我们如果发现在这一步出现问题就可以大概定位到问题出在哪里了，是在服务端还是在客户端。然后再进行更细致的分析问题。

我的问题出现哪里呢？就是第五个原因：文件没有下载到手机上。当APP首次安装时，我对它进行抓包发现，文件不能被下载，原因就是它的域名竟然与我放配置文件的域名不一样！为什么会出现这个问题呢？原因让自己苦笑不得，我在项目中发现两个同样的`entitlement`文件，而造成问题的就是其中一个配置异常域名(造成404无法下载配置文件)的文件。删除多余的异常文件重新安装，测试跳转通过。

总结一下：
如果自己能及时的抓包就可以尽早的发现问题所在，在以后排查问题时，告诫自己一定要切中重点来排除定位问题所在。
