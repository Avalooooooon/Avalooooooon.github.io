---
title: Linux折腾
updated: 2022-02-20 09:58:44
date: 2022-02-20 09:58:44
tags: [Linux]
categories: [操作系统,Linux]


---

操作系统：debian10

## 解决sudo: command not found
先看 /etc/sudoers.d 文件是否存在
```bash
find /etc/sudoers.d
```
如果返回 No such file or directory就说明系统没有安装sudo，更新apt-get并执行安装命令 ：
```bash
apt-get update
apt-get install sudo
```

## 解决无法下载，Internal Error, ordering was unable to handle the media swap
```bash
sudo rm -fR /var/lib/apt/lists/*
sudo apt-get update
```

## 安装包管理器yum
```bash
sudo apt-get update
sudo apt-get install build-essential
sudo apt-get install yum
```

## 开启ssh服务并用电脑访问
### 服务器开启ssh
1.更新软件源并安装ssh服务：
```bash
sudo apt-get update
sudo apt-get install ssh
```
2.修改配置文件sshd_config：
```bash
vim /etc/ssh/sshd_config
```
将#PermitRootLogin prohibit-password的注释去掉并将prohibit-password改为yes
将#PasswordAuthentication no的注释去掉并将no修改为yes
```bash
PermitRootLogin yes
PasswordAuthentication yes
```
3.启动SSH服务并验证SSH服务状态:
```bash
/etc/init.d/ssh start # 或者service ssh start
/etc/init.d/ssh status
```
4.添加开机自启动:
```bash
update-rc.d ssh enable
```

### 通过ssh连接服务器
命令：
```bash
ssh -p port user@IP
 
# 例子
ssh -p 28060 root@xx.xx.xx.xx
```
如果/Users/tusername/.ssh/known_hosts里有相同ip的信息，删除，否则可能会出现报错
```bash
WARNING: REMOTE HOST IDENTIFICATION HAS CHANGED! 
```















