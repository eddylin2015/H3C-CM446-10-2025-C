# Lab11_configOSPF 

- config an OSPF area 单区域OSPF
- config the OSPF DR priority
- config OSPF costs
- describe OSPF route selection
- config multiple OSPF area

Familiarity with OSPF working principles and basic OSPF config commands.

## Lab Diagrams

![](https://github.com/eddylin2015/H3C-CM446-10-2025-C/blob/main/img/lab11_labDiagram.png?raw=true)

实验任务一组网如图11-1所示。本组网模拟单区域 OSPF 的应用。RTA 和RTB 分别是客
户端 ClientA 和 ClientB 的网关。RTA 设置 loopback 口地址 1.1.1.1 为RTA 的 Router ID, RTB
设置 loopback 口地址 2.2.2.2 为RTB 的 Router ID, RTA 和RTB 都属于同一个OSPF 区域0。
RTA 和RTB之间的网络能互通,客户端 ClientA 和 ClientB 能互通。

实验任务二组网如图11-2所示,由2台 MSR3620 (RTA、RTB)路由器组成。本组网模
拟实际组网中OSPF的路由选择。RTA 设置 loopback 口地址 1.1.1.1 为RTA 的 Router ID,
RTB 设置 loopback 口地址 2.2.2.2 为RTB 的 Router ID, RTA//和RTB 都属于同一个OSPF 区
域0。RTA 和RTB 之间有两条链路连接。

实验任务三组网如图11-3 所示,由3 台 MSR3620 (RTA、RTB、RTC)路由器、2台 PC
(ClientA、ClientB)组成。本组网模拟实际组网中多区域 OSPF的应用。RTA 和RTC 分别是
客户端 ClientA 和 ClientB 的网关。RTA 设置 loopback 口地址 1.1.1.1 为RTA 的 Router ID,
RTB 设置 Iloopback 地址 2.2.2.2 为RTB 的 Router ID,RTC 设置 loopback 口地址 3.3.3.3
为RTC 的 Router ID。RTA 和RTB的G0/0 口属于同一个OSPF 区域 0, RTB 的G0/1口和
RTC 属于同一个OSPF 区域1.RTA、RTB 和RTC之间的网络能互通,客户端 ClientA和 ClientB
能互通。


## 实验任务一描述，这是一个典型的单区域OSPF基础配置场景。

### 步骤一:搭建实验环境
首先,依照图 11-1 搭建实验环境。配置客户端 ClientA 的IP地址为 10.0.0.1/24,网关为
10.0.0.2;配置客户端 ClientB的IP地址为10.1.0.1/24,网关为10.1.0.2.

### 步骤二:基本配置
在路由器上完成接口 IP地址等基本配置。
```cmd
[RTA]interface GO/0
[RTA-GigabitEthernet0/0]ip address 20.0.0.1 24
[RTA-GigabitEthernet0/0]interface GO/1
[RTA-GigabitEthernet0/1]ip address 10.0.0.2 24
[RTA-GigabitEthernet0/1]interface loopback 0
[RTA-Loopback0]ip address 1.1.1.1 32

[RTB]interface G0/0
[RTB-GigabitEthernet0/0]ip address 20.0.0.2 24
[RTB-GigabitEthernet0/0]interface GO/1
[RTB-GigabitEthernet0/1]ip address 10.1.0.2 24
[RTB-GigabitEthernet0/1]interface loopback 0
[RTB-Loopback0]ip address 2.2.2.2 32
```
### 步骤三:检查网络连通性和路由器路由表 
在 ClientA 上ping ClientB(IP地址为10.1.0.1),显示如下:
```cmd
C:\>ping 10.1.0.1
Pinging 10.1.0.1 with 32 bytes of data:
From 10.0.0.2 : Destination Net Unreachable
...
From 10.0.0.2 : Destination Net Unreachable
Ping statistics for 10.1.0.1:
Packets: Sent = 4, Received = 0, Lost = 4 (100% loss),
```
结果显示,从 ClientA 无法 ping 通 ClientB。这是因为在RTA 上没有到 10.1.0.1 的路由。
在RTA 上使用 display ip routing-table 查看 RTA 的路由表,显示如下:
```cmd
[RTA]display ip routing-table
Destinations : 17 Routes : 17
Destination/Mask Proto Pre Cost NextHop Interface
0.0.0.0/32 Direct 이 127.0.0.1 InLoop0
1.1.1.1/32 Direct 이 이 127.0.0.1 InLoop0
10.0.0.0/24 Direct 이 이 10.0.0.2 GE0/1
10.0.0.0/32 Direct 이 이 10.0.0.2 GE0/1
10.0.0.2/32 Direct 00 127.0.0.1 InLoop0
10.0.0.255/32 Direct 0 0 10.0.0.2 GE0/1
20.0.0.0/24 Direct00 20.0.0.1 GEO/0
20.0.0.0/32 Direct0 20.0.0.1 GEO/0
20.0.0.1/32 Direct 00 127.0.0.1 InLoop0
20.0.0.255/32 Direct 0 20.0.0.1 GEO/0
127.0.0.0/8 Direct 이 0 127.0.0.1 InLoop0
127.0.0.0/32 Direct00 127.0.0.1 InLoop0
127.0.0.1/32 Direct0 0 127.0.0.1 InLoop0
127.255.255.255/32 Direct 00 127.0.0.1 InLoop0
224.0.0.0/4 Direct 0 0 0.0.0.0 NULLO
224.0.0.0/24 Direct0 0 0.0.0.0 NULLO
255.255.255.255/32 Direct 00 127.0.0.1 InLoopO
```
RTA 上只有直连路由,没有到达 ClientB的路由表,故从 ClientA 上来的数据报文无法转
在RTB 上也执行以上的操作,查看相关信息。
发给 ClientB。
### 步骤四:配置 OSPF
在RTA 上配置 OSPF:
```cmd
[RTA] router id 1.1.1.1
[RTA]ospf 1
[RTA-ospf-1]area 0.0.0.0 edu. mo
[RTA-ospf-1-area-0.0.0.0] network 1.1.1.1 0.0.0.0
[RTA-ospf-1-area-0.0.0.0] network 10.0.0.0 0.0.0.255
[RTA-Ospf-1-area-0.0.0.0] network 20.0.0.0 0.0.0.255
```
在RTB上配置 OSPF:
```cmd
[RTB] router id 2.2.2.2
[RTB]ospf 1 
[RTB-ospf-1-area-0.0.0.0] network 2.2.2.2 0.0.0.0
[RTB-ospf-1]area 0.0.0.0
[RTB-ospf-1-area-0.0.0.0] network 10.1.0.0 0.0.0.255
[RTB-ospf-1-area-0.0.0.0] network 20.0.0.0 0.0.0.255
```
### 步骤五:检查路由器 OSPF 邻居状态及路由表
在RTA 上使用 display ospf peer 查看路由器 OSPF 邻居状态,显示如下:
```cmd
[RTA]display ospf peer
OSPF Process 1 with Router ID 1.1.1.1
Neighbor Brief Information
Area: 0.0.0.0
Router ID Address Pri Dead-Time State
2.2.2.2 20.0.0.2 1 32
Interface
Full/DR GE0/0
```
RTA与Router ID 为2.2.2.2 (RTB)的路由器上配置 IP地址 20.0.0.2 的接口互为邻居,
RTB 的配置 IP地址 20.0.0.2 的接口为该网段的DR 路由器。此时,邻居状态达到 Full,说明
RTA 和 RTB之间的链路状态数据库已经同步,RTA 具备到达RTB 的路由信息。
在RTA 上使用 display ospf routing 查看路由器的OSPF 路由表,显示如下:
```cmd
[RTA] display ospf routing
OSPF Process 1 with Router ID 1.1.1.1
Routing Table
Routing for network
Destination Cost Type NextHop AdvRouter Area
20.0.0.0/24 1 Transit 0.0.0.0 2.2.2.2 0.0.0.0
10.0.0.0/24 1 Stub 0.0.0.0 1.1.1.1 0.0.0.0
2.2.2.2/32 1 Stub 20.0.0.2 2.2.2.2 0.0.0.0.
10.1.0.0/24 2 Stub 20.0.0.2 2.2.2.2 0.0.0.0
1.1.1.1/32 이 Stub 0.0.0.0 1.1.1.1 0.0.0.0
Total nets: 5
Intra area: 5 Inter area: 0 ASE: 0 NSSA: 0
```
RTA  上使用 display ip routing-table  查看路由器全局路由表,显示如下:
```cmd
[RTA]display ip routing-table
Destinations : 19 Routes : 19
Destination/Mask Proto Pre Cost NextHop Interface
0.0.0.0/32 Direct 0 0 127.0.0.1 InLoop0
1.1.1.1/32 Direct 0 0 127.0.0.1 InLoop0
2.2.2.2/32 O INTRA 10 1
N
20.0.0.2 GEO/0
10.0.0.0/24 Direct 0 0 10.0.0.2 GEO/1
10.0.0.0/32 Direct 0 0 10.0.0.2 GE0/1
10.0.0.2/32 Direct 0 이 127.0.0.1 InLoop0
10.0.0.255/32 Direct 이 10.0.0.2 GEO/1
10.1.0.0/24 O INTRA 10 2 20.0.0.2 GEO/0
20.0.0.0/24 Direct 0 0 20.0.0.1 GEO/0
20.0.0.0/32 Direct 0 0 20.0.0.1 GE0/0
20.0.0.1/32 Direct 이 이 127.0.0.1 InLoopo
20.0.0.255/32 Direct 0 이 20.0.0.1 GE0/0
127.0.0.0/8 Direct 0 0 127.0.0.1 InLoop0
127.0.0.0/32 Direct 0 0 127.0.0.1 InLoop0
127.0.0.1/32 Direct 00 127.0.0.1 InLoop0
127.255.255.255/32 Direct 0 0 127.0.0.1 InLoop0
224.0.0.0/4 Direct00 0.0.0.0 NULLO
224.0.0.0/24 Direct00 0.0.0.0 NULLO
255.255.255.255/32 Direct 0 0 127.0.0.1 InLoop0
```
RTA 路由器全局路由表里加入了到达RTB 的2 22 2/32 和 10.1.0.0/24 网段的路由
在RTB 上也执行以上的操作,查看相关信息。

### 步骤六:检查网络连通性
在 ClientA 上 ping ClientB(IP地址为10.1.0.1),
```cmd
C:\>ping 10.1.0.1
Pinging 10.1.0.1 with 32 bytes of data:
Reply from 10.1.0.1: bytes=32 time=1ms TTL=126
Reply from 10.1.0.1: bytes=32 time=1ms TTL=126
Reply from 10.1.0.1: bytes=32 time=1ms TTL=126
```
在 ClientB 上ping ClientA(IP地址为 10.0.0.1),显示如下:
```cmd
C:\>ping 10.0.0.1
Pinging 10.0.0.1 with 32 bytes of data:
Reply from 10.0.0.1: bytes=32 time=1ms TTL=126
Reply from 10.0.0.1: bytes=32 time=1ms TTL=126
Reply from 10.0.0.1: bytes=32 time=1ms TTL=12
```
## 实验任务二:单区域 OSPF 增强配置
### 步骤一:搭建实验环境
首先,依照图 11-2搭建实验环境。
### 步骤二:基本配置
在路由器上完成接口 IP地址、OSPF 等基本配置。
```cmd
[RTA-GigabitEthernet0/0]ip address 20.0.0.1 24
[RTA]interface GO/0
[RTA-GigabitEthernet0/0]interface G0/1
[RTA-GigabitEthernet0/1]ip address 10.0.0.1 24
[RTA-GigabitEthernet0/1]interface loopback 0
[RTA-Loopback0]ip address 1.1.1.1 32 
[RTA-Loopback0]quit
[RTA]router id 1.1.1.1 
[RTA]ospf 1
[RTA-Ospf-1]area 0.0.0.0
[RTA-ospf-1-area-0.0.0.0] network 1.1.1.1 0.0.0.0
[RTA-ospf-1-area-0.0.0.0] network 10.0.0.0 0.0.0.255
[RTA-ospf-1-area-0.0.0.0]network 20.0.0.0 0.0.0.255

[RTB]interface GO/0
[RTB-GigabitEthernet0/0]interface GO/1
[RTB-GigabitEthernet0/1]ip address 10.0.0.2 24
[RTB-GigabitEthernet0/1]interface loopback 0
[RTB-Loopback0]ip address 2.2.2.2 32
[RTB-Loopback0] quit
[RTB] router id 2.2.2.2
[RTB]ospf 1
[RTB-ospf-1]area 0.0.0.0
[RTB-ospf-1-area-0.0.0.0] network 2.2.2.2 0.0.0.0
[RTB-ospf-1-area-0.0.0.0]network 10.0.0.0 0.0.0.255
[RTB-ospf-1-area-0.0.0.0] network 20.0.0.0 0.0.0.255
```
### 步骤三:检查路由器 OSPF 邻居状态及路由表 
在RTA 上使用 display ospf peer 查看路由器 OSPF 邻居状态,显示如下:
```cmd
[RTA]display ospf peer
OSPF Process 1 with Router ID 1.1.1.1
Neighbor Brief Information 
Area: 0.0.0.0
Router ID Address Pri Dead-Time State Interface
2.2.2.2 20.0.0.2 1 38 Full/DR GEO/0
2.2.2.2 10.0.0.2 1 34 Full/DR GEO/1
```
RTA 与 Router ID 为2.2.2.2 (RTB)的路由器建立了两个邻居,RTA的GO/0接口与RTB
配置 IP地址 20.0.0.2 的接口建立一个邻居,该邻居所在的网段为20.0.0.0/24, RTB 配置IP地
址 20.0.0.2的接口为该网段的DR路由器;另外,RTA的G0/1 接口与RTB 配置的IP地址
10.0.0.2 的接口建立一个邻居,该邻居所在的网段为 10.0.0.0/24,RTB 配置 IP地址 10.0.0.2
的接口为该网段的DR 路由器。

在RTA 上使用 display ospf routing 查看路由器 OSPF 路由表,显示如下:
```cmd
[RTA]display ospf routing
OSPF Process 1 with Router ID 1.1.1.1 Routing Table
Routing for network
Destination Cost Type NextHор AdvRouter Area
20.0.0.0/24 O 1 Transit 0.0.0.0 2.2.22 0.0.0.0
10.0.0.0/24 1 Transit 0.0.0.0 2.2.2.2 0.0.0.0
2.2.2.2/32 1 Stub 10.0.0.2 2.2.2.2 0.0.0.0
2.2.2.2/32 1 Stub 20.0.0.2 222.2 0.0.0.0
1.1.1.1/32 이 Stub 0.0.0.0 1.1.1.1 0.0.0.0
```
在RTA的OSPF 路由表上有两条到达RTB 的2.2.2.2/32网段的路由,一条是邻居 20.0.0.2
发布的,另一条是邻居 10.0.0.2发布的,这两条路由的Cost 相同。
 RTA 上使用 display ip routing-table 查看路由器全局路由表,显示如下: 
 ```cmd
 [RTA]display ip routing-table
 
Destinations : 18 Routes : 19
Destination/Mask Proto Pre Cost NextHop Interface
0.0.0.0/32 Direct 이 0 127.0.0.1 InLoop0
1.1.1.1/32 Direct 이 0 127.0.0.1 InLoop0
2.2.2.2/32 O INTRA 10 10.0.0.2 GE0/1
20.0.0.2 GE0/0
10.0.0.0/24 Direct 0
0
10.0.0.1 GE0/1
10.0.0.0/32 Direct 0 0 10.0.0.1 GEO/1
10.0.0.1/32 Directo0 127.0.0.1 InLoop0
10.0.0.255/32 Direct 0 0 10.0.0.1 GE0/1
20.0.0.0/24 Direct 0 0 20.0.0.1 GE0/0
20.0.0.0/32 Direct 0 0 20.0.0.1 GE0/0
20.0.0.1/32 Direct0 0 127.0.0.1 InLoopо
020.0.0.255/32 Direct 0 이 20.0.0.1 GE0/0
127.0.0.0/8 Direct 0 0 127.0.0.1 InLoopo
127.0.0.0/32 Direct 0 0 127.0.0.1 InLoop0
127.0.0.1/32 Direct 0 0 127.0.0.1 InLoop0
127.255.255.255/32 Direct 이 127.0.0.1 InLooр0
224.0.0.0/4 Direct0 0 0.0.0.0 NULLO
224.0.0.0/24 Direct 0 0 0.0.0.0 NULLO
255.255.255.255/32 Direct 0 0 127.0.0.1 InLoop0
```
在RTA 路由器全局路由表内,有两条到达RTB 的 2.2.2.2/32网段的等价 OSPF 路由。在
RTB 上也执行以上的操作,查看相关信息。


### 步骤四:修改路由器接口开销
在RTA 的 G0/0 接口上增加配置ospf cost 150。
```cmd
[RTA]interface G0/0
[RTA-GigabitEthernet0/0]ospf cost 150
```

### 步骤五:检查路由器路由表

在RTA 上使用命令 display ospf routing 查看路由器 OSPF 路由表,显示如下,
[RTA]display ospf routing

OSPF Process 1 with Router ID 1.1.1.1
Routing Table
Routing for network
Destination Cost Type NextHop AdvRouter Area
20.0.0.0/24 150 Transit 0.0.0.0 2.2.2.2 0.0.0.0
10.0.0.0/24 1 Transit 0.0.0.0 2.2.2.2 0.0.0.0
2.2.2.2/32 1 Stub 10.0.0.2 2.2.2.2 0.0.0.0
1.1.1.1/32 이 Stub 0.0.0.0 1.1.1.1 0.0.0.0
Total nets: 4
Intra area: 4 Inter area: 0 ASE: 0 NSSA: 0
```
由于RTA 的G0/0 接口的开销配置为150,远高于 GO/1 接口的开销,故在RTA 的OSPF
路由表上仅有一条由邻居 10.0.0.2(该邻居与RTA的G0/1 接口连接)发布的到达RTB 的
2.2.2.2/32 网段的路由。 
在RTA 上使用 display ip routing-table 查看路由器全局路由表,显示如下,
```cmd
[RTA-GigabitEthernet0/0]display ip routing-table
Destinations : 18 Routes : 18
Destination/Mask Proto Pre Cost NextHop Interface
0.0.0.0/32 Direct 0 0 127.0.0.1 InLoop0
1.1.1.1/32 Direct 0 0 127.0.0.1 InLoop0
2.2.2.2/32 O INTRA 10 1 10.0.0.2 GE0/1
10.0.0.0/24 Direct 0 이 10.0.0.1 GE0/1
10.0.0.0/32 Direct 0 0 10.0.0.1 GE0/1
10.0.0.1/32 Direct 이 이 127.0.0.1 InLoop0
10.0.0.255/32 Direct 00 10.0.0.1 GE0/1
20.0.0.0/24 Direct 0 0 20.0.0.1 GE0/0
20.0.0.0/32 Direct 0 이 20.0.0.1 GEO/0
20.0.0.1/32 Direct 이 127.0.0.1 InLoop0
20.0.0.255/32 Direct 이 0 20.0.0.1 GEO/0
127.0.0.0/8 Direct 0 127.0.0.10 InLoop0
127.0.0.0/32 Direct 00 127.0.0.1 InLoop0
127.0.0.1/32 Direct 0 0 127.0.0.1 InLoop0
127.255.255.255/32 Direct 00 127.0.0.1 InLoop0
224.0.0.0/4 Direct 0.0.0.0 NULLO
224.0.0.0/24 Direct 0 0.0.0.0 NULLO
255.255.255.255/32 Direct 0
이 127.0.0.1 InLoop0
```
在RTA 路由器全局路由表内,仅有一条通过 GO/1 到达RTB 的 2.2.2.2/32 网段的路由。
在RTB 上也执行以上的操作,查看相关信息。
步骤六:修改路由器接口优先级
在RTB的G0/0 上修改接口优先级为0.
```cmd
[RTB]interface G0/0
[RTB-GigabitEthernet0/0]ospf dr-priority 0
```
### 步骤七:在路由器上重启 OSPF 进程
先将 RTB的OSPF进程重启,再将RTA的OSPF进程重启。
```cmd
<RTB>reset ospf 1 process
Warning : Reset OSPF process? [Y/N]:y
<RTA>reset ospf 1 process
Warning : Reset OSPF process? [Y/N]:y
```
### 步骤八:在路由器 OSPF 邻居状态 
在RTA 上使用 display ospf peer 查看路由器 OSPF 邻居状态,显示如下 
```cmd
[RTA]display ospf peer 
OSPF Process 1 with Router ID 1.1.1.1
Neighbor Brief Information
Area: 0.0.0.0
Router ID Address Pri Dead-Time State Interface
2.2.2.2 20.0.0.2 0 39 Full/DROther GE0/0
2.2.2.2 10.0.0.2 1 38 Full/DR GE0/1
```
由于RTB的GO/0接口的dr 优先级为0,不具备 DR/BDR 选举权,故后启动 OSPF的RTA
接口 G0/0 成为该网段的DR路由器,RTB 的GO/0变为 DROther 路由器。

在RTB 上也执行以上的操作,查看相关信息。

## 实验任务三:多区域 OSPF 基本配置
### 步骤一:搭建实验环境 
首先,依照图 11-3 搭建实验环境。配置客户端 ClientA的IP地址为10.0.0.1/24,网关为
10.0.0.2;配置客户端 ClientB的IP地址为10.1.0.1/24,网关为10.1.0.2.

### 步骤二:基本配置
在路由器上完成接口 IP地址、OSPF 基本配置。
```cmd
[RTA]interface GO/0
[RTA-GigabitEthernet0/0]ip address 20.0.0.1 24
[RTA-GigabitEthernet0/0]interface GO/1
[RTA-GigabitEthernet0/1]ip address 10.0.0.1 24
[RTA-GigabitEthernet0/1]interface loopback 0
[RTA-Loopback0]ip address 1.1.1.1 32
[RTA-Loopback0]quit
[RTA]router id 1.1.1.1
[RTA]ospf 1
[RTA-ospf-1-area-0.0.0.0] network 1.1.1.1 0.0.0.0
[RTA-ospf-1]area 0.0.0.0
[RTA-ospf-1-area-0.0.0.0] network 10.0.0.0 0.0.0.255
[RTA-ospf-1-area-0.0.0.0] network 20.0.0.0 0.0.0.255
(RTBlinterface G0/0 
[RTB-GigabitEthernet0/0]ip address 20.0.0.2 24
[RTB-GigabitEthernet0/0]interface G0/1
[RTB-GigabitEthernet0/1]ip address 30.0.0.2 24
[RTB-GigabitEthernet0/1] interface loopback 0
[RTB-Loopback0]ip address 2.2.2.2 32
[RTB-Loopback0]quit
[RTB] router id 2.2.2.2

[RTB]ospf 1 
[RTB-ospf-1]area 0.0.0.0
[RTB-ospf-1-area-0.0.0.0] network 2.2.2.2 0.0.0.0
[RTB-ospf-1-area-0.0.0.0]network 20.0.0.0 0.0.0.255
[RTB-ospf-1-area-0.0.0.0] quit
[RTB-ospf-1-area]area 1
[RTB-ospf-1-area-0.0.0.1] network 30.0.0.0 0.0.0.25$
24
[RTC-GigabitEthernet0/1]interface loopback 0
[RTC]interface GO/0
[RTC-GigabitEthernet0/0]ip address 30.0.0.1
[RTC-GigabitEthernet0/0]interface G0/1
[RTC-GigabitEthernet0/1]ip address 10.1.0.2 24
[RTC-Loopback0]ip address 3.3.3.3 32
[RTC-Loopback0] quit
[RTC] router id 3.3.3.3
[RTC]ospf 1
[RTC-ospf-1]area 1
[RTC-ospf-1-area-0.0.0.1] network 3.3.3.3 0.0.0.0
[RTC-ospf-1-area-0.0.0.1] network 10.1.0.0 0.0.0.255
[RTC-ospf-1-area-0.0.0.1] network 30.0.0.0 0.0.0.255
```
### 步骤三:检查路由器 OSPF 邻居状态及路由表
在RTB 上使用 display ospf peer 查看路由器 OSPF 邻居状态,显示如下:
```cmd
[RTB] display ospf peer
OSPF Process 1 with Router ID 2.2.2.2
Neighbor Brief Information mbc.edu.
mbc.edu. mo
Area: 0.0.0.0
Router ID Address Pri Dead-Time State
11.1.1 20.0.0.1 1 39 Full/DR
Interface
GEO/0
Area: 0.0.0.1
Router ID
3.3.3.3
Address Pri Dead-Time
30.0.0.1 1 35
State
Full/DR
Interface
GE0/1
```
RTB 与Router ID 为1.1.1.1 (RTA)的路由器在Area 0.0.0.0 内,RTB的G0/0 接口与RTA
配置 IP地址为20.0.0.1 的接口建立邻居关系,该邻居所在的网段为20.0.0.0/24,RTA 配置 IP
地址为20.0.0.1 的接口为该网段的DR 路由器。

RTB 与 Router ID 为3.3.3.3 (RTC)的路由器在Area 0.0.0.1 内,RTB的GO/1 接口与
RTC 配置 IP地址为30.0.0.1 的接口建立邻居关系,该邻居所在的网段为30.0.0.0/24, RTC 配
置 IP地址为30.0.0.1的接口为该网段的DR 路由器。 mmou@mail.mbc.
在RTB 上使用 display ospf routing 查看路由器 OSPF 路由表,显示如下:
```cmd
[RTB]display ospf routing
OSPF Process 1 with Router ID 2.2.2.2
Routing Table
Routing for network
Destination Cost Type NextHop AdvRouter Area
20.0.0.0/24 1 Transit 0.0.0.0 1.1.1.1 0.0.0.0
10.0.0.0/24 2 Stub 20.0.0.1 1.1.1.1 0.0.0.0
3.3.3.3/32 1 Stub 30.0.0.1 3.3.3.3 0.0.0.1
2.2.2.2/32 0 Stub 0.0.0.0 2.2.2.2 0.0.0.0
10.1.0.0/24 2 Stub 30.0.0.1 3.3.3.3 0.0.0.1
30.0.0.0/24 1 Transit 0.0.0.0 3.3.3.3 0.0.0.1
1.1.1.1/32 1 Stub 20.0.0.1 1.1.1.1 0.0.0.0
```
在RTB的OSPF 路由表上有到达全部网络的路由。
在RTB 上使用 display ip routing-table 查看路由器全局路由表,显示如下:
```cmd
[RTB]display ip routing-table
Destinations : 21 Routes : 21 bc. ed
Destination/Mask Proto Pre Cost NextHoр Interface
0.0.0.0/32 Direct 00 127.0.0.1 InLoopо
1.1.1.1/32 O INTRA 10 1 20.0.0.1 GEO/0
2.2.2.2/32 Direct 0 0 127.0.0.1 InLoop0
3.3.3.3/32 O INTRA 10 1 30.0.0.1 GEO/1@ma
10.0.0.0/24 O INTRA 10 2 20.0.0.1 GEO/0
10.1.0.0/24 O INTRA 10 2 30.0.0.1 GE0/1
20.0.0.0/24 Direct 0 0 20.0.0.2 GEO/0
20.0.0.0/32 Direct 0 0 20.0.0.2 GE0/0
20.0.0.2/32 Direct00 127.0.0.1 InLoopо
20.0.0.255/32 Direct 0 이 20.0.0.2 GE0/0
30.0.0.0/24 Direct 0 0 30.0.0.2 GE0/1
30.0.0.0/32 Direct 00 30.0.0.2 GE0/1
30.0.0.2/32 Direct 0 0 127.0.0.1 InLoop0
30.0.0.255/32 Direct 00 30.0.0.2 GE0/1
127.0.0.0/8 Direct 0 127.0.0.1 InLoop0
127.0.0.0/32 Direct 0 0 127.0.0.1 InLoop0
127.0.0.1/32 Direct 0c0 127.0.0.1 InLoop0
127.255.255.255/32 Direct 0 0 127.0.0.1 InLoop0
224.0.0.0/4 Direct 0 0 0.0.0.0 NULLO
224.0.0.0/24 Direct 0 0 0.0.0.0 NULLO
255.255.255.255/32 Direct 00 127.0.0.1 InLoopoo
```
在RTB 路由器全局路由表内,有到达全部网络的路由。
在RTA、RTC 上也执行以上的操作,查看相关信息。
### 步骤四:检查网络连通性
在 ClientA 上ping ClientB(IP地址为10.1.0.1),显示如下:
```cmd
C:\>ping 10.1.0.1
Pinging 10.1.0.1 with 32 bytes of data:
Reply from 10.1.0.1: bytes=32 time=1ms TTL=126
```
在 ClientB上 ping ClientA(IP地址为10.0.0.1),显示如下:
```cmd
C:\>ping 10.0.0.1
Pinging 10.0.0.1 with 32 bytes of data:
Reply from 10.0.0.1: bytes=32 time=1ms TTL=126
Reply from 10.0.0.1: bytes=32 time=1ms TTL=126
```

## command reference

- router id router-id
- ospf process-id
- area area-id
- network network ip-addr wildcard-mask
- ospf dr-priority proirity
- ospf cost value


