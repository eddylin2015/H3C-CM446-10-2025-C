# Lab07_ARP 

## Lab Diagram

![](https://github.com/eddylin2015/H3C-CM446-10-2025-C/blob/main/img/hcl_fe8510543865.png?raw=true)

## 7.4 实验过程
## Task1实验任务一:ARP 表项观察
### 步骤二:配置 PC 及路由器的IP 地址
表7-2 IP地址列表
|设备名称| 接口 |IP地址|
|-------|-------|-------|
|PCA ||172.16.0.1/24|
|PCB ||172.16.1.1/24|
|RTA |GO/0| 172.16.0.254/24|
|RTA |G0/1| 172.16.1.254/24|

```cmd
 sysname PCA
#
interface GigabitEthernet0/0/1
 ip address 172.16.0.1 255.255.0.0
#
```
```cmd
 sysname PCB
interface GigabitEthernet0/0/1
 ip address 172.16.1.1 255.255.0.0
#
```
然后在RTA 的接口上配置 IP地址及掩码,如下:
```cmd
sysname RTA
[RTA]interface GigabitEthernet0/0
[RTA-GigabitEthernet0/0]ip address 172.16.0.254 24
[RTA]interface GigabitEthernet0/1
[RTA-GigabitEthernet0/1] ip address 172.16.1.254 24
#
interface GigabitEthernet0/0
 port link-mode route
 combo enable copper
 ip address 172.16.0.254 255.255.255.0
 proxy-arp enable
#
interface GigabitEthernet0/1
 port link-mode route
 combo enable copper
 ip address 172.16.1.254 255.255.255.0
 proxy-arp enable
#
```
### 步骤三:查看 ARP 信息
首先,我们在RTA 及PCA、PCB上用命令来查看它们的IP地址和 MAC 地址。
RTA 的接口 MAC 地址与IP地址如下:

```cmd
[RTA] display interface GigabitEthernet 0/0
GigabitEthernet0/0
Current state: UP
Line protocol state: UP
Description: GigabitEthernet0/0
Bandwidth: 100000kbps
Maximum Transmit Unit: 1500
Interface
Internet Address is 172.16.0.254/24 Primary
IP Packet Frame Type: PKTFMT ETHNT 2, Hardware Address: 70ba-ef80-0958

[RTA] display interface GigabitEthernet 0/1
GigabitEthernet0/1
Current state: UP
Line protocol state: UP
Description: GigabitEthernet0/1 Interface
Bandwidth: 1000000kbps
Maximum Transmit Unit: 1500
Internet Address is 172.16.1.254/24 Primary
IP Packet Frame Type: PKTFMT ETHNT 2, Hardwa Address: 70ba-ef80-0959

```
|设备名称| 接口 |IP地址 |MAC 地址|
|------|------|------|------|
|PCA ||172.16.0.1/24| A4-5D-36-59-26-4F|
|PCB ||172.16.1.1/24| 44-37-E6-AB-7D-FO|
|RTA ||GO/O| 172.16.0.254/24| 70ba-ef80-0958|
|RTA |G0/1 |172.16.1.254/24| 70ba-ef80-0959|

```cmd
c:\>arp -а
```

RTA 的正确信息应如下所示:
```cmd
[RTA]display arp all

Type: S-Static D-Dynamic 0-Openflow M-Multiport I-Invalid
IP address MAC address VLAN Interface Aging Type
172.16.0.1 a45d-3659-264f N/А GE0/0 18 D
172.16.1.1 4437-e6ab-7df0 N/A GE0/1 19 D
```

## 实验任务二:ARP 代理配置
## 步骤二:配置 PC 及路由器的IP地址

表7-4 IP地址列表
|设备名称| 接口 |IP地址|
|-------|-------|-------|
|PCA ||172.16.0.1/16|
|PCB ||172.16.1.1/16|
|RTA |GO/0| 172.16.0.254/24|
|RTA |G0/1| 172.16.1.254/24|

然后在RTA 的接口上配置IP地址及掩码,如下:
```cmd
[RTA]interface GigabitEthernet0/0
[RTA-GigabitEthernet0/0]ip address 172.16.0.254 24
[RTA]interface GigabitEthernet0/1
[RTA-GigabitEthernet0/1]ip address 172.16.1.254 2
```
### 步骤三:ARP 代理配置

在RTA 上配置 ARP 代理:
```cmd
[RTA]interface GigabitEthernet0/0
[RTA-GigabitEthernet0/0] proxy-arp enable
[RTA]interface GigabitEthernet0/1
[RTA-GigabitEthernet0/1] proxy-arp enable
```
### 步骤四:查看 ARP 信息
## cmd list
- proxy-arp enable 开启端口的ARP代理特性
- disp arp all 显示ARP表项
## 總結
完成




