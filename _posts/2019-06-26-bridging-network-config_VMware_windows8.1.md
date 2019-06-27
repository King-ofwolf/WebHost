---
layout: post
title:  "VMware Linux 虚拟机桥接模式联网最佳配置"
date:   2019-06-26 23:52:10
categories: VMware 
tags: network ubuntu vmware
excerpt: VMware中Ubuntu虚拟机的详细桥接网络配置，共享宿主机的网络
---

* content
{:toc}


*此方式从另一方面解决了我的ftp服务器能连接但无法列出目录的问题*  
*主要情况为：腾讯云ubuntu server 16.04 下基于vsftpd的ftp服务器，无防火墙的情况下，在实体机windows环境下使用浏览器、cmd、资源管理器都能进行文件操作，但在虚拟机（ubuntu 16.04，网络为NET模式）的terminal中使用ftp指令，却只能连接(230 login successful)，不能访问(200 PORT command successful. Consider using PASV. 425 Failed to establish connection.)*
*问题详情参考https://blog.csdn.net/sun120204535/article/details/72865865 ,博文中的方法并不能解决我的问题（服务器和客户端都无防火墙）*

> 思路:在实体机上安装一个网卡供虚拟机桥接使用,将本地网络连接(网线的网卡)或WiFi连接网卡(WLAN网卡)的网络共享给供虚拟机桥接的网卡(每次换网要重新在更改网络适配器选项中配置共享).
> 
>图文详细解说参考文末链接

## 一.在实体机上配置供虚拟机桥接的网卡

1. 右键桌面上的 **此电脑** 选择 **管理**
2. 点击**设备管理器** -> 点击**电脑名字**
3. 点击**操作**,选择**添加过时硬件**
4. 点击**下一步**,选择第二个选项
5. 选择**网络适配器**,下一步
6. 选择厂商**Microsoft**,型号**Microsoft KM-TEST 环回适配器**
7. 继续下一步直至安装完成
8. 打开**更改网络适配器**,双击**环回适配器**,点击**详细信息**可以看到 IPv4地址为**192.168.137.1**。选择本地连接的网卡,或WiFi网卡(有网的网卡) 右键 选择 **属性**
9. 选择**共享**，勾选连接共享，并选择以太网四(刚才安装的网卡),点击确定,ok配置完毕

实体机网卡配置部分完毕

## 二.配置VMware网卡配置

### 对于VMware Workstation

1. 打开VMware 选择上方菜单栏 **编辑** **选项** 选择 **虚拟网络编辑器**,选择**更改设置**
2. 选择**添加网络**,将其设置为**桥接模式**,桥接到之前设置的环回适配器上,点击**确定**
3. 右键要联网的虚拟机,打开**设置**
4. 选择**网络适配器**,设置为**自定义**,选择刚才添加的网络,点击确定,配置完毕

### 对于VMware Viewer

1. 右键要联网的虚拟机,打开**设置**
2. 选择**桥接模式**，勾选复制物理网络连接状态
3. 点击**配置适配器**，只勾选**环回适配器**，点击确定，配置完毕

## 三.配置虚拟机网络设置

1. 配置虚拟机网卡:
```bash
vi /etc/sysconfig/network-scripts/ifcfg-eth0 

    DEVICE=eth0
    TYPE=Ethernet
    ONBOOT=yes
    BOOTPROTO=static
    IPADDR=192.168.137.21 # ip地址 可随意设置为192.168.137.2~192.168.137.253之间
    NETMASK=255.255.255.0
    GATEWAY=192.168.137.1
    DNS1=8.8.8.8
```
2. 配置host映射文件
```bash
vi /etc/hosts

	192.168.137.21  computername #你的虚拟机主机名
```
3. 重启虚拟机
4. 测试网络,ping

> 针对我使用的环境（物理机window8.1+无线网，虚拟机ubuntu16.04+无下述网卡配置文件），配置操作仅使用了系统设置中的网络配置，地址获取方式为自动获取（DHCP）


参考目录：
<https://blog.csdn.net/weixin_42356952/article/details/80581296>
