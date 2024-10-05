+++
title = '为asr 3000刷入openwrt并认证某师大校园网'
date = 2023-10-15T12:24:38+08:00
toc = true
tags = ["OpenWrt", "网络", "折腾"]
+++

## Why

为什么要费劲千辛万苦折腾校园网呢，老老实实用校园网不好吗？

当然~~是为了折腾~~，具体原因有很多......

首先就是因为校园网的限制两台设备让我不能忍受，还有就是较慢的网速（最贵的资费也只有30M 🙃）再者就是为了实现科学上网、去AD、内网穿透等功能。因此还是决定购入路由器用于宿舍

## 刷入OpenWrt
这次购买的是安博通abt的asr3000运营商定制机，配置方面为MT7981B 128M+256M，这颗u倒是和360 t7、H3C NX30 Pro、小米AX3000T等为同款，机身开孔倒是蛮多，散热应该不会太差，就是机身属实不是很好看，不过就冲这个价格还要什么自行车😎 在 pdd 菜园领券直接85拿下

等了几天快递，到手后便迫不及待开箱，只能说确实是难看，之后便下载资料准备刷机

刷机用到的工具资源教程来自恩山，原帖在这里，感兴趣的可以去看看  

[MT7981B安博通ABT-ASR3000刷机指南及固件](https://www.right.com.cn/forum/thread-8302537-1-1.html) 

先下载工具和固件，主要就用到putty和WinSCP。固件的链接扔这里了 

[OneDrive](https://1drv.ms/f/s!AuDdY4jBTla8h1Zkz7b4LymjnlD-?e=wprE6h) 

1. 先在浏览器登录后台，将配置（e-202306161250-cfg.tar.gz）导入后直接重启，导入配置的路径是
    > 系统工具---配置管理---导入配置

2. 开机之后再重启一次，这时telnet已经开启了，打开putty输入路由器的地址连接。用户名root 无密码

3. 在putty中输入以下命令用于开启ssh
```
echo arch aarch64_cortex-a53 300 >> /etc/opkg.conf
cd /tmp
wget https://downloads.openwrt.org/re ... ch64_cortex-a53.ipk
opkg install ./dropbear_2019.78-2_aarch64_cortex-a53.ipk -f /etc/opkg.conf --force-depends
/etc/init.d/dropbear enable
/etc/init.d/dropbear start
```
这一步是应该要将路由器..联网后下载文件..以开启ssh，这一步踩了个坑，在这说一下

因为是校园网，所以网线直连wan口当然是没有网络的，这时候想到可以桥接，将路由器连上手机的热点.因为asr3000默认开启了mesh，当mesh关闭时是不能桥接的，所以在把mesh关闭了之后便开启桥接，但是在桥接完之后却发现putty连接不上路由器了，只能放弃......

在想了很久之后，灵光一闪想到局域网的链接是可以的。于是马上在电脑上开启了之前搭建的PHP服务器环境，直接将文件扔到网站目录首页之后，尝试刷入，结果证明完全可行，，，就是好像有点麻烦

4. 在开启ssh之后，打开WinSCP，协议选scp之后输入账号密码连接，在tmp目录下上传ubooot文件（mt7981_abt-asr3000-fip-fixed-parts.bin）（其实这步在上一步操作时可以一块进行

    之后再打开putty，输入以下命令
```
cd /tmp
md5sum mt7981_abt-asr3000-fip-fixed-parts.bin
mtd write mt7981_abt-asr3000-fip-fixed-parts.bin FIP
mtd verify mt7981_abt-asr3000-fip-fixed-parts.bin FIP
```

在原来恩山的教程中 中间还有一步备份原厂固件的操作，这边被我忽略掉了，因为我本人用不上，有需求的可以去原帖看一看

5. 刷入成功之后拔电，之后按住MESH键插电，将电脑网卡设置IP：192.168.1.100，用网线连上路由器，浏览器登录192.168.1.1 进入uboot webui，刷入系统固件 （asr3000-squashfs-factory.bin）

6. 刷完之后重启，登录后台192.168.1.1，用户名为root，密码为password。即可享受OpenWrt

## 认证校园网
认证校园网这里用的是mentohust，比较老了但是还是可以正常使用，也可以使用[MiniEAP](https://github.com/updateing/minieap) 有兴趣的可以试试，我这里因为安装不上就没有用

### 安装mentohust
因为后续操作仍然需要下载文件，建议先把路由器连接上网络，我用的是桥接的办法，在路由器后台的

网络---无线---接口: ..apcli0.. | 类型: ..STA..（有两个任选其一即可）点击配置 开启AP客户端模式连接热点即可

![router-setting](https://img.panp.cc/2024/10/6d6e74b99c3f8cd816f931390fa9b40a.jpg)

这里直接使用打包好的ipk包，不需要再去编译，比较~~偷懒~~方便 来自[KyleRicardo/MentoHUST-OpenWrt-ipk](https://github.com/KyleRicardo/MentoHUST-OpenWrt-ipk)

在putty输入以下命令（只适用于asr3000 同款aarch64_cortex-a53架构
```
cd /tmp
wget https://github.com/KyleRicardo/MentoHUST-OpenWrt-ipk/releases/download/v0.3.1/mentohust_0.3.1-1_aarch64_cortex-a53.ipk
opkg update
opkg install libpcap
opkg install mentohust_0.3.1-1_aarch64_cortex-a53.ipk
```

### 配置参数
安装完成后，应该对mentohust的配置文件进行修改。
这里强烈建议直接使用输入命令行的方式进行配置，而不是使用修改文件的方式！！！我一开始直接使用修改文件的方式，折腾了一下午都失败，各种方式都尝试过，甚至想要把路由器砸了🙃 最终随手试了命令行，结果一遍成功......

如果用修改文件的方法去配置参数，在启动服务后会一直认证失败，而在putty中，失败的原因提示是乱码的。原因是因为putty默认的编码是utf-8，而错误原因的编码却是gb2312

![utf8](https://img.panp.cc/2024/10/73e5233f5eabecf864b107723fdbd39a.jpg)
![gb2312](https://img.panp.cc/2024/10/a354020072a438c81719a548c87c33e2.jpg)

在修改编码后可以看到错误原因是因为密码错误，但是其实密码是对的，这里不清楚如何解决。所以建议使用命令行设置参数，直接用命令行设置参数并保存到配置文件是完全可行的

在此配置参数之前建议安装mentohust的windows版，可以快速调整需要的参数，不用频繁修改文件 重启服务，一次性获得参数数据。

![tool](https://img.panp.cc/2024/10/9577a175b3d73dd97cf06fcff4e67c6b.jpg)

>用法:   ./mentohust [-选项][参数]
>
>选项：
>
>        -h 显示本帮助信息
>        -k -k(退出程序) 其他(重启程序)
>        -w 保存参数到配置文件
>        -u 用户名
>        -p 密码
>        -n 网卡名
>        -i IP[默认本机IP]
>        -m 子网掩码[默认本机掩码]
>        -g 网关[默认0.0.0.0]
>        -s DNS[默认0.0.0.0]
>        -o Ping主机[默认0.0.0.0，表示关闭该功能]
>        -t 认证超时(秒)[默认8]
>        -e 响应间隔(秒)[默认30]
>        -r 失败等待(秒)[默认15]
>        -l 允许失败次数[默认0，表示无限制]
>        -a 组播地址: 0(标准) 1(锐捷) 2(赛尔) [默认0]
>        -d DHCP方式: 0(不使用) 1(二次认证) 2(认证后) 3(认证前) [默认0]
>        -b 是否后台运行: 0(否) 1(是，关闭输出) 2(是，保留输出) 3(是，输出到文件) [默认0]
>        -y 是否显示通知: 0(否) 1~20(是) [默认5]
>        -v 客户端版本号[默认0.00表示兼容xrgsu]
>        -f 自定义数据文件[默认不使用]
>        -c DHCP脚本[默认udhcpc -i]
>例如:   ./mentohust -uusername -ppassword -neth0 -i192.168.0.1 -m255.255.255.0 -g0.0.0.0 -s0.0.0.0 -o0.0.0.0 -t8 -e30 -r15 -a0 -d1 -b0 -v4.10 -fdefault.mpf -cudhcpc -i

具体参数根据每个学校都是不同的，具体要靠自己调出来

这里直接贴我们学校的参数，比较简单，没有什么限制

```
./mentohust -u账号 -p密码 -neth1 -a1 -y0 -b1 -w
```

参数-u和-p没啥好说的，就是账号和密码

-n即wan口使用的网卡，在路由器后台的接口页可以看到一般类似eth0,eth1这类

-y这个需要注意，建议填个0，否则会一直报没有安装libnoify，而这个插件是桌面端用的，在openwrt上是安不了的

-w即写入配置文件数据，建议在尝试认证成功后再加上

-f用于填写抓包的参数 如果要填写应该填写/etc/mentohust/data.mpf

其他配置一般可以直接默认，或者填写在windows中认证成功的数据

### 抓包（可选）
如果一直认证失败而其他参数都正确时，可以尝试抓包提供数据

抓包要用到锐捷认证的客户端还有Mentohust Tool

打开Mentohust Tool后，选择网卡后建议勾选“集成8021x.exe”和“集成W32N55.dll”这两个选项。之后即可开始抓包，打开锐捷客户端登录校园网，认证成功后会自动抓包完成，将抓包文件.mpf直接保存下来即可

在[Mentohust项目地址](https://code.google.com/p/mentohust/downloads/list)中的Tool工具不知道为什么抓包失败，在各类论坛找了很久终于找到一个能够使用的Tool。本文章所有提到的文件在开头OneDrive链接中均有。

这里直接引用wiki上的说明

>-f：由于MentoHUST内置数据是与xrgsu兼容的（即如果用xrgsu能认证成功，用MentoHUST不设置这个参数就也能认证成功），
有些学校关闭了xrgsu的认证（一般提示“不允许使用的客户端类型”），这时可以将8021x.exe和W32N55.dll复制到/etc/mentohust目录，
如果认证失败，再将SuConfig.dat复制到/etc/mentohust目录一般即可认证成功。如果还失败就需要抓包并指定该参数。
到http://code.google.com/p/mentohust/downloads/list 下载MentoHUST抓包工具，然后运行其中的MentoHUSTTool，视情况勾选是否
“集成8021x.exe”和“集成W32N55.dll”（建议勾选），点击“开始”，运行“锐捷”，捕获锐捷认证时的数据包，等待抓包结束保存文件。
然后在Linux下将数据文件路径指定在这个参数中，如果没有勾选“集成8021x.exe”和“集成W32N55.dll”，则还要将8021x.exe与
W32N55.dll复制到数据文件所在目录，接下来就可以开始认证了。认证失败的话，再将SuConfig.dat也复制到该目录即可认证成功。

W32N55.dll、8021x.exe，SuConfig.dat这三个文件在锐捷客户端的目录下可以找到

## 其他玩法
最终没有意外的话，应该已经成功认证校园网✌️，如果仍然被限制设备，被封禁校园网账号，可以参考这篇文章，应该可以解决。我目前没有这个需求，就没有细研，需要编译，怪麻烦的......

[关于某大学校园网共享上网检测机制的研究与解决方案](https://www.sunbk201.site/posts/crack-campus-network)

### 多拨
既然成功刷入openwrt并且认证成功，按道理来说自然是可以实现多拨

只需要重新添加一个虚拟网口，再启动一个mentohust服务配置参数即可（按道理，没试过，一些学校会限制同时在线一个有线连接和一个无线连接，很不巧，我就是 🤡）
同时在线两个mentohust需要删除pid文件，可以参考下这个 [issue](https://github.com/KyleRicardo/MentoHUST-OpenWrt-ipk/issues/20)
 
 具体的多拨和负载均衡都教程可以直接在网上查找，基本适用

 MiniEAP在多拨方面比较有优势

 这是MiniEAP项目主页对此的介绍

 > 数据包修改同样由插件完成 可以在不修改主要认证流程的情况下适配各种环境。..可以启用多个插件，也可将一个插件启用多次。..程序会让标准 EAP 算法生成的数据包按照命令行中 --module 参数的顺序让数据包流经这些插件。目前提供一个锐捷 v3 认证算法插件和一个打印流经的数据包大小的示例插件。

 ### Clash

这个没啥好说的，应该算是路由器刷机必安的插件之一

恩山大佬提供的固件已经内置了OpenClash，不需要再安装。如果没有的话，OpenClash的项目地址在这里，可以直接下载ipk安装 [OpenClash](https://github.com/vernesong/OpenClash)

也可以选择ShellClash，就是没有可视化面板，比较麻烦点 [ShellClash](https://github.com/juewuy/ShellClash)

### 去广告
这类去广告的插件有AdGuard Home、广告屏蔽大师 Plus+等插件，不过这类插件貌似效果一般，我个人还是直接在浏览器安装拓展

### 内网穿透
这个可能会是最常用的功能，目前还没配置

想法是将路由器内网穿透后，链接电脑，配置远程后，实现远程访问电脑🤔

目前想依靠frp和京东云买的活动服务器实现
