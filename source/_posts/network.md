---
title: network
date: 2023-08-25 14:52:21
tags: 
categories: network

---

## DNAT&SNAT

[NAT相关资料](https://zhuanlan.zhihu.com/p/26992935)

网络地址转换（`Network Address Translation，简称NAT`）是一种常见的网络技术。NAT的主要目的是将私有网络中的内部IP地址转换为公共网络可路由的IP地址，以实现与公共网络的连接。

[NAT、DANT、SNAT的区别](https://www.cnblogs.com/pam-sh/p/13445846.html)

DNAT Destination Network Address Translation **目的网络地址转换**

SNAT Source Network Address Translation **源网络地址转换**

### 区分SNAT和DNAT

从定义来讲它们一个是源地址转换，一个是目标地址转换。都是地址转换的功能，将私有地址转换为公网地址。
**要区分这两个功能可以简单的由连接发起者是谁来区分：**
内部地址要访问公网上的服务时（如web访问），内部地址会主动发起连接，由路由器或者防火墙上的网关对内部地址做个地址转换，将内部地址的私有IP转换为公网的公有IP，网关的这个地址转换称为SNAT，主要用于内部共享IP访问外部。
当内部需要提供对外服务时（如对外发布web网站），外部地址发起主动连接，由路由器或者防火墙上的网关接收这个连接，然后将连接转换到内部，此过程是由带有公网IP的网关替代内部服务来接收外部的连接，然后在内部做地址转换，此转换称为DNAT，主要用于内部服务对外发布。

## iptables

[iptables 详解](https://zhuanlan.zhihu.com/p/441089738)

