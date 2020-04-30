layout: post
title: Filecoin链
comment: true
tags: [工作中的点滴]
date: 2020-04-30 14:50:04
updated: 2020-04-30 14:50:04
---

------


>Filecoin链目前的还在测试阶段，测试阶段中因为版本的变更，参数什么的会有所不同，在支持它的同时要做好版本的兼容工作;

## 一些文档工具:

1, 在app中支持它目前提供的[RPC接口][1]
2, IM讨论[地址][2]:
3, lotus节点所有的api(包括钱包需要的，以及提供给节点使用的)[接口定义][3]
4, 基于官方java库的一个[web钱包][4]
5, Cid介绍[这里][5]
<!--more-->

## 1. 账户系统

### 1.1 私钥

>与其他的链并无很大区别,它的coinType为461

### 1.2 地址组成

|  network   | protocol | payload | checksum |
|------------|----------|---------|----------|
| 'f' or 't' |  1 byte  | n bytes | 4 bytes  |

>地址之间的不同主要是生成payload的过程

*SECP256K1*
>payload: Blake2b(pubkeyData,20)

## 2. 转账

### 2.1 SECP256K1类型的message

```Objective-OC
明文格式化信息:
{
    Message =     {
        From = " ";
        GasLimit = 10000;//节点版本0.3.0
        GasPrice = 1;
        Method = 0;
        Nonce = 0;
        To = " ";
        Value = 1;
        params ="";
    };
    Signature =     {
        Data = "string";
        Type = 1;//节点版本0.3.0
    };
}
签名数据结构及顺序:
{
        To = " ";
        From = " ";
        Nonce = 0;
        Value = 1;
        GasPrice = 1;
        GasLimit = 10000;
        Method = 0;//默认
        params ="";//默认
};
```
### 2.2 SECP256K1类型的签名过程

>与以往的链不同的是，它序列化的数据是通过CBOR标准encode的,encode的内容会再生成一个内容寻址Cid,这个Cid会作为hash的输入参数，最后再把hash的被容进行签名,伪代码:base64(blake2b(cid(cbor(message))));


  [1]: https://documenter.getpostman.com/view/4872192/SWLh5mUd?version=latest#d3695114-9bbb-4f63-a8d3-f7432e7c10da
  [2]: https://app.slack.com/client/TEHTVS1L6/CEGN061C5/files/F0138EJ3A8G
  [3]: https://github.com/filecoin-project/lotus/blob/master/api/api_full.go
  [4]: https://wallet.gamma.xjxh.pro/
  [5]: https://github.com/ipld/cid