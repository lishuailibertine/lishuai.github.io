layout: post
title: 区块链bip32、bip44、bip39介绍
comment: true
tags: [工作中的点滴]
date: 2020-04-30 10:05:00
updated: 2020-04-30 10:05:00
---

------

## 1. 账户体系介绍

>在区块链交易中会涉及到账户，这个账户相当于你的银行卡卡号一样，通过这个账户你可以很方便的去接收发送代币。像银行卡一样你也可以拥有许多账户。那么这个账户是怎么产生的呢? 
注: 大致为 私钥->椭圆曲线算法->公钥->hash->地址

## 2. bip32

>补充说明下 BIP: Bitcoin Improvement Proposals 比特币改进建议, bip32是第32个改进建议。bip32生成私钥的过程是一个分层推导的过程。

一句话概括下BIP32就是：为了避免管理一堆私钥的麻烦提出的分层推导方案。

<!--more-->

### 2.1 主私钥

*主私钥*
>它是由一个随机数种子根据`hmac_sha512`生成64字节长度的主私钥,主私钥前32个字节作为私钥将要作为下次生成子私钥的输入参数,后32个字节作为主链编码`ChainCode`。
```Objective-OC
NSData *masterPriKey = [DeterministicKey masterPriKeyForSeed:seed forKey:[@"xxx" dataUsingEncoding:NSUTF8StringEncoding]];

Pri:[masterPriKey subdataWithRange:NSMakeRange(0, 32)]
ChainCode:[masterPriKey subdataWithRange:NSMakeRange(32, 32)]
```

### 2.2 推导子私钥

*推导子私钥*
>衍生推导的方案其实有两个：一个用父私钥推导（称为强化衍生方程），一个用父公钥推导。通过子秘钥向下一层（深度扩展）可以无限生成私钥。


## 3. bip44

>指定了推导结构:m / purpose' / coin' / account' / change / address_index;

一句话概括下BIP44就是：给BIP32的分层路径定义规范

*m*
>是固定的;

*purpose*
>是固定的，值为44;

*coin*
>每条公链都不相同,可在[这里][1]查看;

*account*
>代表这个币的账户索引，从0开始;

*change*
>常量0用于外部链一般用于转账收款;常量1用于内部链一般用于找零为不可见地址;

*address_index*
>地址索引从0开始，代表生成第几个地址,官方建议，每个account下的address_index不要超过20;

## 3. bip39

>用户只需要记住自己的私钥就能拿到自己的资产了。但是私钥那么长我记不住怎么办？这时候bip39 提案就来了，用户不用记住一大串的16进制数据了，只需要很短的随机数种子或者是助记词就可以拿到自己的资产;

文章只是简单的描述下几个提案的区别，更详细的内容可以查看[知乎上的这篇文章讲的很好][2]


  [1]: https://github.com/satoshilabs/slips/blob/master/slip-0044.md
  [2]: https://zhuanlan.zhihu.com/p/45642005