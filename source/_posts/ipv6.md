---
title: 为什么校园网这么贵？
date: 2017-09-10 13:04:19
tags: 
  - vps
  - shadowsocks
  - ipv6
  - proxycap
categories:
  - geek
---

学校在9月份把校园网每月**免费20G**改成了**25G**，也取消了超出部分的阶梯计价，统一为**2元/G**。大家看到免费流量变多了，总是很乐意的。然而对于很多同学而言，*尤其是高年级同学*，每个月用掉**50G+**也是司空见惯。<del>（什么？当然是看papaer烧掉的流量呀，不然你以为我们每天在搞什么，滑稽）</del>早知道就去开通个移动宽带了对不对？屁！辣鸡移动网吃鸡都得开加速器。校园网只不过是在守望开团的时候*稳定*掉包，也只不过是在吃鸡决赛圈突然瞬移骑脸给对手身心双重打击，**丝毫不影响游戏体验**。

不过我不是来给教育网写软文的，我要告诉大家如何通过一些**基本操作**实现**全局ipv6**，获得流量和钱包的**大和谐**。*（强行写在摘要里让你们看到，别走！别走！*<!-- more -->

首先，你需要一个<del>女朋友</del>有ipv6的网络环境，教育网基本都有。另外，你还需要：
* 服务器端 
  * 支持ipv6的服务器
  * 搭载app: shadowsocks server
* 客户端
  * 操作系统：Win/MacOS 
  * app: shadowsocks client
  * app: proxycap

看完这篇教程后，你就可以搭建出一个**<font color=red>全局流量走ipv6</font>**的环境，有ipv6的地方，*就是江胡*。看直播、刷剧、更新软件游戏、**看paper、学习**，统统不在话下。同学们也不同担心速度的问题，有线ipv6环境下，steam依然能以4M/s的速度下载**学习资料**。<del>我不买游戏不会玩游戏根本不知道什么是steam</del>
![](/images/ipv6/steam.jpg)

> *插播一条广告，没想到吧*
> ![](/images/ipv6/campnet.png)
> **万一这篇文章你看不下去了，你还可以用亲室友开发的CampNet管控一下自己的流量，但目前仅支持清华校园网。**

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

我选[DigitalOcean](https://m.do.co/c/3e154132de86)（👈这个是博主DigitalOcean的推广链接，介意的请点[这个](https://www.digitalocean.com)）。

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
  * 比如将上述文件保存为`/etc/shadowsocks-ipv6.conf`
  * 当然，你可能还需要打开防火墙的相关端口（以60001端口为例）
  ```
  ufw allow 60001
  ```
  * 运行
  ```
  ssserver -c /etc/shadowsocks-ipv6.conf
  # 或者后台运行
  nohup ssserver -c /etc/shadowsocks-ipv6.conf &
  ```
  ssserver即可正常工作了。

**服务器端的配置就基本完成了，还是很简单的。**

> 「我觉得一点也不简单QAQ」
> 「我这里有一个已经搭好的VPS，想不想要？」

## 基本操作之二：Shadowsocks客户端

鸽你不是两三天。