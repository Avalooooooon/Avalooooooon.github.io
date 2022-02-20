---
title: 搭建L2TP-IPsec
tags: [vpn]
updated: 2022-02-20 15:20:43
date: 2022-02-20 15:20:43
categories:
---


## 1. 前言

虚拟专用网络（VPN，Virtual Private Network）是一种网络底层协议，主机开启 VPN 后，所有网络请求都会先发送到 VPN 服务器，然后 VPN 服务器作为代理帮我们访问敏感资源。本文介绍如何在 Linux（Ubuntu 18.04）服务器上搭建 L2TP/IPsec VPN。

## 2. IPSec

互联网安全协议（IPsec，Internet Protocol Security）是 OSI 第三层的安全协议，工作分传输模式和隧道模式，传输模式为自己传输信息，隧道模式为给上层协议提供安全隧道。此处使用隧道模式给上层 VPN 协议提供安全隧道。软件使用开源的 libreswan。

安装：

```sh
sudo apt-get install libreswan
```

验证安装成功：

```sh
ipsec --version
```

新建软件配置文件：

```sh
sudo vim /etc/ipsec.d/l2tp-ipsec.conf
```

写入：

```text
conn L2TP-PSK-NAT
	rightsubnet=vhost:%priv
	also=L2TP-PSK-noNAT

conn L2TP-PSK-noNAT
	authby=secret
	pfs=no
	auto=add
	keyingtries=3
	rekey=no
	ikelifetime=8h
	keylife=1h
	type=transport
	left=<服务器公网 IP>
	leftprotoport=17/1701
	right=%any
	rightprotoport=17/%any
```

新建预共享密钥文件：

```sh
sudo vim /etc/ipsec.d/l2tp-ipsec.secrets
```

写入：

```text
<服务器公网 IP> %any : PSK "<IPSec 密钥>"
```

编辑系统配置文件：

```sh
sudo vim /etc/sysctl.d/99-ipsec.conf
```

末尾添加：

```text
net.ipv4.ip_forward = 1
net.ipv4.conf.default.rp_filter = 0
net.ipv4.conf.all.send_redirects = 0
net.ipv4.conf.default.send_redirects = 0
net.ipv4.conf.all.log_martians = 0
net.ipv4.conf.default.log_martians = 0
net.ipv4.conf.default.accept_source_route = 0
net.ipv4.conf.all.accept_redirects = 0
net.ipv4.conf.default.accept_redirects = 0
net.ipv4.icmp_ignore_bogus_error_responses = 1
```

更新系统配置：

```sh
sudo sysctl -p
```

试运行：

```sh
sudo systemctl start ipsec
ipsec verify
```

可添加至开机启动：

```sh
sudo systemctl enable ipsec
```

## 3. PPP

L2TP 协议基于 PPP 协议，因此需先确保 PPP 协议正常运行。

安装：

```sh
sudo apt-get install ppp
```

新建配置文件：

```sh
sudo vim /etc/ppp/options.xl2tpd
```

写入：

```text
require-mschap-v2
ms-dns 8.8.8.8
ms-dns 8.8.4.4
asyncmap 0
auth
crtscts
lock
hide-password
modem
debug
name l2tpd
proxyarp
lcp-echo-interval 30
lcp-echo-failure 4
```

编辑 PPP 用户名及密码文件：

```sh
sudo vim /etc/ppp/chap-secrets
```

末尾添加：

```text
<用户名> * <密码> *
```

## 4. L2TP

第二层隧道协议（L2TP，Layer 2 Tunneling Protocol）顾名思义是一个OSI 第二层协议。软件使用开源的 xl2tpd。

安装：

```sh
sudo apt-get install xl2tpd
```

编辑配置文件：

```sh
sudo vim /etc/xl2tpd/xl2tpd.conf
```

末尾添加：

```text
[global]
ipsec saref = yes
[lns default]
ip range = 10.1.2.2-10.1.2.254
local ip = 10.1.2.1
refuse chap = yes
refuse pap = yes
require authentication = yes
ppp debug = yes
pppoptfile = /etc/ppp/options.xl2tpd
length bit = yes
```

试运行：

```sh
sudo systemctl start xl2tpd
```

然后在用户机上尝试连接 VPN，协议选择 L2TP，密钥、用户名和密码分别为刚才设置的 IPSec 密钥、PPP 用户名和 PPP 密码。应该能够连接成功，但还不能访问互联网。

可添加至开机启动：

```sh
sudo systemctl enable xl2tpd
```

查看l2tp日志：
```bash
systemctl status xl2tpd
```

## 5. 系统防火墙

使用系统自带的 iptables 软件管理系统防火墙。

向 iptables 缓存添加策略，在其 NAT 表的 POSTROUTING 链中设置 IP 伪装：

```sh
sudo iptables -t nat -A POSTROUTING -s 10.1.2.0/24 -o eth0 -j MASQUERADE -m comment --comment "xl2tp"
```

将当前策略保存至配置文件，然后应用策略：

```sh
sudo iptables-save > /etc/network/iptables
sudo iptables-apply -w /etc/network/iptables
```
ptables-persistant netfilter-persistant
然后netfilter-persistant save就能持久化

centos7:/etc/sysconfig/iptables

重启 xl2tpd 服务：

```sh
sudo systemctl restart xl2tpd
```

此时用户机应该就能通过 VPN 访问互联网了。

可修改网络配置文件，使连接网络时自动加载 iptables 配置：

```sh
sudo vim /etc/network/interfaces
```

末尾添加：

```text
pre-up iptables-restore < /etc/network/iptables
```

## 6. 参考资料

1. [centos7 搭建 xl2tpd 服务](https://blog.csdn.net/weixin_41004350/article/details/83594393)

2. [Ubuntu iptables 配置](https://blog.51cto.com/yangzhiming/1982814)
