---
layout: draft
title: Clang、LLVM知识记录
date: 2016-11-20 14:20:36
tags: 编译器
---
![header](Clang、LLVM知识记录/Clang、LLVM知识记录.png)
上午的时候看了`趣直播`中孙源大神讲的Apple编译器相关知识，现在我把他的ppt上面的相关内容记录下来，没事的时候好好滴的认识学习下(有些图片没有贴上,空闲时间再整理)。
<!--more-->
* [Apple编译器Clang-LLVN架构初识](#Apple编译器Clang-LLVN架构初识)
* [你的源码是如何一步步成为可执行文件的?](#你的源码是如何一步步成为可执行文件的?)
* [我们能用Clang做什么有意思的事情?](#我们能用Clang做什么有意思的事情?)
Apple编译器Clang-LLVN架构初识
------
* LLVM - Low Level Virtual Machine (编译器后端)
* Clang - C Language Family Frontend For LLVM (编译器前端)
###GCC用的好好的,Apple为啥要自己搞一套？
* GCC的Objective-C Frontend不给力
* GCC插件、工具、IDE的支持薄弱
* GCC编译效率和性能
* Apple收回对工具链的控制(lldb,lld,swift...)
Three-Phase 编译器架构
`source`->`Frontend、Optimizer、Backend`->`machinecode`
>`源码、前端、优化、后端、机器码`

### Clang+LLVM代码规模
* Total 400w
* c++ 235
###Swift Frontend代码规模
* c++ 43w
你的源码是如何一步步成为可执行文件的?
------
>当我们点击`run`按钮的时候，执行了一大串的脚本(配置xcode上的一些参数)
``` Objective-C
/Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctolchain/usr/bin/clang -x objective-c -fobjc-arc ...... main.m -o main.o
```
Clang命令
* Clang在概念上是编译器前端，同时，在命令中也作为一个`黑盒`的Driver(驱动)
* 封装了编译管线、前端命令、LLVM命令、Toolchain命令等，一个Clang走天下
* 方便从gcc迁移过来
拆解编译过程
```Objective-C
main.m
#import <Foundation/Foundation.h>
int main(){
    @autoreleasepool{
        id obj = [NSObject new];
        NSLog(@"hello world: %@",obj);
    }
    return 0;
}
```
1. Preprocess - 预处理

* import 头文件
* macro 展开
* 处理`#`打头的预处理指令，如`#if`

>命令
```
$clang -E main.m
```

*注意：pch文件正确使用*
>命令
```
//-fmodules 默认会把库打成module的形式
$clang -E -fmodules main.m
```
```Objective-C
>@import Foundation;
其它不变...
```
2. Lexical Analysis -词法分析
* 词法分析，也作lex或者Tokenization
* 将预处理过的代码文本转化成Token流
* 不校验语义

>命令
```
//-fsyntax -only:解到语法层面 -Xclang -dump:透传给前端的工具搞成token流

$clang -fmodules -fsyntax -only -Xclang -dump -tokens main.m
```
>缺少图片
3. Semantic Analysis - 语法分析
* 语法分析，在Clang中由Parse和sema两个模块配合完成
* 验证语法是否正确
* 根据当前语言的语法，生成语意节点，并将所有节点组成抽象语法树(AST)

>命令
```
$clang -fmodules -fsyntax -only -Xclang -ast -dump -tokens main.m
```
>缺少图
额外的步骤`Static Analysis -静态分析`
* 通过语法树进行代码静态分析，找出非语法性错误
* 模拟代码执行路径，分析出control-flow graph (CFG)
* 预置了常用Checker
4. CodeGen -IR代码生成
* CodeGen 负责将语法树从顶至下遍历，翻译成LLVM IR
* LLVM IR是Frontend的输出，也是LLVM Backend的输入，前后端的桥接语言
* 与Objective-C Runtime 桥接

4(1). 与Objective-C Runtime的桥接
* Class/Meta Class/Protocol/Category内存结构生成，并存放在指定section中(如Class:_DATA,_objc_classrefs)
* Method/Ivar/Property内存结构生成
* 组成method_list/ivar_list/property_list并填入Class
* Non-Fragile ABI:为每个Ivar合成`OBJC_IVAR_$_`偏移值常量
* 存取Ivar的语句(_ivar =123;int a= _ivar;)转写成`base+OBJC_IVAR_$_`的形式
* 将语法树中的`ObjCMessageExpr`翻译成相应版本的`objc_msgSend`,对super关键字的调用翻译成`objc_msgSendSuper`
* 根据修饰符strong/weak/copy/atomic合成@property自动实现的setter/getter
* 处理@synthesize
* 生成block_layout的数据结构
* 变量的capture(_block/_weak)
* 生成_block_invoke函数
* ARC: 分析对象引用关系，将objc_storeStrong/objc_storeWeak等ARC代码插入
* 将ObjcAutoreleasePoolStmt转译成objc_autoreleasePoolPush/Pop
* 实现自动调用[super dealloc]
* 为每个拥有ivar的Class合成.cxx_destructor方法类自动释放类的成员变量，代替MRC时代的"self.xxx=nil"

>命令
```
// -S 
$clang -S -fobjc-arc -emit-llvm main.m -o main.ll
```
Optimize -优化IR
>命令
```
$clang -O3 -S -fobjc-arc -emit-llvm main.m -o main.ll
```
LLVM Bitcode -生成字节码
```
>$clang  -emit-llvm -c main.m -o main.bc
```
Assemble - 生成Target相关汇编
```
>$clang -S -fobjc-arc main.m -o main.s
```
Assemble - 生成Target相关Object(Mach-O)
```
>$clang -fmodules -c  main.m -o main.o
```
Link生成Executable
```
>$clang main.m -o main
$./main
```
我们能用Clang做什么有意思的事情?
-----
* LibClang
* LibTooling
* ClangPlugin

LibClang
> 1、C API 来访问Clang的上层能力，如获取Tokens、遍历语法树、代码补全、获取诊断信息
>2、API稳定，不受Clang源码更新影响
>3、只有上层的语法树访问不能获取到全部信息

LibClang使用
>1、使用原始C API
>2、脚本语言:使用官方提供的python binding 或开源的node-js/ruby binding
>3、Objective-C:开源库ClangKit

LibTooling
>1、对语法树有完全的控制权
>2、可作为一个standalone命令单独的使用，如clang-format
>3、需要使用c++且对Clang源码熟悉

ClangPlugin
>1、对语法树有完全的控制权
>2、作为插件注入到编译流程中，可以影响build和决定编译过程
>3、需要使用c++且对Clang源码熟悉

http://clang.llvm.org/docs/index.html
http://blog.llvm.org/
https://www.objc.io/issues/6-build-tools/compiler
http://llvm.org/docs/tutorial/index.html
https://github.com/loarabia/Clang-tutorial
http://lowlevelbits.org/getting-started-with-llvm/clang-on-os-x/
http://kevinaboos.wordpress.com/2013/07/23/clang-tutorial-part-i-introduction/
http://szelei.me/code-generator/
"`<<Getting Started with LLVM Core Libraries>><<LLVM Cookbook>>"`
