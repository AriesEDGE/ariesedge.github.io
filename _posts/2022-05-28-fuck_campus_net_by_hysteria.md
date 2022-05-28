---
layout: post
title: "浅谈基于Hysteria协议的校园网端口转发"
date:   2022-05-28
tags: [network]
comments: true
author: aries
---

对于UDP端口绕验证，尝试了一下Hysteria协议，不得不说这个协议确实很强，低延迟高速率。

但是，在他快速低延迟的情况下，也有他的不足。

<!-- more -->

# Hysteria协议

Hysteria 歇斯底里 是一个功能丰富的，专为恶劣网络环境进行优化的网络工具（双边加速），比如卫星网络、拥挤的公共 Wi-Fi、在中国连接国外服务器等。 
基于修改版的 **QUIC 协议** (基于UDP协议)。

说白了就是魔改的QUIC。
以1.5倍发UDP包。

# Hysteria注意事项

由于Hysteria是以1.5倍发包，并且是基于UDP协议的魔改QUIC。

注意：

**请务必把上行下载速度调低！！！VPS服务端封号自负！！！**

**请务必把上行下载速度调低！！！VPS服务端封号自负！！！**

**请务必把上行下载速度调低！！！VPS服务端封号自负！！！**

# Hysteria环境搭建

大体分为2步，服务端搭建，客户端搭建.

## Hysteria服务端

1.请在VPS的安全策略或者防火墙中放行端口！！！比如53，67等端口。

2.部署Hysteria服务

使用SSH连接到你的VPS.

这里我是采用的 [小御坂的破站] (https://owo.misaka.rest/Hysteria-script/) 提供的一键搭建脚本，这个更容易上手与配置。

当然，也有其他一键搭建脚本，更详细 [Git上的脚本](https://git.io/hysteria.sh).

部署方法

`wget -N https://raw.githubusercontents.com/Misaka-blog/Hysteria-script/master/hysteria.sh && bash hysteria.sh`

按照脚本指引走就好，注意你的放行端口。

 ![配置hy服务端.png](https://s2.loli.net/2022/05/28/GHZcM9BerXWxbPV.png)

3.会生成一个类似VMESS的Hysteria协议地址，复制即可 

协议解析：hysteria://服务器:端口?auth=123456&upmbps=15 //上行限速15mbps &downmbps=15 //下行限速15mbps &obfs=xplus&obfsParam=123456 //123456代表密码

查看client端配置文件(特指Windows端)

`cat /root/Hysteria/client.json`

在你的本地Windows中新建一个hy.json,将内容复制到里面并保存。

 ![查看配置文件截图.png](https://s2.loli.net/2022/05/28/o1ki8nFBR3uZN2U.png)

至此，服务端配置完成。

## Hysteria客户端

三个客户端，玩Linux客户端都是大佬/doge

[Git开源地址](https://github.com/HyNetwork/hysteria)

Github下载加速:将 github.com 换成 git.imaries.cf ,自己用CloudFlare Worker搭建。

### iOS端 

使用 **ShadowRocket** 直接导入Hysteria协议即可

**ShadowRocket**需要美区账号并且需要购买，你可以去github上找找(手上无iOS设备)有第三方安装。

### Android端

使用 **SagerNet** 导入Hysteria协议即可，注意可能要下载额外的Hysteria Plugin插件(请到Google Play下载).

**注意：国产ui需要将Hysteria Plugin自启动打开**

**注意：国产ui需要将Hysteria Plugin自启动打开**

**注意：国产ui需要将Hysteria Plugin自启动打开**

### Windows端

我试过好多方法，hy官方+SSTap\Netch\V2ray(socks5)，单走V2ray Hy核心。

最后采用了 hy官方内核+Proxifer真全局代理

虽然新版的V2ray已经支持了hy核心，但是代理效果差，微信/QQ这些毒瘤软件会走自己的私人端口，所以最终采用了Hysteria核心+Proxifer 的socks5代理

 下载 [**Hysteria Core**](https://git.imaries.cf/HyNetwork/hysteria/releases/download/v1.0.4/hysteria-tun-windows-6.0-amd64.exe)

 下载 [**Proxifer**](https://www.proxifier.com/download/legacy/ProxifierSetup331.exe )

下载完成后安装Proxifer 

`激活码：JCZBX-A3N3F-9DWY5-327V5-DKUXX (已测试, 可用)`

**Hysteria Core设置**

新建一个文件夹，将Hysteria Core和你编写的json文件(假设你的json文件叫做 hy.json)一起拖入该文件夹。

执行以下命令

`.\hysteria-tun-windows-6.0-amd64.exe -c hy.json client`

 ![启动Hycore客户端.png](https://s2.loli.net/2022/05/28/SiMl4813pmCQXhb.png)

**Proxifer设置**

Profile -- Add -- Proxy Server

添加 127.0.0.1 端口1080

 ![配置prxoy1.png](https://s2.loli.net/2022/05/28/rkQsvo9pcZHIK3u.png)

 ![配置proxy2.png](https://s2.loli.net/2022/05/28/ndFohStmEeOU7Aa.png)

至此，配置完成

# 测试效果以及小结

 ![speedtest测试.jpg](https://s2.loli.net/2022/05/28/EpOJAoBW24vL6PN.jpg) 

可以看出，延迟特别低速度也可以(香港Azure服务器)，但是同上文说，一定要 **限制速率** ，封号概不负责。

效果比搭建OPENVPN+SOFTETHER效果好太多了。









