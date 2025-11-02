# Lab 2 網絡基本連接調試 connecting and Debugging Network Devices

## Purpose

After completing the Lab, you will be able to:

- Master the method of connecting a router
- Master the method of testing system connectivity using Ping and Tracert commands.
- Master the method of using debug commands

## Lab Diagram

Figure 2-1 Lab diagram
![](https://github.com/eddylin2015/H3C-CM446-10-2025-C/blob/main/img/figure201labdiagram.png?raw=true)

## Lab Procedure

ITEM                |  VERSION   |   QUANTITY
--------------------|------------|-------------
MSR36-20             | CMW7.1           |   2
S5820V2              | CMW7.1           |2
PC                   |            |2
Cat5UTPEthernetCable |            |4

## Lab Procedure
## 实验任务一:搭建基本连接环境
本实验任务供学员熟悉并掌握路由器、交换机、PC的基本网络连接配置。
### 步骤一:完成 PC、交换机、路由器互连 edu. mo
在教师指导下,完成两台路由器通过以太口GO/O 背靠背相连;路由器以太口 GO/1 分别下
接一台交换机(S5820V2);PC 通过网线连接到交换机端口上。
- routers G0/0
- router to switch G0/1
### 步骤二:配置 IP 地址
```cmd
# The RTA configuration is as follows:
[H3C] sysname RTA
[RTA]interface GigabitEthernet 0/1
[RTA-GigabitEthernet0/1]ip add 192.168.0.1 24
[RTA]interface GigabitEthernet 0/0
[RTA-Serial1/0]ip address 192.168.1.1 30

#The RTB configuration is as follows:
[H3C]sysname RTB
[RTB]interface GigabitEthernet 0/1
[RTB-GigabitEthernet0/1]ip add 192.168.2.1 24
[RTA]interface GigabitEthernet 0/0
[RTA-Seriall/0]ip address 192.168.1.2 30
```

The network IP address of the PCA is set as 192.168.0.10/24, and the gateway is 192.168.0.1. 

Connect the PCA to the router port G0/1 via the L2 switch.

## 实验任务二:使用 ping 命令检查连通性

- 1. Ping the RTA ports G0/1 and G0/0 on the PCA. The ports can be pinged.
- 2. Ping the RTB port G0/0 on the PAC. The port cannot be pinged.
- 3.  Ping PCB on PCA. PCB cannot be pinged.

```cmd
[RTA]disp ip routing-table
Destinations : 17 Routes 17
Destination/Mask Proto Pre Cost NextHop Interface
0.0.0.0/32 Direct 0 0 127.0.0.1 InLoop0
127.0.0.0/8 Direct 0 0 127.0.0.1 InLoop0
127.0.0.0/32 Direct 0 0 127.0.0.10 InLoop0
127.0.0.1/32 Direct 00 127.0.0.1 InLoop0
127.255.255.255/32 Direct 0 0 127.0.0.1 InLoopo
192.168.0.0/24 Direct 0 0 192.168.0.1 GE0/1
192.168.0.0/32 Direct 0 0 192.168.0.1 GE0/1
192.168.0.1/32 Direct 0 0 127.0.0.1 InLoop0
192.168.0.255/32 Direct 0 0 192.168.0.1 GEO/1
192.168.1.0/30 Direct 0 0 192.168.1.1 GO/0
192.168.1.0/32 Direct 0 0 192.168.1.1 GO/0
192.168.1.1/32 Direct 0 0 127.0.0.1 InLoop0
192.168.1.2/32 Direct 0 0 192.168.1.2 GO/0
192.168.1.3/32 Direct 0 0 192.168.1.1 GO/0
224.0.0.0/4 Direct 0 0 0.0.0.0 NULLO
224.0.0.0/24 Direct 0 0 0.0.0.0 NULLO
255.255.255.255/32 Direct 0 0 127.0.0.1 InLoop0
```

### 步骤五:配置静态路由
```cmd
#RTA 上配置 Destination 项中,没有看到192.168.2.0表
[RTA]ip route-static 192.168.2.0 255.255.255.0 192.168.1.2

#RTB 上配置 RTB 也没有到达192.168.0.0/24 网段的路由。
[RTB]ip route-static 192.168.0.0 255.255.255.0 192.168.1.1
```
### 步骤六:PCA ping PCB
### 步骤七:以 RTA 接口 G0/1 地址为源,ping PCB

## 实验任务三:使用 tracert 命令检查连通性
### 步骤一:PCA tracert PCB
### 步骤二:在RTA 上 tracert PCB
## tracert enable cmd
```cmd
[RTA]ip unreachables enable
[RTA]ip ttl-expires enable
```
## 实验任务四:使用 debugging 命令察看调试信息
### 步骤一:开启 RTB 终端对信息的监视和显示功能
### 步骤二:打开 RTB 上ICMP 的调试开关
### 步骤三:在RTA 上 ping RTB,观察 RTB 调试信息输出

```cmd
<RTB>terminal monitor
<RTB>terminal debugging
<RTB>debugging ip icmp
<RTB>undo debugging all
```

## Commands Used in the Lab

Command | Description
--------|--------------
ip add|configure an ip address配置IP地址
iproute-static|configure a static route配置静态路由
ping|check connectivity检测连通性
tracert|check a route探测转发路径
terminal monitor|enable the system monitoring function开启终端对系统信息的监视功能
terminal debugging|Enable the debugging information display function开启终端对调试信息的显示功能
debugging|Enable the debugging switch of a specified module.打开系统指定模块调试开关

## 總結
完成LAB2.
