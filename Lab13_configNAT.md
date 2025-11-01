# Lab13_configNAT 
## Purpose
- Basic NAT
- NAPT
- Easy IP
- NAT Server

## LabDiagram

![](https://github.com/eddylin2015/H3C-CM446-10-2025-C/blob/main/img/lab13labDiagram.png?raw=true)

## LabProcedure

### LabTask1: config basic NAT
实验任务 1：配置基本 NAT

在本测试中，私网客户端 Client_A 和 Client_B 需要访问公网服务器。RTB 不存储私网路由，因此在 RTA 上配置基本 NAT，为 Client_A 和 Client_B 动态分配公网地址。

步骤 1：搭建测试环境
根据图 13-1 搭建测试环境，在 RTA 和 RTB 的端口上配置 IP 地址。为了路由发往服务器的数据包，在 RTA 上配置指向 RTB 的静态路由，下一跳为 RTB G0/0。RTA 应能 ping 通服务器。将 Client_A 的 IP 地址配置为 10.0.0.1/24，网关为 10.0.0.254；将 Client_B 的 IP 地址配置为 10.0.0.2/24，网关为 10.0.0.254。

步骤 2：基本配置
配置 IP 地址和路由。

text
[RTA]interface GigabitEthernet 0/0
[RTA-GigabitEthernet0/0]ip address 10.0.0.254 24
[RTA]interface GigabitEthernet 0/1
[RTA-GigabitEthernet0/1]ip address 198.76.28.1 24
[RTA]ip route-static 0.0.0.0 0 198.76.28.2

[RTB]interface GigabitEthernet 0/0
[RTB-GigabitEthernet0/0]ip address 198.76.28.2 24
[RTB]interface GigabitEthernet 0/1
[RTB-GigabitEthernet0/1]ip address 198.76.29.1 24
步骤 3：检查连通性
分别在 Client_A 和 Client_B 上 ping 服务器（IP 地址 198.76.29.4）。输出信息如下：

text
C:\>ping 198.76.29.4

Ping 198.76.29.4 with 32-byte data:
Request timeout.
Request timeout.
Request timeout.

Ping statistics of 198.76.29.4:
Packets: sent = 4, received = 0, lost = 4 (100% lost),
根据上述信息，Client_A 和 Client_B 无法 ping 通服务器。因为 RTB 没有私网路由，所以对于从服务器发送的 ping 数据包，RTB 无法找到目的地址为网段 10.0.0.0 的路由。

配置基本 NAT

在 RTA 上配置基本 NAT。

text
# 使用 ACL 定义源地址位于网段 10.0.0.0/24 的流。
[RTA]acl number 2000
[RTA-acl-ipv4-basic-2000]rule 0 permit source 10.0.0.0 0.0.0.255
# 配置 NAT 地址池 1，地址范围为 198.76.28.11 到 198.76.28.20，用于地址转换。
[RTA]nat address-group 1
[RTA-address-group-1]address 198.76.28.11 198.76.28.20
# 进入接口视图。
[RTA]interface GigabitEthernet 0/1
# 将地址池 1 与 ACL 2000 关联，并通过出站端口交付地址。
[RTA-GigabitEthernet0/1]nat outbound 2000 address-group 1 no-pat
在 RTA 上配置了公网地址池 address-group 1，地址范围为 198.76.28.11~198.76.28.20。参数 no-pat 表示进行一对一地址转换，即只转换地址而不转换端口号。在这种情况下，RTA 将转换出站数据包中匹配 ACL 2000 规则的地址。

步骤 1：检查连通性
分别在 Client_A 和 Client_B 上 ping 服务器。Client_A 和 Client_B 应能 ping 通服务器。

text
C:\>ping 198.76.29.4

Ping 198.76.29.4 with 32-byte data:
Reply from 198.76.29.4: byte=32 time=4ms TTL=253
Reply from 198.76.29.4: byte=32 time=1ms TTL=253
Reply from 198.76.29.4: byte=32 time=1ms TTL=253
Reply from 198.76.29.4: byte=32 time=1ms TTL=253

Ping statistics of 198.76.29.4:
Packets: sent = 4, received = 4, lost = 0 (0% lost),
Estimated round trip time (unit in ms):
Shortest = 1ms, longest = 4ms, average = 12ms
步骤 2：检查 NAT 表项
在 RTA 上检查 NAT 表项。

text
<RTA>display nat session
Initiate:
    Source IP/port: 10.0.0.2/249

### LabTask2: config NAPT

### LabTask3: Easy IP

### LabTask4: NAT Server


## command reference
![](https://github.com/eddylin2015/H3C-CM446-10-2025-C/blob/main/img/lab13commandreference.png?raw=true)

