# Lab11_configOSPF 

- config an OSPF area 单区域OSPF
- config the OSPF DR priority
- config OSPF costs
- describe OSPF route selection
- config multiple OSPF area

Familiarity with OSPF working principles and basic OSPF config commands.

## Lab Diagrams

![](https://github.com/eddylin2015/H3C-CM446-10-2025-C/blob/main/img/lab11_labDiagram.png?raw=true)

## 实验任务一描述，这是一个典型的单区域OSPF基础配置场景。
组网图关键信息提取：
设备：两台路由器 RTA 和 RTB

客户端：ClientA（网关为RTA）、ClientB（网关为RTB）

Router ID：
- RTA: 1.1.1.1（Loopback接口地址）
- RTB: 2.2.2.2（Loopback接口地址）

OSPF区域：两台路由器均属于区域 0（单区域）

目标：
- RTA与RTB之间网络互通
- ClientA与ClientB之间互通

## 实验任务二描述，这是一个典型的多链路、单区域OSPF组网，重点在于验证OSPF的路由选择机制。以下是配置思路与分析：

组网图关键信息提取：
设备：两台MSR3620路由器（RTA和RTB）

Router ID:
- RTA: 1.1.1.1 (Loopback0)
- RTB: 2.2.2.2 (Loopback0)

OSPF区域：两台路由器均属于区域 0（单区域）

关键特征：RTA和RTB之间有两条链路连接

实验目的：模拟并理解OSPF的路由选择过程

## 实验任务三描述，这是一个典型的多区域OSPF组网。以下是详细的配置思路和分析：

组网图关键信息提取：
设备：三台MSR3620路由器（RTA、RTB、RTC）和两台PC（ClientA、ClientB）

Router ID:

- RTA: 1.1.1.1 (Loopback0)
- RTB: 2.2.2.2 (Loopback0)
- RTC: 3.3.3.3 (Loopback0)

OSPF区域划分：

- 区域0：RTA的G0/0接口 ↔ RTB的G0/0接口
- 区域1：RTB的G0/1接口 ↔ RTC的G0/0接口

网关分配：

- ClientA的网关：RTA
- ClientB的网关：RTC

目标：全网互通，ClientA与ClientB能通信

## Lab Task1:config basic OSPF single-area Settings
### step1
- clientA 10.0.0.1/24 10.0.0.2
- clientB 10.1.0.1/24 10.1.0.2

### step2
```cmd
[RTA]interface g0/0
[RTA interface g0/0]ip addr 20.0.0.1 24
[RTA]interface g0/1
[RTA interface g0/1]ip addr 10.0.0.2 24
[RTA interface g0/1]interface loopback 0
[RTA  loopback 0]ip addr 1.1.1.1 32

[RTB]interface g0/0
[RTB interface g0/0]ip addr 20.0.0.2 24
[RTB]interface g0/1
[RTB interface g0/1]ip addr 10.1.0.2 24
[RTB interface g0/1]interface loopback 0
[RTB  loopback 0]ip addr 2.2.2.2 32
```
### step3 检查连通和路由表
- [PCA]ping 10.1.0.1 PCB Unreachable
- RTA 没有10.1.0.1的路由
- [RTA]disp ip routing-table #直接路由，需要设定静态路由/动态OSPF

### step4 OSPF
```cmd
[RTA]router id 1.1.1.1
[RTA]ospf 1
[RTA ospf 1]area 0.0.0.0
[RTA ospf 1area 0.0.0.0]network 1.1.1.1 0.0.0.0 
[RTA ospf 1area 0.0.0.0]network 10.0.0.0 0.0.0.255 
[RTA ospf 1area 0.0.0.0]network 20.0.0.0 0.0.0.255
```
```cmd
[RTB]router id 2.2.2.2
[RTB]ospf 1
[RTB ospf 1]area 0.0.0.0
[RTB ospf 1area 0.0.0.0]network 2.2.2.2 0.0.0.0 
[RTB ospf 1area 0.0.0.0]network 10.1.0.0 0.0.0.255 
[RTB ospf 1area 0.0.0.0]network 20.0.0.0 0.0.0.255
```
### step5 路由器ospf neighbors and routing tables
```cmd
[RTA]disp ospf peer
OSPF Process 1 with Router ID 1.1.1.1 Neighbor Brief Info

[RTA]disp ospf routing
OSPF Process 1 with Router ID 1.1.1.1 Routing table

[RTA]disp ip routing-table
Dest/Mask Proto Pre Cost NextHop Interface

```
RTA 路由器加到达RTB 2.2.2.2/32 10.1.0.0/24路由。
### step6 网络连通


## Lab Task2:单区域OSPF增强配置
### step1

### step2
接口IPAddr,OSPF配置·
```cmd
[RTA]interface g0/0
[RTA-GigabitEthernet0/0]ip address 20.0.0.124
[RTA-GigabitEtherneto/0]interface go/1
[RTA-GigabitEthernet0/1]ip address 10.0.0.124
[RTA-GigabitEtherneto/1]interface loopback 0
[RTA-Loopback0]ip address 1.1.1.132
[RTA-Loopback0]quit
[RTA]router id 1.1.1.1
[RTA]ospf 1
[RTA-ospf-1]area 0.0.0.0
[RTA-ospf-1-area-0.0.0.0]network 1.1.1.10.0.0.0
[RTA-ospf-1-area-0.0.0.0]network 10.0.0.00.0.0.255
[RTA-Ospf-1-area-0.0.0.0]network 20.0.0.00.0.0.255

[RTB]interface g0/0
[RTB-GigabitEthernet0/0]ip address 20.0.0.224
[RTB-GigabitEtherneto/0]interface go/1
[RTB-GigabitEthernet0/1]ip address 10.0.0.224
[RTB-GigabitEtherneto/1]interface loopback 0
[RTB-Loopback0]ip address 2.2.2.232
[RTB-Loopback0]quit
[RTB] routerid 2.2.2.2
mbc.
[RTB]ospf1
[RTB-Ospf-1]area 0.0.0.0
[RTB-ospf-1-area-0.0.0.0]network 2.2.2.20.0.0.0
[RTB-ospf-1-area-0.0.0.0]network 10.0.0.00.0.0.255
[RTB-ospf-1-area-0.0.0.0]network20.0.0.00.0.0.255
```
### step3 路由OSPF邻居状态，路由表
```cmd
[RTA]display ospf peer
OSPE Process 1 with Router ID 1.1.1.1
Neighbor Brief Information
路由器ID	邻居接口地址	优先级	Dead-Time	状态	本地接口
2.2.2.2	20.0.0.2	1	38	Full/DR	GE0/0
2.2.2.2	10.0.0.2	1	34	Full/DR	GE0/1

[RTA]display ospf routing
OSPF Process 1 with Router ID 1.1.1.1 Routing Table
Routing for network
Destination Cost Type NextHop AdvRouter Area
20.0.0.0/24   1  Transit 0.0.0.0 2.2.2.2 2.2.2.2 0.0.0.0 0.0.0.0
10.0.0.0/24   1  Transit 0.0.0.0 2.2.2.2 2.2.2.2 0.0.0.0 0.0.0.0
2.2.2.2/32    1   stub 10.0.0.2 20.0.0.2 2.2.2.2 2.2.2.2 0.0.0.0
2.2.2.2/32    1   stub 20.0.0.2 20.0.0.2 2.2.2.2 2.2.2.2 0.0.0.0
1.1.1.1/32    0   Stub 0.0.0.0 1.1.1.1 0.0.0.0
Total nets:5
Intra area:5Inter area:0·ASE:0NSSA:0

[RTA]disp ip routing-table
2.2.2.2/32 O_INTRA 10 1 10.0.0.2 GE0/1
                        20.0.0.2 GE0/0
```
### step4 modify the ospf cost of an interface
```cmd
[RTA]interface g0/0
[RTA-GigabitEthernet0/0]ospf cost 150
```
### step5 
```cmd
[RTA]display ospf routing

OSPF process 1 

[RTA-GigabitEthernet0/0]display ip routing-table
Destinations:18 Routes:18
Destination/Mask Proto Pre Cost NextHop Interface
1.1.1.1/32 Direct 0 10.0.0.0/24 0.0.0.0/32
2.2.2.2/32 O_INTRA 10 1 10.0.0.2 GE0/1
```
### step6 modify the ospf DR priority of an interface
```cmd
[RTB]interface g0/0
[RTB-GigabitEthernet0/1]ospf dr-priority 0
```
### step7 restart the ospf process on the routers
```cmd
<RTB>reset ospf 1 process
Warning:Reset OSPE process?[Y/N]:y

<RTA>reset ospf 1 process
Warning:Reset OSPE process?[Y/N]:y

```
### step8 check the state ospf neighbors.
```cmd
[RTA]display ospf peer
OSPF Process 1 with Router ID 1.1.1.1
mbc.
Neighbor Brief Information
Area:0.0.0.0
Router ID Address Pri Dead-Time State Interface
2.2.2.2 20.0.0.2 1 39 Ful1/DROther GE0/0 
2.2.2.2 10.0.0.2 1 38 Ful1/DR GE0/1
```
## Lab Task3:多区域OSPF

### step1
First, estabish the lab environment as illutrted in the lab diagram.Then,configure the IP address of Client A as 10.0.0.1/24 and specify the gateway address as 10.0.0.2.Configure the IP address of Client B as 10.1.0.1/24 and specify the gateway address as 10.1.0.2.

### step2
```cmd
[RTA]interface G0/0
[RTA-GigabitEthernet0/0]ip address 20.0.0.1 24
[RTA-GigabitEthernet0/0]interface G0/1
[RTA-GigabitEthernet0/1]ip address 10.0.0.1 24
[RTA-GigabitEthernet0/1]interface loopback 0
[RTA-Loopback0]ip address 1.1.1.1 32
[RTA-Loopback0]quit
[RTA]router id 1.1.1.1
[RTA]ospf 1
[RTA-ospf-1]area 0.0.0.0
[RTA-ospf-1-area-0.0.0.0]network 1.1.1.1 0.0.0.0
[RTA-ospf-1-area-0.0.0.0]network 10.0.0.0 0.0.0.255
[RTA-ospf-1-area-0.0.0.0]network 20.0.0.0 0.0.0.255

[RTB]interface G0/0
[RTB-GigabitEthernet0/0]ip address 20.0.0.2 24
[RTB-GigabitEthernet0/0]interface G0/1
[RTB-GigabitEthernet0/1]ip address 10.0.0.2 24
[RTB-GigabitEthernet0/1]interface loopback 0
[RTB-Loopback0]ip address 2.2.2.2 32
[RTB-Loopback0]quit
[RTB]router id 2.2.2.2
[RTB]ospf 1
[RTB-ospf-1]area 0.0.0.0
[RTB-ospf-1-area-0.0.0.0]network 2.2.2.2 0.0.0.0
[RTB-ospf-1-area-0.0.0.0]network 20.0.0.0 0.0.0.255
[RTB-ospf-1-area-0.0.0.0]QUIT
[RTB-ospf-1-area]area 1
[RTB-ospf-1-area-0.0.0.1]network 30.0.0.0 0.0.0.255


[RTC]interface G0/0
[RTC-GigabitEthernet0/0]ip address 30.0.0.2 24
[RTC-GigabitEthernet0/0]interface G0/1
[RTC-GigabitEthernet0/1]ip address 10.0.0.2 24
[RTC-GigabitEthernet0/1]interface loopback 0
[RTC-Loopback0]ip address 3.3.3.3 32
[RTC-Loopback0]quit
[RTC]router id 3.3.3.3
[RTC]ospf 1
[RTC-ospf-1]area 0.0.0.1
[RTC-ospf-1-area-0.0.0.1]network 3.3.3.3 0.0.0.0
[RTC-ospf-1-area-0.0.0.1]network 10.0.0.0 0.0.0.255
[RTC-ospf-1-area-0.0.0.1]network 30.0.0.0 0.0.0.255
```
### step3 ospf neighbor and routing-table
```cmd
[RTB]disp ospf peer
OSPF Process 1 with RID 2.2.2.2 Neighbor brief info
Area 0.0.0.0
1.1.1.1 20.0.0.1 1 39 Full/DR G0/0
Area 0.0.0.1
3.3.3.3 30.0.0.1 1 35 Full/DR G0/1

[RTB]disp ospf routing
OSPF Process 1 with RID 2.2.2.2 routing table

[RTB]disp ip routing-table
      Direct
      O_INTRA
      O_INTRA
      O_INTRA

```
### step4 ping

```cmd
```


## command reference

- router id router-id
- ospf process-id
- area area-id
- network network ip-addr wildcard-mask
- ospf dr-priority proirity
- ospf cost value

