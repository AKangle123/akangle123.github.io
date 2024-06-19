---
title: VMware虚拟机使用主机代理软件
created: '2024-06-19T09:44:25.585Z'
modified: '2024-06-19T10:03:31.484Z'
---

# VMware虚拟机使用主机代理软件
## 前言
网上的教程参差不齐，有的还挺具有误导性的，于是打算在这里记录一下我的做法。<br />
我的做法能够解决以下这些网上的教程存在的问题：<br />
拨号上网的环境，无法使用主机代理。<br />
网络环境改变后（比如路由器分配的 ip 改变了），需要重新设置代理。<br />
如果主机没有连接网络，那虚拟机和主机没法通过网络通信（虽然此时代理也用不了）<br />
这篇文章将会尽可能详细的描述操作过程，同时解释其中的原理以帮助读者理解，一些我认为读者没必要知道的内容将会省略。<br />

## 预备知识
### 网络地址转换（NAT）

不需要深入理解，你只需要知道，路由器具有它的功能，你也可以把使用 NAT 的地方看成一个简单的路由器（即使是虚拟的）。<br />

### VMware 虚拟网
VMware 具有“虚拟网络”的功能，而我们可以利用此功能虚拟出一个“路由器”，我们只需要将主机和虚拟机连接在同一个“路由器”下，便可以实现双向通信，这就是虚拟机使用主机侧代理软件的核心思路。<br />

### 虚拟网络编辑器
这一步主要起到验证作用，为的是知道 VMware 自动创建的 NAT 类型虚拟网是否存在，叫什么名字。一般而言只需要检查，并没有什么需要修改的内容。<br />

VMware 自带了一个虚拟网络编辑器，我们可以在这里查看所有 VMware 所创建的虚拟网的信息。<br />

<div align=center>
<img src="https://github.com/AKangle123/akangle123.github.io/raw/master/attachments/Clipboard_2024-06-19-17-53-21.png">
</div>

打开之后点击一下“更改设置”来显示全部内容。<br />
在这里我们注意到，VMware 默认已经为我们创建了一个 NAT 类型的虚拟网，其名称为 VMnet8（需要记一下这个名字）。<br />
（ VMware 默认都会创建一个 NAT 类型虚拟网，故此处不再赘述如何创建并配置一个 NAT 虚拟网）<br />
<div align=center>
<img src="https://github.com/AKangle123/akangle123.github.io/raw/master/attachments/Clipboard_2024-06-19-17-55-42.png">
</div>

我们可以将这个 NAT 类型的虚拟网看成一个虚拟的路由器，于是我们只需要同时将主机和虚拟机连接在这个“路由器”下面，就可以实现双向通信了，接下来我将陈述该怎么做。<br />
（只要“将主机虚拟适配器连接到此网络”保持勾选状态，主机就已经连接到这个“路由器”了）<br />

## 把虚拟机连接到“路由器”
这一步我们要做的事情是：将虚拟机连接到上述虚拟网。<br />

首先，每台虚拟机都有一个“网络适配器”的选项。<br />
<div align=center>
<img src="https://github.com/AKangle123/akangle123.github.io/raw/master/attachments/Clipboard_2024-06-19-17-56-00.png">
</div>


这个选项可以在 顶栏的“虚拟机”/在虚拟机上右键 -> "设置" 找到<br />

在这里，我们可以选择要将虚拟机连接到“虚拟网络编辑器”中的哪个网络。<br />
我们将网络选择为之前记住的：VMnet8。

<div align=center>
<img src="https://github.com/AKangle123/akangle123.github.io/raw/master/attachments/Clipboard_2024-06-19-17-56-15.png">
</div>

这样，就相当于把虚拟机连接在虚拟“路由器”的下面了。<br />

## 配置代理
在这一步之前，我们已经将虚拟机和主机连接在了同一个“虚拟路由器”的下面，现在，我们只需要知道主机在该“虚拟路由器”下的 ip 地址和代理端口，便可以完成代理的设置。<br />

### 主机的 ip 地址
我们可以打开主机的任务管理器，找到上面记住的 VMnet8 。<br />

<div align=center>
<img src="https://github.com/AKangle123/akangle123.github.io/raw/master/attachments/Clipboard_2024-06-19-17-56-27.png">
</div>

这个地址就是主机在这个“虚拟路由器”下的地址，我们需要将其记录下来。<br />

### 代理端口

以代理软件“clash”为例。<br />

首先，我们需要打开“允许来自局域网的连接”来保证来自虚拟机的连接不会被主机拒绝。<br />
<div align=center>
<img src="https://github.com/AKangle123/akangle123.github.io/raw/master/attachments/clash1.PNG">
</div>
然后，我们可以在软件主界面的底部看到 SOCKS5 和 HTTP 代理端口<br />
<div align=center>
<img src="https://github.com/AKangle123/akangle123.github.io/raw/master/attachments/clash2.PNG">
</div>
## 在虚拟机中配置代理
此处以 Ubuntu 为例。<br />
我们已经知道了主机在“虚拟路由器”下的地址以及代理软件的端口，现在我们只需要在虚拟机当中将代理信息设置为上方记录的 ip 地址与端口即可。<br />

打开 Ubuntu 的设置，选择“网络”选择卡。<br />
<div align=center>
<img src="https://github.com/AKangle123/akangle123.github.io/raw/master/attachments/Clipboard_2024-06-19-18-02-30.png">
</div>

可以看到其下有一个“网络代理”设置。<br />
<div align=center>
<img src="https://github.com/AKangle123/akangle123.github.io/raw/master/attachments/3897726368.png">
</div>
点开，将代理模式调成手动，输入上方记录的 主机 ip 地址 和 代理软件端口<br />

那么虚拟机的代理就配置完成了，我们可以尝试使用一下。<br />
