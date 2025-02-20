---
layout:     post
title:      IP RAN HVPN
subtitle:   
date:       2019-07-29
author:     chauncey
header-img: img/197d67f25cdb8bfe125d9e37b8b91c9d..jpg
catalog: true
tags:
    - 解决方案
---

## IP RAN

[![eUGLVJ.png](https://s2.ax1x.com/2019/08/01/eUGLVJ.png)](https://imgchr.com/i/eUGLVJ)

### 一   IP的接口配置、接入环和汇聚环分别配置不同的IS-IS进程

     []cost-style wide
     []tranffic-eng level-2
     
### 二   全局使能、接口使能

     [全局视图下]mpls lsr-id 1.1.1.1
     [全局视图下]mpls
     [全局视图下]mpls te
     [全局视图下]mpls te cspf
     [全局视图下]mpls rsvp-te
     
     [接口视图下]mpls
     [接口视图下]mpls te
     [接口视图下]mpls rsvp-te

### 三   建立隧道、隧道保护（Hot-Standby）

**建立隧道**

     []interface Tunnel 0/0/13
     []ip address unnumbered interface Loopback 0
     []Tunnel-Protocol mpls te
     []Destination 3.3.3.3
     []mpls te Tunnel-id 13
     []mpls te commit
     
**Hot-Standby**
     
     []explicit main-13
     []next hop 10.1.1.2
     []explicit secondary-13
     []next hop 10.1.1.6
     []next hop 10.1.1.10
     []next hop 10.1.1.13
     [interface-Tunnel0/0/13]mpls te path explicit-path main-13
     [interface-Tunnel0/0/13]mpls te path explicit-path secondary-13
     [interface-Tunnel0/0/13]mpls te backup Hot-Standby
     [interface-Tunnel0/0/13]mpls te commit
     
**查看指令**
     
     []display mpls te Tunnel-interface Tunnel 0/0/13
     []tracert lsp te Tunnel 0/0/13
     []tracert lsp te Tunnel 0/0/13 Hot-Standby

### 四   VPN业务（HVPN）

**MP-BGP**

**反射器**

     [ipv4-family-vpnv4]peer 1.1.1.1 reflect-client
     [ipv4-family-vpnv4]peer 1.1.1.1 next-hop-local
     []Tunnel-selector HAVE permit node 10
     [ASG-3-tunnel-selector]apply tunnel-policy 1
     [ipv4-family-vpnv4]Tunnel-selector HAVE 
     
**VPN FRR**

     []ip ip-prefix main-next-hop permit 3.3.3.3 32
     []route-policy vpn-frr-1 permit node 10
     []if-match ip next-hop ip-prefix main-next-hop
     []apply backup-nexthop 4.4.4.4
     [vpn-instance-vpna]vpn frr route-policy vpn-frr-1
     
**查看指令**

     []display ip routing-table vpn-instance vpna 192.168.1.0 verbose

[![eUGD8P.png](https://s2.ax1x.com/2019/08/01/eUGD8P.png)](https://imgchr.com/i/eUGD8P)
     

### 五   VPN应用隧道策略

     []Tunnel-policy 13
     []Tunnel select-seq cr-lsp lsp load-balanced-nunmber 1
     [ip-vpn-instance]Tnl-policy 13
     
     []

###


