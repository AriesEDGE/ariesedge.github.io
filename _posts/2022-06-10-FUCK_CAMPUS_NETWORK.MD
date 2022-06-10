---
layout: post
title: "这可能是最全面的校园网端口转发的文章"
date:   2022-06-10
tags: [network]
comments: true
author: Aries
---

    注意，这是以学习目的为主，请勿用于非法用途，谢谢。
    注意:需要一台服务器。当然，国际的节点也不是不行，甚至还可以实现翻墙。。

<!-- more -->

# 思路

思路基于UDP端口转发。



有其他思路欢迎联系我,[Telegram](https://t.me/awwwwwwesome).



1.IPv6



服务器有ipv6地址.



2.IPv4



其中又分为四个方法

(1)**V2ray**的mKCP协议(基于X-UI)

(2)**Hysteria**协议

(3)OPENVPN+SOFTEHTER

(4) ~~GOST/BROOK隧道/iptables转发~~



# 测试效果



1.IPv6：

​	有就很好，效果不错(朋友反馈)，插一句，我们学校还是6in4(都啥年代了....)



2.IPv4：

​	(1)V2ray:

​	貌似效果最好，也简单粗暴.但是mkcp可能不安全???国内鸡没事，海外的可能被GFW日掉

​	(2)Hysteria:

​	高速率,低延迟,协议冷门不易被墙掉.但是暴力发包VPS端概率封号,而且客户端不好配置

​	(3)OPENVPN+SOFTETHER:

​	利于计费(账户设置)，走TAP级真全局代理。其他也找不出啥优点，缺点很多，易被GFW墙，而且某度搜的全是这种落后的方法...

​	(4)~~GOST/BROOK隧道/iptables转发~~:

​	玩不明白 且 需要额外加一台中转鸡.



# 环境配置+搭建



## 服务器搭建



首先，你需要有一台服务器，而且必须拥有**公网ip**地址。



这里我选择的是**Microsoft Azure国际版**的学生服务器.

(小声bb:用学校邮箱即可白嫖一年100刀的额度)。

或者去白嫖一年阿里云国内的学生服务器，最近618腾讯云那个也不错。

当然，如果你的财力雄厚的话，你甚至可以买两台/doge



1.购买vps服务器，创建ECS实例(虚拟机),Azure服务器或者阿里云等服务器创建实例可以看一下[Google](https://google.com)。

  注意：
  1.推荐使用 **私匙** 链接ssh到你的服务器，安全性更高一些。但是使用用户名与密码登陆更容易记一点

  2.服务器端强烈建议使用 **Linux** 。推荐**CentOS 7/8**或者**Debian**。这里我选用的是AZURE的Debian11.

  3.域名问题：目前在用三家DNS解析商：国内**DNSPOD**，国外**CloudFlare/Freenom**。

  推荐使用DNSPOD进行解析，速度比较快。当然，如果爱整活的话推荐CloudFlare。

2.设置服务器安全策略端口开放，打开ssh开放端口22，http端口80，https端口443.

  如果嫌麻烦你甚至可以开放0-65535端口，但是我==强烈不建议==你这么做，这样做==非常非常非常==不安全。



**注意**

    建议在Linux系统中**禁用系统防火墙**
    使用服务器管理端的**安全策略**



Linux关闭防火墙(方便后面部署)

`sudo -i`

`ufw disable`



3.使用 [WinSCP](https://winscp.net/eng/index.php) / [MobaXterm](https://mobaxterm.mobatek.net/download.html) / [Git](https://gitforwindows.org/) 等工具进行ssh链接
    推荐使用**Git**，因为方便.



    `ssh -i 私匙.pem 用户名@公网IP/你的绑定域名`



4.链接到你的服务器，更新你的服务器配置。



    Debian系统 更新系统配置 (同理CentOS改yum就OK)
    
    `sudo -i`
    
    `apt-get update`
    
    `apt-get upgrade`



至此，你的服务器环境基本搭建完了。



## 端口测试



### DNS(53)端口检测方法NSLOOKUP



1.在不进行认证的情况下，连接你的校园WiFi。

2.win+R执行cmd命令行（Windows）

3.输入`nslookup baidu.com`，看有无正确的返回值。

4.如果有正确的返回值，那么恭喜你，53端口可用。



### 其他端口检测



使用**nmap**抓包你们学校WiFi连接的IP地址

环境推荐**KALI Linux** (当然Windows也可以使用)

感谢 **@kmroiosn** 提供的KALI Linux环境以及技术支持！

(一般53.67.68.69.8083.8383这几个比较常用)


注意：

某些系统53等端口可能会占用,使用

`lsof -i:53`


至于关闭53端口进程占用，请使用Google搜索.

![kali](https://s2.loli.net/2022/05/26/B2ijSFUlbguQA8H.jpg)


## 方法实例(IPv4）



# 1.V2ray+XUI



方法基于mKCP协议(UDP)搭建,貌似QUIC也行,这里重点说说mKCP.



分两步，服务端+客户端



## V2ray服务端搭建



1.安装XUI面板



```shell
sudo -i

wget https://raw.githubusercontent.com/vaxilu/x-ui/master/install.sh

chmod 777 install.sh

./install.sh
```



2.进入XUI面板



按图设置



![89a16c4c32bc445f0355d5916fe52e1.png](https://s2.loli.net/2022/06/10/mu5eL7NqEXvHGYb.png)





3.导入你在XUI创建的节点至V2rayN客户端即可.





## 2.Hysteria 歇斯底里



是一个功能丰富的，专为恶劣网络环境进行优化的网络工具（双边加速），比如卫星网络、拥挤的公共 Wi-Fi、在中国连接国外服务器等。 

基于修改版的 **QUIC 协议** (基于UDP协议)。

说白了就是魔改的QUIC。
以1.5倍发UDP包。



## **Hysteria注意事项:**



**请务必把上行下载速度调低！！！VPS服务端封号自负！！！**

**请务必把上行下载速度调低！！！VPS服务端封号自负！！！**

**请务必把上行下载速度调低！！！VPS服务端封号自负！！！**



## Hysteria环境搭建



大体分为2步，服务端搭建，客户端搭建.



### Hysteria服务端



1.请在VPS的安全策略或者防火墙中放行端口！！！比如53,67,68等端口。

2.部署Hysteria服务

使用SSH连接到你的VPS.

这里我是采用的 [小御坂的破站](https://owo.misaka.rest/Hysteria-script/) 提供的一键搭建脚本，这个更容易上手与配置。

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



### Hysteria客户端



[Git开源地址](https://github.com/HyNetwork/hysteria)

Github下载加速:将 github.com 换成 git.imaries.cf ,自己用CloudFlare Worker搭建。



### iOS端 



使用 **ShadowRocket** 直接导入Hysteria协议即可.

**ShadowRocket**需要美区账号并且需要购买，你可以去github上找找(手上无iOS设备)有第三方安装。



### Android端

1.使用 **SagerNet** 导入Hysteria协议即可，注意可能要下载额外的Hysteria Plugin插件(请到Google Play下载).



**注意：国产ui需要将Hysteria Plugin自启动打开**

**注意：国产ui需要将Hysteria Plugin自启动打开**

**注意：国产ui需要将Hysteria Plugin自启动打开**



导入协议时候需要把  **"允许不安全的连接"**  打开



![e440dba83a44ea1ba7fdcfff9305f09.jpg](https://s2.loli.net/2022/06/10/V5GMsePu129UTqz.jpg)



2.基于LINUX **root** 的新方法,麻烦是麻烦了点，但是效率高.



**注意：手机需要root**



[工具+环境配置](https://smyisaries.lanzoum.com/iCC9105kzckd)



官方给的SagerNet的方法效果太难受了.



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

这里系统走的是HTTP代理，还需要走一个SOCKS代理走全局



开始Google，发现一个走root的全局代理的 **ProxyDroid** ，这个软件可以走socks也可以走http,那我们就叫他走SOCKS吧.



(拒绝垃圾国内软件从我做起.点名批评某号称全球最大的中文搜索引擎..)



抱着试试的心态，啊哈，直接成功了.有一点点小激动，哈哈.

 ![proxy.png](https://s2.loli.net/2022/05/29/nROMlJ1f3sko7LQ.png)

这样



系统代理走HTTP,PROXYDROID走SOCKS实现全局代理(借用LINUX的思路，同理，Linux TUN/Windows也可以这样走)



### Windows端



我试过好多方法，hy官方+SSTap\Netch\V2ray(socks5)，单走V2ray Hy核心。



最后采用了 hy官方内核+Proxifer(SOCKS)+系统HTTP.真全局代理



虽然新版的V2ray已经支持了hy核心，但是代理效果差，微信/QQ这些毒瘤软件会走自己的私人端口(非TAP/TUN那种走虚拟网卡的权限)，所以最终采用了Hysteria核心+Proxifer 的socks5代理



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

添加 127.0.0.1 端口1080，配置SOCKS代理

 ![配置prxoy1.png](https://s2.loli.net/2022/05/28/rkQsvo9pcZHIK3u.png)

 ![配置proxy2.png](https://s2.loli.net/2022/05/28/ndFohStmEeOU7Aa.png)



**Windows代理设置**



以Windows11为例，打开设置，直接搜索"代理"



设置代理127.0.0.1，端口1081，让系统走HTTP代理



![e93ea3047d654832dba52f9d3de12a5.png](https://s2.loli.net/2022/06/10/sfIwYxKqu3hbyDJ.png)



### 测试效果以及小结

 ![speedtest测试.jpg](https://s2.loli.net/2022/05/28/EpOJAoBW24vL6PN.jpg) 

可以看出，延迟特别低速度也可以(香港Azure服务器)，但是同上文说，一定要 **限制速率** ，封号概不负责。



### SOFTEHTHER+OPENVPN.



1.Softether服务端搭建



在ssh中执行

```shell


sudo -i

wget http://www.softether-download.com/files/softether/v4.31-9727-beta-2019.11.18-tree/Linux/SoftEther_VPN_Server/64bit_-_Intel_x64_or_AMD64/softether-vpnserver-v4.31-9727-beta-2019.11.18-linux-x64-64bit.tar.gz

tar -zxvf softether-vpnserver-v4.31-9727-beta-2019.11.18-linux-x64-64bit.tar.gz

cd vpnserver

./install.sh
```



2.执行安装程序



根据安装提示输入1，应该是三次



3.在**当前目录**下打开



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



10.下载对应的[OPENVPN](https://openvpn.net) 
      
并导入相关的配置文件



 ![opens](https://s2.loli.net/2022/05/24/oY6VGSxkRrvy3UW.png)

**注意：你可能需要去ovpn文件中修改服务器名称，softether提供的域名可能被墙，可能需要你修改为你自己的域名/ip**



登陆你在**虚拟hub**中添加的账号密码。



大功告成！



### IPv6+v2ray



**条件：校园网/服务器都支持ipv6**



注意：

​			最好是双栈服务器。

​			如果你是IPv6 ONLY的VPS,建议你使用CloudFlare Warp来实现对ipv4资源的访问



搭建脚本(来自Misaka大佬)

`wget -N --no-check-certificate https://raw.githubusercontents.com/Misaka-blog/Misaka-WARP-Script/master/misakawarp.sh && bash misakawarp.sh`



1.连接到服务器ssh



2.搭建v2ray



233boy大佬的脚本

```shell
sudo -i

wget https://git.io/v2ray.sh

chmod 777 v2ray.sh

./v2ray.sh
```



或者 XUI(推荐这个，方法同v4搭建一样)

```shell

sudo -i

wget https://raw.githubusercontent.com/vaxilu/x-ui/master/install.sh

chmod 777 install.sh

./install.sh

```



3.执行安装程序，正常搭建v2ray节点(端口啥的无所谓)



==注意在服务器安全策略中开相应的端口！==

==注意在服务器安全策略中开相应的端口！==

==注意在服务器安全策略中开相应的端口！==



4.将你搭建的节点配置文件导入到手机/pc的v2ray



5.修改v2ray中的配置文件，将你的服务器公网ipv4改为你的服务器的ipv6地址



6.打开v2ray，连接就ok啦



详情加申请https证书请见 [积木别倒的博客](https://jimubiedao.com/1166)

















