+++
title = "systemd-networkd的坑"
description = ""
date = "2018-08-07"
tags = ["systemd"]
+++
# systemd-networkd的坑
前几天发现了systemed-networkd 这个东西。本以为自己已经使用了这么久的systemd，不会有什么坑了，可实际上这是个大坑。

*  也是最大的坑，bond。由于我们的交换机只支持802.3ad。看文档很简单，在network里面配置一下mode=802.3ad。可是通过交换机发现网口是通的，但是链路确实断的。很容易判断是因为链路协议问题。可是我已经配置好了呀，网卡也启动了。

```
[Match]
Name=bond0
[Network]
Mode=802.3ad
Address=10.88.131.153/24
Gateway=10.88.131.254
DNS=10.88.131.39
DNS=10.88.131.90
```
查看bond状态

```
cat /proc/net/bonding/bond0
Bonding Mode: load balancing (round-robin)

```
让我很奇怪，我没有陪错呀，systemd-networkd 状态也是对的。最后google 了好久，发现一个哥们说不管你怎么配置bond bond0 永远是 round-robin。看了一下linux 文档发现这是内核的一种表现。我通过改名字为bond1可以解决但是不完美呀。最后通过

```
cat /etc/modprobe.d/bonding.conf
options bonding miimon=100
options bonding lacp_rate=1
options bonding mode=4

cat /proc/net/bonding/bond0

Bonding Mode: IEEE 802.3ad Dynamic link aggregation

```
解决了。

* 剩下一个坑，更能说明了systemd 的野心。现象是发现 docker 容器没有办法访问外网。我们知道这肯定是IP forwad 问题。可是我早就是通过ISO来解决了。以后有空讲一下定制centos ISO,来解决自动化装机问题。通过观察网卡流量确定，是docker0 网卡没有收到流量。流量都被bond0截断了。然后有只能通过看文档发现，必须要显示配置。最终配置如下。

```
[Match]
Name=bond0
[Network]
IPForward=yes
Address=10.88.131.153/24
Gateway=10.88.131.254
DNS=10.88.131.39
DNS=10.88.131.90
```

希望没有坑在等着我了。


