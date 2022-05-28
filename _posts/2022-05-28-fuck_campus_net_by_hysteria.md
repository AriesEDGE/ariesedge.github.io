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
  注意本博客仅用于学习目的，请勿用于非法用途，谢谢！

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

1.使用 **SagerNet** 导入Hysteria协议即可，注意可能要下载额外的Hysteria Plugin插件(请到Google Play下载).

**注意：国产ui需要将Hysteria Plugin自启动打开**

**注意：国产ui需要将Hysteria Plugin自启动打开**

**注意：国产ui需要将Hysteria Plugin自启动打开**

2.基于LINUX **root** 的新方法,效果比较好.

**手机需要root**

[工具+环境配置](https://smyisaries.lanzoum.com/iCC9105kzckd)

官方给的SagerNet的方法实在是太垃圾，对于Hysteria协议跟没有一样

晚上也是突发奇想，Android不是也是Linux系统吗？

为什么我不用Linux的心态看Android呢？

基于Android也是Linux的心态，我打算试一下使用Linux的方法执行核心，这里我在官网下载的是ARM64版本(现在手机处理器一般都是arm-v8a或者v9.）

--顺便吐槽一句，今年ARM v9架构真的垃圾..我的Redmin K50Pro深受其害..--

刚开始的时候只写了一个shell脚本，执行后出现"Pessmion Denied"，权限不足，已经见怪不怪了..

这个时候发现无法给777权限，由于在Android 11后在用户sdcard目录下无法给予777权限

所以将他移到了data目录，并且采用了官网wiki给的命令.

/data/aries下有hysteria核心，json配置文件，执行sh脚本

`./hysteria -c hysteria.json client`

(提供的脚本执行成功了是不会自动退出的).

此时执行成功了，那么我们还要想办法监听本地127.0.0.1的端口.

 ![shell.jpg](https://s2.loli.net/2022/05/29/S4bwcBfqRHdGnhN.jpg)

开始我想到了V2RAY,SOCKPROXY，但是无一例外都失败了.

于是我想到了Android自带的WiFi代理并且修改了wifi代理设置，修改为127.0.0.1跟端口，突然给我了个惊喜，可以直接连！

打开edge，bing可以打得开.蛮不错

可是当我打开微信QQ这些走私端的毒瘤软件，害，毒瘤还得是毒瘤。。

 ![WIFI代理.jpg](https://s2.loli.net/2022/05/29/gASh1TnIsQb672X.jpg)

这个时候我只能把思路又转回软件代理。

开始Google，发现一个走root的全局代理的 **ProxyDroid** ，这个软件可以走socks也可以走http.

(拒绝垃圾国内软件从我做起.点名批评某号称全球最大的中文搜索引擎..)

抱着试试的心态，啊哈，直接成功了.有一点点小激动，哈哈.

 ![proxy.png](https://s2.loli.net/2022/05/29/nROMlJ1f3sko7LQ.png)

此时已经成功了，但是我还是觉得麻烦.

所以后续打算打包成magisk模块让他开机就自动运行shell脚本，并且想用iptables代替软件的代理功能(还在学)，实现真shell实现功能，顺便加一个自动发送LOG的TG BOT.(马上就会更新) /doge

未完待续

### Windows端

我试过好多方法，hy官方+SSTap\Netch\V2ray(socks5)，单走V2ray Hy核心。

最后采用了 hy官方内核+Proxifer真全局代理

虽然新版的V2ray已经支持了hy核心，但是代理效果差，微信/QQ这些毒瘤软件会走自己的私人端口，所以最终采用了Hysteria核心+Proxifer 的socks5代理

 下载 [**Hysteria Core**](https://git.imaries.cf/HyNetwork/hysteria/releases/download/v1.0.4/hysteria-tun-windows-6.0-amd64.exe)

 下载 [**Proxifer**](https://www.proxifier.com/download/legacy/ProxifierSetup331.exe )

下载完成后安装Proxifer 

`激活码：JCZBX-A3N3F-9DWY5-327V5-DKUXX (已测试, 可用)`

**Hysteria Core设置**

新建一个文件夹，将Hysteria Core和你编写的json文件(假设你的json文件叫做 hysteria.json)一起拖入该文件夹。

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









