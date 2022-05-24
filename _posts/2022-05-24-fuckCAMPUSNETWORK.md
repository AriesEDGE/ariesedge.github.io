---
layout: post 
title: "浅谈校园网绕认证UDP转发方法" 
date:   2022-05-24 
tags: [校园网][网络][端口转发]
comments: true
author: Aries
---

最近校园网的web认证天天崩溃，加之校园网的不合理的价格捆绑以及强制绑定一张不常用的手机卡，让我有了绕web认证的想法。注意，这是以学习目的为主，而且学校的垄断行为我们也无能为力。

请勿用于牟利，谢谢。

<!-- more -->

#方法

方法同网上几种方法。

前言：都需要一台服务器，最好是国内的节点。当然，国际的节点也不是不行，甚至还可以实现fq。。

1.利用Softether+OPENVPN开源项目，在远程端服务器搭建UDP中转服务，利用53等校园网开放端口进行端口转发。

2.部分学校ipv6不计费，同理，在远程端借用ipv6以及客户端V2ray搭建ipv6中转服务。

3.利用GOST进行端口转发。

4.利用最近新出的Hysteria(歇斯底里)协议的端口转发。

#测试效果

1.Softether+OPENVPN是我目前正在使用的方案。此方案比较成熟，目前比较成熟的有无为Wifi等。

2.ipv6的方法，服务器必须支持ipv6服务，最好是双栈服务。速度较其他方案快

3.GOST端口转发，到现在没弄明白？？？
  
4.Hysteria协议，由于协议比较冷门，许多客户端都不支持。

#操作方法

##服务器搭建

首先，你需要有一台服务器，而且必须拥有公网ip地址。

这里我选择的是Microsoft Azure国际版的学生服务器(小声bb:用学校邮箱即可白嫖一年100刀的额度)。
或者去白嫖一年阿里云国内的学生服务器，当然，如果你的财力雄厚的话，你甚至可以买两台/doge

1.购买vps服务器，创建ECS实例(虚拟机),Azure服务器或者阿里云等服务器创建实例可以看一下[Google](https://google.com.hk)。

  注意：1.推荐使用私匙链接ssh到你的服务器，安全性更高一些。但是使用用户名与密码登陆更容易记一点。
        2.服务器端强烈建议使用Linux。推荐CentOS 7/8或者Ubuntu。这里我选用的是AZURE的Ubuntu18.
        3.域名问题：目前在用三家DNS解析商：国内DNSPOD，国外CloudFlare/Freenom。
        推荐使用DNSPOD进行解析，速度比较快。当然，如果爱整活的话推荐cloudflare。

2.设置服务器防火墙端口开放，打开ssh开放端口22，http端口80，https端口443.

  如果嫌麻烦你甚至可以开放0-65535端口，但是我强烈不建议你这么做，本人以及有所经历，555，这样做很不安全。

3.使用[WinSCP](https://winscp.net/eng/index.php)/[MobaXterm](https://mobaxterm.mobatek.net/download.html)/[Git](https://gitforwindows.org/)等工具进行ssh链接
    推荐使用Git，简简单单就是因为他的方便.

    `ssh -i 你的证书文件.pem 用户名@公网IP/你的绑定域名`

4.链接到你的服务器，更新你的服务器配置。

    Ubuntu
    `sudo -i`
    `//进入root模式`
    `apt-get update`
    `apt-get upgrade`

至此，你的服务器环境基本以及搭建完了。

##四种方法操作实例

###Softeher+OPENVPN端口转发

1.Softeher服务端搭建




