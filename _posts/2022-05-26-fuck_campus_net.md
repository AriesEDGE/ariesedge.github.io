---
layout: post
title: "浅谈校园网端口转发"
date:   2022-05-26
tags: [network]
comments: true
author: aries
---

    最近校园网的web认证天天崩溃，加之校园网的不合理的价格捆绑以及强制绑定一张不常用的手机卡，让我有了绕web认证的想法。
    注意，这是以学习目的为主，学校的垄断行为我们也无能为力。
    这只是舞铲阶级的抵抗！！！！

<!-- more -->

# 方法

方法大体同网上几种方法一致，有其他思路欢迎联系我，[Telegram](https://t.me/awwwwwwesome)。

前言：都需要一台服务器，最好是国内的节点。当然，国际的节点也不是不行，甚至还可以实现fq。。

1.利用**Softether+OPENVPN**开源项目，在远程端服务器搭建**UDP中转**服务，利用**53**等校园网开放端口进行端口转发。

2.部分学校**ipv6**不计费，同理，在远程端借用ipv6以及客户端**V2ray**搭建ipv6中转服务。

3.利用**GOST**进行端口转发。

4.利用最近新出的**Hysteria(歇斯底里)协议**的端口转发。

# 测试效果

1.Softether+OPENVPN是我目前正在使用的方案。此方案比较成熟，比如无为Wifi等。

2.ipv6的方法，服务器以及校园网必须支持ipv6服务，最好是双栈服务。速度较其他方案快

3.GOST端口转发，到现在没弄明白？？？
  
4.Hysteria协议，由于协议比较冷门，许多客户端都不支持。

# 操作方法

## 服务器搭建

首先，你需要有一台服务器，而且必须拥有**公网ip**地址。

这里我选择的是**Microsoft Azure国际版**的学生服务器

(小声bb:用学校邮箱即可白嫖一年100刀的额度)。

或者去白嫖一年阿里云国内的学生服务器，当然，如果你的财力雄厚的话，你甚至可以买两台/doge

1.购买vps服务器，创建ECS实例(虚拟机),Azure服务器或者阿里云等服务器创建实例可以看一下[Google](https://google.com)。

  注意：
  1.推荐使用**私匙**链接ssh到你的服务器，安全性更高一些。但是使用用户名与密码登陆更容易记一点
  
  2.服务器端强烈建议使用**Linux**。推荐**CentOS 7/8**或者**Ubuntu**。这里我选用的是AZURE的Ubuntu18.
  
  3.域名问题：目前在用三家DNS解析商：国内**DNSPOD**，国外**CloudFlare/Freenom**。
  
  推荐使用DNSPOD进行解析，速度比较快。当然，如果爱整活的话推荐CloudFlare。


2.设置服务器安全策略端口开放，打开ssh开放端口22，http端口80，https端口443.

  如果嫌麻烦你甚至可以开放0-65535端口，但是我==强烈不建议==你这么做，这样做==非常非常非常==不安全。

**注意**

    建议在Linux系统中**禁用系统防火墙**
    使用服务器管理端的**安全策略**

Linux关闭防火墙

`sudo -i`

`ufw disable`

3.使用 [WinSCP](https://winscp.net/eng/index.php) / [MobaXterm](https://mobaxterm.mobatek.net/download.html) / [Git](https://gitforwindows.org/) 等工具进行ssh链接
    推荐使用**Git**，因为方便.

    `ssh -i 私匙.pem 用户名@公网IP/你的绑定域名`

4.链接到你的服务器，更新你的服务器配置。

    Ubuntu系统 更新系统配置
    
    `sudo -i`
    
    `apt-get update`
    
    `apt-get upgrade`

至此，你的服务器环境基本以及搭建完了。

## 四种方法操作实例

### Softether+OPENVPN端口转发

1.Softether服务端搭建

在ssh中执行

`sudo -i`

`wget http://www.softether-download.com/files/softether/v4.31-9727-beta-2019.11.18-tree/Linux/SoftEther_VPN_Server/64bit_-_Intel_x64_or_AMD64/softether-vpnserver-v4.31-9727-beta-2019.11.18-linux-x64-64bit.tar.gz`

`tar -zxvf softether-vpnserver-v4.31-9727-beta-2019.11.18-linux-x64-64bit.tar.gz`

`cd vpnserver`

2.执行安装程序

root用户下执行

`./install.sh`

根据安装提示输入1，应该是三次

3.在**当前目录**下使用

`./vpnserver start`

打开远程服务端的softether。

至此，服务端已经配置成功了。

4.打开你的**Windows本地电脑**

下载 [Softether Server](https://ghproxy.com/github.com/SoftEtherVPN/SoftEtherVPN_Stable/releases/download/v4.39-9772-beta/softether-vpnserver_vpnbridge-v4.39-9772-beta-2022.04.26-windows-x86_x64-intel.exe) ,点击可以直接下载。

安装server，Windows管理端。

5.打开server端。

选择“新建vpn服务端”，在地址中输入你的ip/域名，登陆默认为tcp443。

首次进入会提示你输入server端的密码，正常输入即可。

 ![server1](https://s2.loli.net/2022/05/24/zCIoryNmt9QGfex.png)

 ![server2](https://s2.loli.net/2022/05/24/It6nTCGAOc3sL1g.png)
 
 **进入账户后会有引导，下文的Windows server设置跟软件的引导差不多**

6.“**新建虚拟HUB**”-“**创建账户**，
新建一个账户账号密码随便设置，添加账户。

 ![HUB](https://s2.loli.net/2022/05/24/rxczvkdq2GijSZp.png)


7.打开“**启用SecureNat**”，在链接的时候DHCP分配会用到

 ![nat](https://s2.loli.net/2022/05/24/QFDqVdW75RflhaG.png)

8.打开右下角的openvpn设置，默认1194端口修改为53/67/68/69/80/443/8083/8081等可用端口

 ![open](https://s2.loli.net/2022/05/26/L8q69ZGcVDTaB2t.jpg)

**DNS(53)端口检测方法**

使用**nslookup**解析

1.在不进行认证的情况下，连接你的校园WiFi。

2.win+R执行cmd命令行（Windows）

3.输入`nslookup baidu.com`，看有无正确的返回值。

4.如果有正确的返回值，那么恭喜你，53端口可用。

**其他端口检测**

使用**nmap**抓包你们学校WiFi连接的IP地址

环境推荐**KALI Linux**

感谢 **@kmroiosn** 提供的KALI Linux环境以及技术支持！

 ![kali](https://s2.loli.net/2022/05/26/B2ijSFUlbguQA8H.jpg)

当然Windows也可以使用

9.经过**nslookup/nmap**抓包可以知道，53端口可以用，同时8083也可以用。

那我们输入~~53~~端口，并生成.open的配置文件

[后面测试53端口单链接无效，我们的学校只能走53+8083udp端口，softether支持多重udp端口]

10.下载对应的[OPENVPN](https://openvpn.net) 
      
并导入相关的配置文件

 ![opens](https://s2.loli.net/2022/05/24/oY6VGSxkRrvy3UW.png)

**注意：你可能需要去ovpn文件中修改服务器名称，softether提供的域名可能被墙，可能需要你修改为你自己的域名/ip**

11.登陆你在**虚拟hub**中添加的账号密码。

大功告成！

### ipv6+v2ray转发

**条件：校园网/服务器都支持ipv6**

1.连接到服务器ssh

2.搭建v2ray

这里我使用的233boy的脚本

`sudo -i`

`wget https://git.io/v2ray.sh`

`chmod 777 v2ray.sh`

`./v2ray.sh`

3.执行安装程序，正常搭建v2ray节点

==注意在服务器安全策略中开相应的端口！==

==注意在服务器安全策略中开相应的端口！==

==注意在服务器安全策略中开相应的端口！==

4.将你搭建的节点配置文件导入到手机/pc的v2ray

5.修改v2ray中的配置文件，将你的服务器公网ipv4改为你的服务器的ipv6地址

6.打开v2ray，连接就ok啦

**补充：懒人必备xui管理面板**

`sudo -i`

`wget https://raw.githubusercontent.com/vaxilu/x-ui/master/install.sh`

`chmod 777 install.sh`

`./install.sh`

正常安装即可

详情加申请https证书请见 [积木别倒的博客](https://jimubiedao.com/1166)

### gost端口转发

方法来自网络，自己也尝试搭建了一下。[遨游者](https://aoyouer.com/posts/udp53-gost-surfing/#:~:text=%E9%A6%96%E5%85%88%E6%88%91%E4%BB%AC%E8%A6%81%E5%88%A4%E6%96%AD%E6%A0%A1%E5%9B%AD%E7%BD%91%E7%BD%91%E5%85%B3%E6%98%AF%E5%90%A6%E6%94%BE%E8%A1%8C%E7%9B%AE%E6%A0%87%E7%AB%AF%E5%8F%A3%E4%B8%BA53%E7%9A%84udp%E6%95%B0%E6%8D%AE%E5%8C%85%EF%BC%8C53%E7%AB%AF%E5%8F%A3%E7%9A%84udp%E6%95%B0%E6%8D%AE%E5%8C%85%E9%80%9A%E5%B8%B8%E7%94%A8%E6%9D%A5%E8%BF%9B%E8%A1%8Cdns%E8%A7%A3%E6%9E%90%EF%BC%8C%E6%89%80%E4%BB%A5%E6%88%91%E4%BB%AC%E5%8F%AF%E4%BB%A5%E8%BF%9E%E6%8E%A5%E6%A0%A1%E5%9B%AD%E7%BD%91%20%28%E4%B8%8D%E8%A6%81%E7%99%BB%E9%99%86%E8%AE%A4%E8%AF%81%29%2C%E7%84%B6%E5%90%8E%E5%9C%A8cmd%E4%B8%AD%E8%BE%93%E5%85%A5,nslookup%20baidu.com%20%E8%BF%99%E6%A0%B7%E7%9A%84%E5%91%BD%E4%BB%A4%E6%9D%A5%E5%B0%9D%E8%AF%95%E6%9F%A5%E8%AF%A2%E5%9F%9F%E5%90%8D%E3%80%82)

这里也尝试搭建了一下，环境挺不好配置的。

服务端的53号端口太容易被dns服务给占用了，还要花心思给他关了，推荐使用V2ray而不是socks。

至于客户端，更难弄。需要设定gost客户端路径，给予全局变量。如果是socks代理还要搭配SStap，特别麻烦。。

**并不推荐GOST这种方法**

### Hysteria协议

最近新推出的一个冷门代理协议，开源地址：[Hysteria](https://www.toby.moe/hysteria);

服务端容易搭建，同V2ray搭建方法。

但是客户端由于协议支持软件少导致适用范围贼小，貌似PC端的V2rayNG支持了。至于Android客户端，官方给的**SagerNet**直接无法使用（万恶之源：miui）。

看着潜力挺大的这个协议，后期再关注一下

 ![Hy](https://s2.loli.net/2022/05/24/mZu6pSQDwaCEyPF.jpg)












