---
title: 为什么校园网这么贵？
date: 2017-09-10 13:04:19
tags: 
  - vps
  - shadowsocks
  - ipv6
  - proxifier
categories:
  - geek
---

学校在9月份把校园网每月**免费20G**改成了**25G**，也取消了超出部分的阶梯计价，统一为**2元/G**。大家看到免费流量变多了，总是很乐意的。然而对于很多同学而言，*尤其是高年级同学*，每个月用掉**50G+**也是司空见惯。<del>（什么？当然是看papaer烧掉的流量呀，不然你以为我们每天在搞什么，滑稽）</del>早知道就去开通个移动宽带了对不对？屁！辣鸡移动网吃鸡都得开加速器。校园网只不过是在守望开团的时候*稳定*掉包，也只不过是在吃鸡决赛圈突然瞬移骑脸给对手身心双重打击，**丝毫不影响游戏体验**。

不过我不是来给教育网写软文的，我要告诉大家如何通过一些**基本操作**实现**全局ipv6**，<font color=red>获得流量和钱包的**生命大和谐**</font>。*（强行写在摘要里让你们看到，别走！别走！*<!-- more -->

首先，你需要一个<del>女朋友</del>有ipv6的网络环境，教育网基本都有。另外，你还需要：
* 服务器端 
  * 支持ipv6的服务器
  * 搭载app: shadowsocks server
* 客户端
  * 操作系统：Win/MacOS 
  * app: shadowsocks client
  * app: proxifier

看完这篇教程后，你就可以搭建出一个**<font color=red>全局流量走ipv6</font>**的环境，有ipv6的地方，就是江湖。看直播、刷剧、更新软件游戏、**看paper、学习**，统统不在话下。同学们也不同担心速度的问题，有线ipv6环境下，steam依然能以4M/s的速度下载**学习资料**。<del>我不买游戏不会玩游戏根本不知道什么是steam</del>
![](/images/ipv6/steam.jpg)
<img src="/images/ipv6/research.jpeg" alt="" style="width: 300px;">

