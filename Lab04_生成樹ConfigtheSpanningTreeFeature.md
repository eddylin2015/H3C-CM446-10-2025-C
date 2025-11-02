# lab4 生成樹Config the Spanning Tree Feature

## Purpose

- Understand the basic principle of STP.
- Master the basic method of config STP.

## Lab Diagram

![](https://github.com/eddylin2015/H3C-CM446-10-2025-C/blob/main/img/hcl_aefc21344210.png?raw=true)

## Lab Process
### 步骤二:配置 STP
本实验任务是配置 STP 根桥及边缘端口。在系统视图下启用 STP,并设置 SWA 的优先级为0,以使 SWA 为根桥;并且配置连接 PC 的端口为边缘端口。

```cmd
配置 SWA:
[SWA]stp global enable
[SWA]stp priority 0
[SWA]interface GigabitEthernet 1/0/1
[SWA-GigabitEthernet1/0/1]stp edged-port

配置 SWB:
[SWB]stp global enable
[SWB]stp priority 40962
[SWB]interface GigabitEthernet 1/0/1
[SWB-GigabitEthernet1/0/1] stp edged-port
```
### 步骤三:查看 STP 信息
```cmd
[SWA]display stp

[SWA]display stp brief
0 GigabitEthernet1/0/1 DESI FORWARDING NO
0 GigabitEthernet1/0/23 DESI FORWARDING NO
0 GigabitEthernet1/0/24 DESI FORWARDING NO
```
以上信息表明,SWB 是非根桥,端口 G1/0/23 是根端口,处于转发状态,负责在交换机之间转发数据;端口G1/0/24 是备份根端口,处于阻塞状态:连接 PC的端口 G1/0/1 是指定端口, 处于转发状态。
### 步骤四:STP 冗余特性验证

STP 不但能够阻断冗余链路,并且能够在活动链路断开时,通过激活被阻断的冗余链路而恢复网络的连通。

- 设备名称 IP地址 网关
- PCA 172.16.0.1/24
- PCB 172.16.0.2/24

PCA向PCB 不间断发送ICMP 报文

```cmd
C:\Documents and Settings\Administrator>ping 172.16.0.2 -t
Pinging 172.16.0.2 with 32 bytes of data:
Reply from 172.16.0.2: bytes=32 time<1ms TTL=128
Reply from 172.16.0.2: bytes=32 time<1ms TTL=128
Reply from 172.16.0.2: bytes=32 time<1ms TTL=128
```
在SWB 上查看 STP 端口状态,确定交换机间哪一个端口(本例中是G1/0/23)处于转发
状态。将交换机之间处于STP 转发状态的端口上电缆断开,观察PCA 上发送的ICMP 报文有
无丢失。正常情况下,应该没有根文丢失或仅有一个报文丢失。

```cmd
[SWB]display stp brief
MSTID Port Role STP State Protection
이 GigabitEthernet1/0/1 DESI FORWARDING NONE
이 GigabitEthernetl1/0/24 ROOT FORWARDING NONE
```
可以看到,原来处于阻塞状态的端口 G1/0/24迁移到了转发状态。 edu. mo
无报文丢失说明目前STP 的收敛速度很快。其实,这就是RSTP/MSTP 相对于STP 的改进之一。缺省情况下,交换机运行 MSTP,SWB上的两个端口中有一个是根端口,另外一个是备份根端口。当原根端口断开时,备份根端口快速切换到转发状态。
### 步骤五:端口状态迁移查看
在交换机 SWA 上断开端口 G1/0/1的电缆,再重新连接,并且在 SWA 上查看交换机输出
信息。如下:
```cmd
[SWA]
GigabitEthernet1/0/1: link status is UP
Apr 26 14:04:53:880 2000 SWA MSTP/2/PFWD:Instance 0's GigabitEthernet1/0/1
been set to forwarding state!
```
```cmd
[SWA]interface GigabitEthernet 1/0/1
[SWA-GigabitEthernet1/0/1] undo stp edged-port

[SWA]display stp brief
이 GigabitEthernet1/0/1 DESI DISCARDING NONE
0 O GigabitEthernet1/0/24 DESI FORWARDING NONE

[SWA]display stp brief
GigabitEthernet1/0/1 DESI LEARNING NONE
GigabitEthernet1/0/24 DESI FORWARDING NONE

[SWA]display stp brief
이 GigabitEthernet1/0/1 DESI FORWARDING NONE
0 GigabitEthernet1/0/24 DESI FORWARDING NONE
```

## config

```cmd
 #PCA
interface GigabitEthernet0/0/1
 ip address 172.16.0.1 255.255.255.0

#PCB
interface GigabitEthernet0/0/1
 ip address 172.16.0.2 255.255.255.0

# SWA
 stp instance 0 priority 0
 stp global enable
interface GigabitEthernet1/0/1
 port link-mode bridge
 stp edged-port
#
disp stp 

# SWB
 stp instance 0 priority 4096
 stp global enable
interface GigabitEthernet1/0/1
 port link-mode bridge
 stp edged-port
#
disp stp 
```

## cmd list

- stp global enable 开启或关闭全局或端口的STP特 
- stp mode {mstp| pvst| rstp| stp}设置MSTP的工作模式

- stp [instance instance-list|vlan vlan-id-list] priority priority 配置设备的优先级
- stp edged-port 将当前的以太网端口配置为边缘端口
- disp stp [instance instance-list|vlan vlan-id-list] [interface interface-list|slot slot-number] [brief] 显示生成树的状态信息与统计信息


## 結語:

完成
