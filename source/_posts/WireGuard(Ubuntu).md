---
title: WireGuard配置Ubuntu教程
comments: true
donate: true
tags: WireGuard
categories: 网络技术
description: WireGuard(Ubuntu版本)组网教程.
sticky: 10000
abbrlink: 1297332112
date: 2024-04-24 00:00:00
from:
url:
author:
about:
avatar:
---
 **wireguard是什么？**[维基百科](https://en.wikipedia.org/wiki/WireGuard)是这样描述的：

**WireGuard**是一种实现[加密虚拟专用网络(VPN) 的通信协议](https://en.wikipedia.org/wiki/Virtual_private_network)和免费开源软件，其设计目标是：易于使用、高速性能、低攻击面
    
 它旨在比[IPsec](https://en.wikipedia.org/wiki/IPsec)和[OpenVPN](https://en.wikipedia.org/wiki/OpenVPN)这两种常见的隧道协议具有更好的性能和更强大的功能；
 **WireGuard** 协议通过[UDP](https://en.wikipedia.org/wiki/User_Datagram_Protocol)传递流量。

通过描述，我们知道了wireguard其实就是**一个FAST（速度快）、MODERN（流行）、SECURE （安全）的VPN TUNNEL（VPN隧道）**。

## Ubuntu版本配置


### **安装Wireguard（以ubuntu20.04为基础）**
```        
    #root权限
    sudo -i

    #安装wireguard软件
    apt install wireguard resolvconf -y

    #开启IP转发
    echo "net.ipv4.ip_forward = 1" >> /etc/sysctl.conf
    sysctl -p
```        
### **进入配置存储路径，调整目录权限**
```
    cd /etc/wireguard/
    chmod 0777 /etc/wireguard
    
    #调整目录默认权限
    umask 077
```
### **生成服务器秘钥**
```
    #生成私钥
    wg genkey > server.key
    
    #通过私钥生成公钥
    wg pubkey < server.key > server.key.pub
```
### **生成客户端(client1)秘钥**
```
    #生成私钥
    wg genkey > client1.key
    
    #通过私钥生成公钥
    wg pubkey < client1.key > client1.key.pub
```
### **显示所有生成的秘钥**
```
    cat server.key && cat server.key.pub && cat client1.key && cat client1.key.pub
```    
### **自动创建服务器配置文件**
```
    echo "
    [Interface]
    PrivateKey = $(cat server.key) # 填写本机的privatekey 内容
    Address = 10.0.8.1 #本机虚拟局域网IP
    
    PostUp   = iptables -A FORWARD -i wg0 -j ACCEPT; iptables -A FORWARD -o wg0 -j ACCEPT; iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE
    PostDown = iptables -D FORWARD -i wg0 -j ACCEPT; iptables -D FORWARD -o wg0 -j ACCEPT; iptables -t nat -D POSTROUTING -o eth0 -j MASQUERADE
    #注意eth0需要为本机网卡名称
    
    ListenPort = 50814 # 监听端口
    DNS = 8.8.8.8
    MTU = 1420
    [Peer]
    PublicKey =  $(cat client1.key.pub)  #自动client1的公钥
    AllowedIPs = 10.0.8.10/32 #客户端所使用的IP" > wg0.conf
```    
### **设置服务器开机自启动**
```
    systemctl enable wg-quick@wg0
```   
### **启动wireguard**
```
    #启动wg0
    wg-quick up wg0
    #关闭wg0
    wg-quick down wg0
```   
### **手动进入创建服务器配置文件**
```    
    nano /etc/wireguard/wg0.conf
```    
### **wireguard客户端下载地址**
    
    https://www.wireguard.com/install/
    
### **客户端配置（以client1为例）**
```    
    [Interface]
    PrivateKey = 6M8HEZioew+vR3i53sPc64Vg40YsuMzh4vI1Lkc88Xo= #此处为client1的私钥
    Address = 10.0.8.10 #此处为peer规定的客户端IP
    MTU = 1500
    
    [Peer]
    PublicKey = Tt5WEa0Vycf4F+TTjR2TAHDfa2onhh+tY8YOIT3cKjI= #此处为server的公钥
    AllowedIPs = 10.0.8.0/24 #此处为允许的服务器IP
    Endpoint = 114.132.56.178:50814 #服务器对端IP+端口
```    
### **增加服务器客户端节点client2**
```
    #生成私钥
    wg genkey > client2.key
    
    #通过私钥生成公钥
    wg pubkey < client2.key > client2.key.pub
    
    #将peer公钥加入wg0.conf配置
    echo "
    [Peer]
    PublicKey =  $(cat client2.key.pub)  #自动client1的公钥
    AllowedIPs = 10.0.8.11/32 #客户端Client2所使用的IP" >> wg0.conf
```