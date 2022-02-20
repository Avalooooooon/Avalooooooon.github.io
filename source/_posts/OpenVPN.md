---
{}
updated: 2022-02-20 14:10:04
date: 2022-02-20 14:10:04
title:
tags:
categories:
---

1. 简介

   OpenVPN 是基于 TLS 加密层实现 VPN 的开源项目。

   ## 安装

   ### Linux 服务端(debian10)

   登录服务端：除了ssh方式，也可以通过用户名密码方式。参考了[这篇文章](https://blog.csdn.net/liuxiao723846/article/details/121618772)。端口号可以在服务器后台查阅ssh server。

   ```bash
   ssh -p port user@IP
   ```


   安装软件包（包含客户端与服务端）：

   ```sh
   sudo apt-get update && sudo apt-get install openvpn
   openvpn --version  # 验证
   ```

   生成自签 CA、服务器证书及额外所需密钥：

   ```sh
   cd /etc/openvpn
   
   sudo cat <<EOF | sudo tee openvpn.cnf
   [ v3_ca ]
   basicConstraints       = critical, CA:TRUE
   subjectKeyIdentifier   = hash
   authorityKeyIdentifier = keyid:always, issuer:always
   keyUsage               = critical, cRLSign, digitalSignature, keyCertSign
   
   [ v3_ica ]
   basicConstraints       = critical, CA:TRUE, pathlen:1
   subjectKeyIdentifier   = hash
   authorityKeyIdentifier = keyid:always, issuer:always
   keyUsage               = critical, cRLSign, digitalSignature, keyCertSign
   
   [ v3_server ]
   basicConstraints       = critical, CA:FALSE
   subjectKeyIdentifier   = hash
   authorityKeyIdentifier = keyid:always, issuer:always
   keyUsage               = critical, nonRepudiation, digitalSignature, keyEncipherment, keyAgreement 
   extendedKeyUsage       = critical, serverAuth
   
   [ v3_client ]
   basicConstraints       = critical, CA:FALSE
   subjectKeyIdentifier   = hash
   authorityKeyIdentifier = keyid:always, issuer:always
   keyUsage               = critical, nonRepudiation, digitalSignature, keyEncipherment
   extendedKeyUsage       = critical, clientAuth
   EOF
   
   sudo openssl genrsa -out ca.key 4096
   sudo openssl rand -writerand ~/.rnd
   sudo openssl req -new -key ca.key -subj "/CN=US/ST=NewYork/L=New York City/O=DigitalOcean/OU=Community/CN=ca/emailAddress=admin@example.com" -out ca.csr
   sudo openssl x509 -req -days 36500 -signkey ca.key -extfile openvpn.cnf -extensions v3_ca -in ca.csr -out ca.crt
   sudo rm ca.csr
   
   sudo openssl genrsa -out server.key 4096
   sudo openssl rand -writerand ~/.rnd
   sudo openssl req -new -key server.key -subj "/C=US/ST=NewYork/L=New York City/O=DigitalOcean/OU=Community/CN=server/emailAddress=admin@example.com" -out server.csr
   sudo openssl x509 -req -days 36500 -CA ca.crt -CAkey ca.key -CAcreateserial -extfile openvpn.cnf -extensions v3_server -in server.csr -out server.crt
   sudo rm server.csr
   
   sudo openssl dhparam -out dh.key 2048  # 此步将耗时数分钟
   
   sudo openvpn --genkey --secret ta.key  # 用于 OpenVPN TLS-auth 功能的密钥
   ```

   从例子拷贝配置文件：

   ```sh
   cd /etc/openvpn
   sudo cp /usr/share/doc/openvpn/examples/sample-config-files/server.conf.gz .
   sudo gzip -d server.conf.gz
   ```

   > 由于版本不同，server.conf的路径也可能不同。
   > 如果上述路径找不到server.conf文件，可以尝试路径/etc/openvpn/server/server.conf。
   > 比如openvpn版本为2.4.11时，可以将命令修改如下：
   > ```cp -p /usr/share/doc/openvpn-2.4.11/sample/sample-config-files/server.conf /etc/openvpn/```
   > ```cat /etc/openvpn/server.conf```

   修改配置文件：
   ```bash
   vim server.conf
   ```
   1. 修改 port(默认为1194，可以出现问题再修改，但要和客户端保持一致）。
   2. 修改协议为 UDP。
   3. 检查 ca：`ca.crt`、cert：`server.crt`、key：`server.key`、dh：`dh.key`、tls-auth：`ta.key`相对于配置文件夹的相对路径（或绝对路径）。注意检查每种证书只在一行出现，否则后面的行会覆盖掉前面行的值(tls-auth：`ta.key`的位置较为靠后）。
   4. 注释掉`explicit-exit-notify 1`（该功能仅用于 UDP 模式）。

   启动服务：

   ```sh
   sudo systemctl start openvpn@server
   ```

   开启系统转发功能：

   ```sh
   cat <<EOF | sudo tee /etc/sysctl.d/99-openvpn.conf
   net.ipv4.ip_forward = 1
   EOF
   sudo sysctl --system
   ```

   配置 iptables 转发：

   ```sh
   sudo iptables -t nat -A POSTROUTING -s 10.8.0.0/24 -o eth0 -j MASQUERADE -m comment --comment "openvpn"
   ```

   查看服务器日志：
   ```bash
   systemctl status openvpn@server
   ```

   ### Linux 客户端

   安装软件包同服务端。

   生成密钥文件`client.key`及证书请求文件`client.csr`：

   ```sh
   cd /etc/openvpn
   sudo openssl genrsa -out client.key 4096
   sudo openssl rand -writerand ~/.rnd
   sudo openssl req -new -key client.key -subj "/C=US/ST=NewYork/L=New York City/O=DigitalOcean/OU=Community/CN=client/emailAddress=admin@example.com" -out client.csr
   ```

   OpenVPN 服务器会将 CN 名作为多个客户端时的区分标志，客户端应各自使用不同的 CN 名。

   将`client.csr`拷贝至 CA 端：
   ```sh
   cd /etc/openvpn
   rm client.csr 
   vim client.csr 
   ```

   由 CA 端签发证书`client.crt`：

   ```sh
   sudo openssl x509 -req -days 36500 -CA ca.crt -CAkey ca.key -CAcreateserial -extfile openvpn.cnf -extensions v3_client -in client.csr -out client.crt
   ```

   将`client.crt`及`ca.crt`、`ta.key`拷贝至客户端，直接复制文本内容即可：
   ```sh
   cat client.crt
   cat ca.crt
   cat ta.key
   ```

   CA 端可清除文件：

   ```sh
   sudo rm client.csr client.crt
   ```

   client 端可清除文件：

   ```sh
   sudo rm client.csr
   ```

   复制配置文件：

   ```sh
   cd /etc/openvpn
   sudo cp /usr/share/doc/openvpn/examples/sample-config-files/client.conf .
   ```

   修改配置文件：

   1. 修改 remote 为服务器地址及 port。
   2. 修改协议为 UDP！。
   3. 检查 ca：`ca.crt`、cert：`client.crt`、key：`client.key`、tls-auth：`ta.key`相对于配置文件夹的相对路径。
   4. 末尾添加：`auth-nocache`以避免在内存中缓存认证信息（否则会报 warning）。
   5. 末尾添加：`redirect-gateway def1 bypass-dhcp`，将系统默认路由网关改为 OpenVPN 虚拟对端网卡。
   6. 末尾添加：`dhcp-option DNS 8.8.8.8`，设置连接 OpenVPN 后使用的 DNS 服务器。

   启动服务：

   ```sh
   sudo systemctl start openvpn@client
   ```

   ping VPN 网关进行测试：

   ```sh
   ping 10.8.0.1
   ```

   ### Windows 客户端

   去官网->Community->Downloads 下载 OpenVPN GUI 安装器并安装。

   配置方法与 Linux 几乎一致。只是配置文件夹为`%USERPROFILE%/OpenVPN/config`，客户端配置模版文件为`C:/Program Files/OpenVPN/sample-config/client.ovpn`。

   生成密钥文件`client.key`及证书请求文件`client.csr`：

   ```sh
   cd C:\Users\28422\OpenVPN\config
   openssl genrsa -out client.key 4096
   openssl rand -writerand .rnd # 配置文件夹就地新建.rnd随机数种子
   openssl req -new -key client.key -subj "/C=US/ST=NewYork/L=New York City/O=DigitalOcean/OU=Community/CN=client2/emailAddress=admin@example.com" -out client.csr # gitbash会报错，需要在powershell中运行
   ```

   OpenVPN 服务器会将 CN 名作为多个客户端时的区分标志，客户端应各自使用不同的 CN 名。

   将`client.csr`拷贝至 CA 端：
   ```sh
   cd /etc/openvpn
   rm client.csr 
   vim client.csr 
   ```

   由 CA 端签发证书`client.crt`：

   ```sh
   sudo openssl x509 -req -days 36500 -CA ca.crt -CAkey ca.key -CAcreateserial -extfile openvpn.cnf -extensions v3_client -in client.csr -out client.crt
   ```

   将`client.crt`及`ca.crt`、`ta.key`拷贝至客户端，直接复制文本内容即可：
   ```sh
   cat client.crt
   cat ca.crt
   cat ta.key
   ```

   CA 端可清除文件：

   ```sh
   sudo rm client.csr client.crt
   ```

   client 端可清除文件：

   ```sh
   rm client.csr
   ```

   修改配置文件C:/Program Files/OpenVPN/sample-config/client.ovpn：

   1. 修改 remote 为服务器地址及 port。
   2. 修改协议为UDP。
   3. 检查 ca：`ca.crt`、cert：`client.crt`、key：`client.key`、tls-auth：`ta.key`相对于配置文件夹的相对路径。
   4. 末尾添加：`auth-nocache`以避免在内存中缓存认证信息（否则会报 warning）。
   5. 末尾添加：`redirect-gateway def1 bypass-dhcp`，将系统默认路由网关改为 OpenVPN 虚拟对端网卡。
   6. 末尾添加：`dhcp-option DNS 8.8.8.8`，设置连接 OpenVPN 后使用的 DNS 服务器。

   在可视化界面中启动服务即可。

   ### Mac 客户端

   安装软件OpenVPN Connect。

   生成密钥文件`client.key`及证书请求文件`client.csr`：

   ```sh
   cd /Applications
   cd "OpenVPN Connect"
   sudo openssl genrsa -out client.key 4096
   sudo openssl rand -out .rnd 1
   sudo openssl req -new -key client.key -subj "/C=US/ST=NewYork/L=New York City/O=DigitalOcean/OU=Community/CN=client/emailAddress=admin@example.com" -out client.csr
   ```

   OpenVPN 服务器会将 CN 名作为多个客户端时的区分标志，客户端应各自使用不同的 CN 名。

   将`client.csr`拷贝至 CA 端：
   ```sh
   cd /etc/openvpn
   rm client.csr 
   vim client.csr 
   ```

   由 CA 端签发证书`client.crt`：

   ```sh
   sudo openssl x509 -req -days 36500 -CA ca.crt -CAkey ca.key -CAcreateserial -extfile openvpn.cnf -extensions v3_client -in client.csr -out client.crt
   ```

   将`client.crt`及`ca.crt`、`ta.key`拷贝至客户端，直接复制文本内容即可：
   ```sh
   cat client.crt
   cat ca.crt
   cat ta.key
   ```

   CA 端可清除文件：

   ```sh
   sudo rm client.csr client.crt
   ```

   client 端可清除文件：

   ```sh
   sudo rm client.csr
   ```

   修改配置文件client.ovpn：

   1. 修改 remote 为服务器地址及 port。
   2. 修改协议为 UDP。
   3. 检查 ca：`ca.crt`、cert：`client.crt`、key：`client.key`、tls-auth：`ta.key`相对于配置文件夹的相对路径。
   4. 末尾添加：`auth-nocache`以避免在内存中缓存认证信息（否则会报 warning）。
   5. 末尾添加：`redirect-gateway def1 bypass-dhcp`，将系统默认路由网关改为 OpenVPN 虚拟对端网卡。
   6. 末尾添加：`dhcp-option DNS 8.8.8.8`，设置连接 OpenVPN 后使用的 DNS 服务器。

   在OpenVPN Connect启动服务。

   ## 使用

   ### 系统服务开机自启

   开机自启脚本`/etc/init.d/openvpn`会扫描`/etc/openvpn/xxx.conf`并为每个配置文件启动一个同名的 openvpn@xxx 系统服务。可编辑`/etc/default/openvpn`文件的 AUTOSTART 项，选择性地开启配置自启。

   ### 客户端 IP 分配

   服务端配置文件的 ifconfig-pool-persist 项设置了客户端 IP 持久化记录文件，默认为`/var/log/openvpn/ipp.txt`，但此文件仅用于 OpenVPN 自己回写动态分配给各客户端的 IP，并不应通过该文件配置。正确方法是为服务端配置文件添加 client-config-dir ccd，然后新建`/etc/openvpn/ccd`客户端静态 IP 配置文件夹，在其中新建以客户端 CN 命名的配置文件。

   ```sh
   cd /etc/openvpn
   sudo mkdir ccd
   sudo cat <<EOF | sudo tee ccd/client
   ifconfig-push 10.8.0.5 10.8.0.6
   EOF
   ```

   其中 ifconfig-push 第一个参数为分配给客户端的 IP，第二个参数为隧道对端 IP。为了与 Windows 客户端的 TAP-Windows 驱动兼容，两 IP 应位于统一`/30`子网，且各客户端应分属不同`/30`子网，例如此处 IP 为`10.8.0.5 10.8.0.6`，下个客户端 IP 就至少为`10.8.0.9 10.8.0.10`。

   ### 客户端互访

   注释掉服务端配置文件的 client-to-client，即可实现连接到同一 OpenVPN 服务端的客户端通过 VPN IP 互访。

   ## 参考资料

   1. [OpenVPN 官网](https://openvpn.net/)
   2. [OpenSSL 添加 x509 扩展](https://www.golinuxcloud.com/add-x509-extensions-to-certificate-openssl/)
   3. [OpenVPN KeyUsage 扩展](https://superuser.com/questions/738612/openssl-ca-keyusage-extension/)

3. https://superuser.com/questions/738612/openssl-ca-keyusage-extension/)
