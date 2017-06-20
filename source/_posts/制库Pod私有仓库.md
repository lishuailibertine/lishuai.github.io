layout: post
title: 制库Pod私有仓库
comment: true
tags: [工作中的点滴]
date: 2017-06-20 17:00:21
updated: 2017-06-20 17:00:21
---
>项目中使用到pod库来解决包管理，这篇文章记录下pod库制作的一些步骤和注意事项。
<!--more-->
私有库制作官方地址:https://guides.cocoapods.org/making/private-cocoapods.html

**制作pod库步骤(私有仓库)**

>本篇文章是基于github上创建的仓库。

第一步:环境准备

>我的CocoaPods安装版本是1.2.1

*cocoapods安装*

>sudo gem install cocoapods

第二步:创建仓库(管理你制作的pod库)

>你可以在任何一个git服务器上创建一个私有仓库。下面的截图是我在github上已经创建的一个仓库`SLTestSpecs`

* 通过下面的命令添加仓库到本地
>pod repo add `SLTestSpecs` `https://github.com/lishuailibertine/SLTestSpecs.git`

![此处输入图片的描述][1]

* 如果添加成功会在目录中看到添加的仓库(如下)。

![此处输入图片的描述][2]

第三步:制作一个pod库并上传到你建的仓库中(上面步骤建的仓库)

* 开始建一个仓库(作为你的pod库)
> 下图是我已经创建好并提交过的一个仓库`SLFirstPod`

![此处输入图片的描述][3]

* clone 刚创建的仓库(`SLFirstPod`)到本地
* 开始编辑`podspec`文件(非常重要)
1, 管理着你的pod库源文件、模块、.a、.framework等的依赖关系。
2, 关系到pod库能否验证并成功push到我们自己的pods仓库中。

```Objective-OC
Pod::Spec.new do|s|
  //项目名
  s.name ='SLFirstPod'
  //版本号
  s.version ='1.0.0'
  //详细介绍
  s.description = "详细介绍"
  //license文件的类型
  s.license = 'MIT'
  //简单描述
  s.summary = 'ATest in iOS.'
  //支持的平台及版本
  s.platform     = :ios, '7.0' 
  //最低要求的系统版本
  s.ios.deployment_target= '7.0'
  //项目的地址，只支持HTTP和HTTPS地址，不支持ssh的地址
  s.homepage ='https://github.com/lishuailibertine/SLFirstPod'
  //截图                      
  s.screenshots     = "www.example.com/screenshots_1"
  //多媒体介绍地址
  s.social_media_url = 'https://twitter.com/<twitter_username>'  
  //作者和邮箱
  s.authors = {'lishuai' => 'lishuai19@yeah.net' }
  //git仓库的https地址
  s.source = { :git=>'https://github.com/lishuailibertine/SLFirstPod.git', :tag =>s.version}
  //是否要求arc
  s.requires_arc = true
 //在这个属性中声明过的.h文件能够使用<>方法联想调用（这个是可选属性）
  s.public_header_files = 'UIKit/*.h'
  //表示源文件的路径，这个路径是相对podspec文件而言的
  s.source_files ='AppInfo/*.*'
  #依赖关系，该项目所依赖的其他库
  s.dependency 'AFNetworking', '~> 2.3'
  //可拥有多个dependency依赖属性   
  s.dependency 'JSONKit', '~> 1.4'
  //资源文件存放位置，放在Resources文件夹中
  s.resource_bundles = {
    'Test5' => ['Test5/Assets/*.png']
  } 
  // 设置只依赖一个系统的library
  s.library = 'iconv'
  // 设置依赖多个系统的library
  s.libraries = 'iconv', 'xml2' 
  //其他的library。
  s.vendored_libraries = "x.a"
  //需要用到的frameworks，不需要加.frameworks后缀。（这个没有用到也可以不填）
  s.frameworks ='Foundation', 'CoreGraphics', 'UIKit'
  //本地的frameworks
  s.vendored_frameworks =""
  //依赖的路径
  s.preserve_paths = 'x.framework'
  //依赖的库
  s.frameworks = 'x'
  //资源
  s.resource = "x.bundle"
  //配置参数
  s.xcconfig = { 'FRAMEWORK_SEARCH_PATHS' => '"$(SRCROOT)/x"' } 
  //建立名称为Info的子文件夹（虚拟路径）
  s.subspec 'Info' do |ss| 
     ss.subspec 'InfoSub' do |sss| 
     end 
  end
end
```
第四步:验证你的`podspec`文件

>主要是验证你的文件建立的依赖关系是否正确。

>pod lib lint --allow-warnings `本地podspec文件地址`

第五步:打个tag上传到我们的pod库

>git add .
>git commit -m "描述信息"
>git tag 版本号 -m "描述信息"
>git push tags

第六步:push我们刚才做的pod库到我们pods仓库中

>pod repo push `pods仓名字` --allow-warnings  `本地的podspec文件路径`

到此我们的pod库制作完成。

我们可以在远程自己的私有仓库中可以看到这条记录版本等信息。

![此处输入图片的描述][4]

* 本地`pod search`一下看是否有这个仓库

![此处输入图片的描述][5]
到此我们的pod库创建完毕并且上传到了我们的私有仓库中了。


**其他内容**

常用命令详情说明:

参考:https://guides.cocoapods.org/terminal/commands.html

touch Podfile :生成pofile文件

pod install:根据podfile文件安装pod库

pod update:更新库(可以指定某个具体的pod库)

pod spec create:生一个podspec文件

pod spec create xx 地址 (已有库)

pod spec lint:远程校验podspec文件

pod repo add:增加私有仓库到本地

pod repo update:更新本地仓库

pod repo lint:验证仓库下所有的podspec文件

pod repo list:列出本地仓库下所有的pod库

pod lib create:创建一个模版工程

pod ipc spec:把podspec文件格式转为podspec.json


一些小技巧:

1.加快pod install/update 速度
>pod install --no-repo-update  
>pod update --no-repo-update

注意事项

1.--use-libraries
>当你遇到下面的错误：include of non-modular header inside framework module 发布的时候带上`--use-libraries`

[1]: http://oo84571hk.bkt.clouddn.com/podSpec.png
[2]: http://oo84571hk.bkt.clouddn.com/localSpec.png
[3]: http://oo84571hk.bkt.clouddn.com/private_pod.png
[4]: http://oo84571hk.bkt.clouddn.com/podSpec.png
[5]: http://oo84571hk.bkt.clouddn.com/local_search.png