> *插播一条广告，没想到吧*
> ![](/images/ipv6/campnet.png)
> **万一这篇文章你看不下去了，你还可以用亲室友 @ClumsyLee 开发的CampNet管控一下自己的流量，但目前仅支持清华校园网。**
> 点击<font color=607fa6>[阅读原文](https://mp.weixin.qq.com/s/uvM9zvXqH6rq7AEXjEIB7g)</font>（文风雷同纯属他学我！！！）

**<font color=red>而我要讲的，只要是支持ipv6的网络就可以！！win mac都支持！！非常高级！！</font>**

## 非基本操作之一：VPS

VPS的搭建不是基本操作，毕竟还是有一点技术门槛，还要消耗一丢丢人民币。如果你认为自己没功夫去克服技术门槛，或者不想消耗珍贵的人民币，那有以下几种方案可以替代：
* 如果你是汉子
  * 找一个人傻钱多活好的基友，求他。
  * 找一个人傻钱多活好眼还瞎的女朋友，求她。
  * 别求我，**我不在**。
* 如果你是妹子
  * 请联系我。

谢谢你继续往下看，我就喜欢你这样爱学习<del>钱多</del>的女同学，有机会一起：
![](/images/ipv6/study.jpg)

### 什么是VPS？

Virtual Private Server，说白了就是租用的一台为你提供服务的机器。为什么我们需要vps？因为我们希望vps代替我们去访问整个互联网，而我们与vps之间搭建**ipv6的通道**。

![](/images/ipv6/structure.png)

这样便可以通过IPv6间接地访问到**整个Internet**。当然，可以多用户共享一个vps，降低成本，提高效率。

### 租用VPS

国内外提供VPS的云服务商非常多，**建议大家选择国内的服务商，不仅快速、稳定，最重要的是搭建前后你能访问到Internet的部分<font color=red>完全没有区别</font>，可以说用户体验非常的好。**

![](/images/ipv6/imhappy.jpg)

说的好，我选[DigitalOcean](https://m.do.co/c/3e154132de86)（👈这个是博主DigitalOcean的推广链接，介意的请点[这个](https://www.digitalocean.com)）。

另外给大家奉上github的[education pack](https://education.github.com/pack)，里面有更多DigitalOcean优惠。

接下来可以创建你的云服务器了，DigitalOcean把其称之为Droplet，创建Droplet及如何远程登录到服务器请移步[官方新手教程](https://www.digitalocean.com/community/tutorials/how-to-create-your-first-digitalocean-droplet)，其他平台类似。**记得勾选<font color=red>Enable IPv6</font>选项**

### 部署Shadowsocks server

登录至你的服务器后，就可以安装Shadowsocks server了。

* 安装
  * Debian/Ubuntu
  ```
  apt-get install python-pip
  pip install shadowsocks
  ```
  * CentOS
  ```
  yum install python-setuptools && easy_install pip
  pip install shadowsocks
  ```

* 配置ssserver
  建议[用配置文件进行配置](https://github.com/shadowsocks/shadowsocks/wiki/Configuration-via-Config-File)
  * 获取你服务器的IPv6地址，DigitalOcean中可以在Droplet管理页面直接看到
  * 编写配置文件，保存在服务器上
  ```json
  {
      "server":"ipv6_address_of_your_server",
      "timeout":600,
      "method":"aes-256-cfb",
      "server_port":60001,
      "local_port":1080,
      "password":"your_password"
  }
  ```
  * 感谢 @ClumsyLee 大佬指点，<font color=red>更推荐同时监听ipv4和ipv6</font>，配置如下（请确保你所安装的是通过pip/pip3安装的shadowsocks，而不是shadowsocks-libev。否则同时监听v4和v6的配置写法略有不同）：
  ```json
  {
      "server":"::",
      "timeout":600,
      "method":"aes-256-cfb",
      "server_port":46001,
      "local_port":1080,
      "password":"your_password"
  }
  ```
  * 比如将上述文件保存为`/etc/shadowsocks.conf`
  * 当然，你可能还需要打开防火墙的相关端口（以46001端口为例）
  ```
  ufw allow 46001
  ```
  * 运行
  ```
  ssserver -c /etc/shadowsocks.conf
  # 或者后台运行（感谢 @ClumsyLee 大佬指点）
  sudo ssserver -c /etc/shadowsocks.conf -d start --user nobody
  # 以及更新/etc/rc.local实现开机自动启动
  ```
  ssserver即可正常工作了。
  * *以上配置中`server_port`，`password`等参数是完全可以自由配置的。*

**服务器端的配置就基本完成了，还是很简单的。**

> 「我觉得一点也不简单QAQ」
> 「我这里有一个已经搭好的VPS，想不想要？」
![](/images/ipv6/zhuangbixia.jpg)

## 基本操作之二：Shadowsocks客户端

如果你已经成功通过<del>撒娇打滚卖萌</del>技术手段拿到了服务器端的shadowsocks配置方案，恭喜你离生命的大和谐只有两步之遥啦。

接下来需要在你的客户端上安装Shadowsocks client，以连接到VPS上的Shadowsocks server。

### 安装并配置Shadowsocks client
* 安装Shadowsocks
  * Windows
    请**想办法**下载[Shadowsocks.exe](https://github.com/shadowsocks/shadowsocks-windows/releases/download/4.0.6/Shadowsocks-4.0.6.zip)（*没错你需要特别的打开方式*
  * MacOS
    下载安装[ShadowsocksX-NG](https://github.com/shadowsocks/ShadowsocksX-NG/releases/download/v1.6.1/ShadowsocksX-NG.1.6.1.zip)
* 运行Shadowsocks
* 服务器->编辑服务器->添加
* 将你的ss服务端相应配置填入
![](/images/ipv6/ss-win.jpeg)
  * 服务器IP请填写**服务器的ipv6地址**；
  * 服务器端口对应上文配置中的`server_port`；
  * 密码对应上文配置中的`password`；
  * 代理端口保持默认`1080`即可，如果你理解它的含义当然也可以自定义；
  * 其他选项可以保持默认，新版本的Shadowsocks可能还会有插件选项，这里不需要设置。

### 测试配置可用性

如果配置正确的话，那么你的**浏览器**以及一些可以设置**SOCKS 5**代理的应用应该已经可以顺利与Shadowsocks对接了。

这意味着你可以让浏览器走ipv6流量了。

本质上Shadowsocks是一个帮助科学上网的软件，所以如果Shadowsocks设置为**自动代理模式**，那么它会在访问一些无法正常访问的网站（如[Google](https://www.google.com)）时走你所设置的代理服务器，而访问<del>局域网内</del>正常网站（如[Baidu](https://www.baidu.com)）时则是直接连接，不通过代理服务器。

* 所以当Shadowsocks为自动代理模式时，
  * 你租用的vps是国外节点，你将可以走ipv6流量连接至代理服务器，进而成功访问[Google](https://www.google.com)等网站；
  * 你租用的vps是国内节点，将没有任何变化。
* 而当Shadowsocks为全局模式时，
  * 你租用的vps是国外节点，理论上你可以访问到任何你想访问的网站，而且所有流量是ipv6的形式。因此即使你不登录校园网账号，依然可以通过浏览器正常上网。**但是，对于一些视频网站，你将会受到地域版权限制，因为你是以国外ip的身份访问它们的**，不过[斗鱼](https://www.douyu.com)等直播网站倒是没有限制地域；
  * 你租用的vps是国内节点，你依然无法访问[Google](https://www.google.com)等网站，但是访问其他网站均是以ipv6的形式，可以随心所欲畅游各大视频网站，而校园网不会收取你任何费用。

所以，我推荐大家选国内vps**也不是毫无道理的**，看大家各自的需求是什么咯。

## 基本操作之三：Proxifier

我相信将Shadowsocks设为全局模式，ipv6下使用浏览器已经可以满足相当一部分同学的需求。但是，很多时候其他软件或进程也非常烧流量，比如windows的系统更新、百度云网盘<del>以及Steam、Battle.net</del>等。这些程序通常不支持代理或者不支持SOCKS 5的代理，没关系，我们可以通过**代理的转发**强行让所有程序都走ipv6。

只需要另一个软件：Proxifier，在windows和MacOS平台都有。

Proxifier的基本思路是**把本地所有网络流量通过可配置的规则按情况转发到对应的端口**。首先明确，我们的Shadowsocks只监听本地（`localhost`或`127.0.0.1`）的1080端口（默认设置），而且代理的类型是SOCKS 5（不是HTTP，也不是SOCKS 4）。现在我们就要把所有流量都转发到`127.0.0.1:1080`上，以实现真正的全局ipv6。

### 下载安装Proxifier

鸽你不是两三天。

