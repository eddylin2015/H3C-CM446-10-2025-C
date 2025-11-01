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
### 实验任务 1：配置基本 NAT

- 私网客户端 Client_A 和 Client_B 需要访问公网服务器。
- RTB 不存储私网路由
- 因此在 RTA 上配置基本 NAT，为 Client_A 和 Client_B 动态分配公网地址。

### 步骤 1：搭建测试环境

根据图 13-1 搭建测试环境，在 RTA 和 RTB 的端口上配置 IP 地址。
为了路由发往服务器的数据包，在 RTA 上配置指向 RTB 的静态路由，下一跳为 RTB G0/0。
RTA 应能 ping 通服务器。
- 将 Client_A 的 IP 地址配置为 10.0.0.1/24，网关为 10.0.0.254；
- 将 Client_B 的 IP 地址配置为 10.0.0.2/24，网关为 10.0.0.254。

### 步骤 2：基本配置
配置 IP 地址和路由。

```cmd
[RTA]interface GigabitEthernet 0/0
[RTA-GigabitEthernet0/0]ip address 10.0.0.254 24
[RTA]interface GigabitEthernet 0/1
[RTA-GigabitEthernet0/1]ip address 198.76.28.1 24
[RTA]ip route-static 0.0.0.0 0 198.76.28.2

[RTB]interface GigabitEthernet 0/0
[RTB-GigabitEthernet0/0]ip address 198.76.28.2 24
[RTB]interface GigabitEthernet 0/1
[RTB-GigabitEthernet0/1]ip address 198.76.29.1 24
```
### 步骤 3：检查连通性
分别在 Client_A 和 Client_B 上 ping 服务器（IP 地址 198.76.29.4）。输出信息如下：

```cmd
C:\>ping 198.76.29.4
Ping 198.76.29.4 with 32-byte data:
Request timeout.
Request timeout.
Request timeout.
Ping statistics of 198.76.29.4:
Packets: sent = 4, received = 0, lost = 4 (100% lost),
```
根据上述信息，Client_A 和 Client_B 无法 ping 通服务器。因为 RTB 没有私网路由，所以对于从服务器发送的 ping 数据包，RTB 无法找到目的地址为网段 10.0.0.0 的路由。

### 配置基本 NAT

在 RTA 上配置基本 NAT。

```cmd
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
```
在 RTA 上配置了公网地址池 address-group 1，地址范围为 198.76.28.11~198.76.28.20。参数 no-pat 表示进行一对一地址转换，即只转换地址而不转换端口号。在这种情况下，RTA 将转换出站数据包中匹配 ACL 2000 规则的地址。

### 步骤 1：检查连通性
分别在 Client_A 和 Client_B 上 ping 服务器。Client_A 和 Client_B 应能 ping 通服务器。

```cmd
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
```
### 步骤 2：检查 NAT 表项
在 RTA 上检查 NAT 表项。

```cmd

<RTA>display nat session

Initiator:
Source IP/port: 10.0.0.2/249
Destination IP/port: 198.76.29.4/2048
DS-Lite tunnel peer:
VPN instance/VLAN ID/VLL ID: -/-1-
Protocol: ICMP(1)
Initiator:
Source IP/port: 10.0.0.1/210
Destination IP/port: 198.76.29.4/2048
DS-Lite tunnel peer:-
VPN instance/VLAN I
Protocol: ICMP(1)
Total sessions found:2

<RTA>disp nat no-pat
Local IP: 10.0.0.1
Global IP: 198.76.28.12
Reversible: N
Type : Outbound
Local IP: 10.0.0.2
Global IP: 198.76.28.11
Reversible:N
Type : Outbound
```
Check the entries one minute later. The last two entries are lost.
```cmd
<RTA>display nat session
Total sessions found: 0
```
The NAT entries have aging time (aging-time). After the aging time expires. 
```cmd
[RTA]display session aging-time state
State Aging Time (s)
SYN 30
TCP-EST 3600
FIN 30
UDP-OPEN 30
UDP-READY 60
ICMP-REQUEST 60 iL.mbc. e
ICMP-REPLY 30d
RAWIP-OPEN 30
RAWIP-READY 60
UDPLITE-OPEN 30
UDPLITE-READY 60
DCCP-REQUEST 30
DCCP-EST 3600
DCCP-CLOSEREQ 30
SCTP-INIT 30
SCTP-EST 3600
SCTP-SHUTDOWN 30
ICMPV6-REQUEST 60
ICMPV6-REPLY 30
```
Run the session aging-time command to modify the connection aging time of NAT
sessions.
The NAT debugging information is as follows:
```cmd
<RTA>terminal monitor
# The current terminal is enabled to display logs.
<RTA>terminal debugging
# The current terminal is enabled to display debugging
<RTA>debugging nat packet
<RTA>*Nov 13 10:05:09:565 2014 RTA NAT/7/COMMON:
PACKET: (GigabitEthernet0/0-out) Protocol: ICMP 
10.0.0.1:0 - 198.76.29.4: 0(VPN: 0) ------>
198.76.28.14: 0- 198.76.29.4: 0 (VPN: 0)
```
Based on the debugging information, in the GigabitEthernet0/0-out direction, the source
address of the ICMP packets 10.0.0.1 is converted to 198.76.28.14.

IP+Port(65536) 用地址转换。

### Step 3 Restore the configuration.
Delete the Basic NAT configuration on the RTA.
```cmd
# Delete the NAT address pool.
[RTA]undo nat address-group 1
# Delete the NAT binding under a port.
[RTA]interface GigabitEthernet0/1 
[RTA-GigabitEthernet0/1lundo nat outbound 2000
``

## LabTask2: config NAPT
The private network clients Client_A and Client_B need to access the public network server.
Since the public network addresses are limited, the public network address range
configured on the RTA is 198.76.28.11~198.76.28.11. Configure NAPT on the RTA to
dynamically allocate public network addresses and ports to Client_A and Client_В.
Step 1 Build a test environment.
Build a test environment. See steps 1 and 2 in task 1. 
Ping the server (IP address 198.76.29.4) on Client_A and Client_B, respectively. The
output information is as follows:
Step 2 Check connectivity.
C:\>ping 198.76.29.4
Ping 198.76.29.4 with 32-byte data:
Request timeout.
Request timeout.
Request timeout.mmon Request timeout.
Ping statistics of 198.76.29.4:
Packets: sent = 4, received = 0, lost = 4 (100% lost),
Based on the previous information, Client_A and Client_B cannot ping the server.
Configure NAPT.
# Define flow with the source address located in the network segment 10.0.0.0/24
using ACLS.
Configure NAPT on the RTA.
[RTA-acl-ipv4-basic-2000] rule 0 permit source 10.0.0.0 0.0.0.255
[RTA]acl number 2000
# Configure NAT address pool 1 with one address 198.76.28.11.
[RTA-address-group-1]address 198.76.28.11 198.76.28.11
[RTA] nat address-group 1
Bind the NAT address with acl 2000 in the interface view, and deliver the addresses.
[RTA]interface GigabitEthernet0/1
[RTA-GigabitEthernet0/1] nat outbound 2000 address-group 1
The parameter no-pat is not carried, indicating that the NAT will convert the ports in the
packets.
Ping the server on Client_A and Client_B, respectively. The Client_A and Client_B can ping
the server.
## Lab 13 Configuring NAT
C:\>ping 198.76.29.4 larmou@mai
Ping 198.76.29.4 with 32-byte data:
Reply from 198.76.29.4: byte=32 time=46ms TTL=253
Reply from 198.76.29.4: byte=32 time=1ms TTL=253
Reply from 198.76.29.4: byte=32 time=1ms TTL=253
Reply from 198.76.29.4: byte=32 time=1ms TTL=253
Ping statistics of 198.76.29.4:
Packets: sent = 4, received = 4, lost = 0 (0% lost)
Estimated round trip time (unit in ms):
Shortest = 1ms, longest = 46ms, average = 12ms
Step 2 Check the NAT entries.
Check the NAT entries on the RTA.
[RTA]display nat session verbose mmou@mai
Initiator:
Source IP/port: 10.0.0.1/247
Destination IP/port: 198.76.29.4/2048
DS-Lite tunnel peer: -
VPN instance/VLAN ID/VLL ID:-/-/-
Protocol: ICMP (1)
Responder:
Source IP/port: 198.76.29.4/2
Destination IP/port: 198.76.28.11/0
DS-Lite tunnel peer:
VPN instance/VLAN ID/VLL ID: -1-/-
Protocol: ICMP(1)
State: ICMP REPLY
Application: OTHER
Start time: 2014-11-13 10:19:04 TTL:
Interface (in) : GigabitEthernet0/0
Interface (out): GigabitEthernet0/1
Initiator->Responder:
Responder->Initiator: 
Initiator:
5 packets
Source IP/port: 10.0.0.2/218
Destination IP/port: 198.76.29.4/2048
DS-Lite tunnel peer: -
VPN instance/VLAN ID/VLL ID:-/-/-
Protocol: ICMP(1)
Responder:
Source IP/port: 198.76.29.4/3
Destination IP/port: 198.76.28.11/0
DS-Lite tunnel peer:
VPN instance/VLAN ID/VLL ID: -/-/-
Protocol: ICMP (1)
State: ICMP REPLY
Application: OTHER
420 bytes
Start time: 2014-11-13 10:19:09 TTL: 22s
Interface(in) : GigabitEthernet0/0
Interface (out): GigabitEthernet0/1
Initiator->Responder:
Responder->Initiator:
4 packets
4 packets
336 bytes
336 bytes 
Total sessions found: 2
Based on the previous information, the source IP addresses 10.0.0.1 and 10.0.0.2 are
converted to the same public network address 198.76.28.11. But, the port for 10.0.0.1 is
12289, and the port for 10.0.0.2 is 12288. When the RTA receives reply packets destined to
198.76.28.11, the RTA distinguishes whether to forward the packets to 10.0.0.1 or 10.0.0.2
based on the port specified for conversion. The NAPT uses this method to convert packets
on the IP layer and transport layer, which significantly improves the usage of public
addresses.
Step 3 Restore the configuration.
Delete the NAPT configuration on the RTA.
[RTA]undo nat address-group 1
[RTA]interface GigabitEthernet 0
[RTA-GigabitEthernet0/1]undo nat outbound 2000

### LabTask3: Easy IP
The private network clients Client_A and Client_B need to access the public network server.
Use public network port IP addresses to dynamically allocate public network addresses
and ports to Client_A and Client_B.
Step 1 Build a test environment.
Build a test environment. See steps 1 and 2 in task 1. ammoU
du. mo
Ping the server (IP address 198.76.29.4) on Client_A and Client_B, respectively. The
Client_A and Client_B cannot ping the server.
Step 2 Check connectivity.
Step 3 Configure Easy IP. edu. mo
# Define flow with the source address located in the network segment 10.0.0.0/24
using ACLS.
Configure Easy IP on the RTA.
[RTA-acl-ipv4-basic-20001 rule 0 permit source 10.0.0.0 0.0.0.255
[RTA]acl number 2000
# Bind the acl 2000 with a port and deliver NAT.
[RTA]interface GigabitEthernet0/1
[RTA-GigabitEthernet0/1] nat outbound 2000
Ping the server on Client_A and Client_B, respectively. The Client_A and Client_B can ping
the server.
Step 4 Check connectivity.
Step 5 Check the NAT entries.
Check the NAT entries on the RTA.
[RTA]display nat session verbose
Initiator:
IP/port: 10.0.0.1/255
Destination IP/port: 198.76.29.4/2048
Source
DS-Lite tunnel peer: -
VPN instance/VLAN ID/VLL ID: -/-1-
Protocol: ICMP (1)
Responder:
Source IP/port: 198.76.29.4/2
Destination IP/port: 198.76.28.1/0
DS-Lite tunnel peer:-
VPN instance/VLAN ID/VLL ID: -1-1-
Protocol: ICMP(1)
State: ICMP REPLY 
Application: OTHER
Start time: 2014-11-13 10:24:56 TTL: 15s
Interface(in) : GigabitEthernet0/0
Interface (out): GigabitEthernet0/1
Initiator->Responder: 5 packets
Responder->Initiator: 5 packets
420 bytes
Initiator:
Source IP/port: 10.0.0.2/219
Destination IP/port: 198.76.29.4/2048
DS-Lite tunnel peer: -
VPN instance/VLAN
Protocol: ICMP (1)
Responder:
ID/VLL ID: -/-/-
Source IP/port: 198.76.29.4/3
Destination IP/port: 198.76.28.1/0
DS-Lite tunnel peer: -
VPN instance/VLAN ID/VLL ID: -1-1-
Protocol: ICMP(1)
State: ICMP REPLY
Application: OTHER
Start time: 2014-11-13 10:24:59 TTL: 19
Interface(in) : GigabitEthernet0/0
Interface (out): GigabitEthernet0/1
nOnitiator->Responder: 
5 packets 420 bytes
Responder->Initiator: 5 packets 420 bytes
Total sessions found: 2
<RTA>display nat session brief
There are currently 2 NAT sessions:
Protocol GlobalAddr Port InsideAddr Port DestAddr Port
TCMP 198.76.28.1 12290 10.0.0.1 1024 198.76.29.4 1024
ICMP 198.76.28.1 12289 10.0.0.2 512 198.76.29.4 512
Based on the previous information, the source IP addresses 10.0.0.1 and 10.0.0.2 have
been converted to the outbound port address 198.76.28.1 of the RTA.
After the NAT configuration, if the Client_A can ping the server, can the server ping the
Client_A? The output information is as follows: lammo
C:\ping 10.0.0.1
Ping 10.0.0.1 with 32-byte data:
Request timeout.
Request timeout.
Request timeout.
Request timeout.
Packets: sent = 4, received = 0, lost = 4 (100% lost),
Ping statistics of 10.0.0.1:
The server cannot ping the Client_A.Why? edu. mo
The RTA does not have a route to 10.0.0.0/24, so the server cannot ping the Client_A. The
Client_A can ping the server, because the ICMP reply packet of the server uses the server address 198.76.29.4 as the source address and the RTA outbound address 198.76.28.1 as
the destination address. The actual source address of Client_A is 10.0.0.1. That is, the
ICMP connection must be initiated by the Client_A, triggering the RTA to convert addresses
and forward packets. Note that NAT is effective to the RTA outbound port Eth 0/1, so
sending ICMP packets from the server to ping the client cannot trigger the RTA to convert
addresses. 
To know how to ping the Client_A on the server, go to task 4.
Step 6 Restore the configuration.
Delete the Easy IP configuration on the RTA.
[RTA]undo nat address-group 1
[RTA]interface GigabitEthernet0/1
[RTA-GigabitEthernet0/1]undo nat outbound 2000
### LabTask4: NAT Server
The Client_A needs to provide ICMP services externally. Map the Client_A to the static
public network address 198.76.28.11 and port on the RTA.
Ping the Client_A private network address 10.0.0.1 on the server. The server cannot ping
the Client_A.
Step 1 Check connectivity.
Step 2 Configure NAT Server.
Configure NAT Server on the RTA.
[RTB]interface GigabitEthernet 0/1 
#Implement one-to-one NAT mapping for the private network server address and public
network address on the outbound port.
[RTB-GigabitEthernet0/1]nat server protocol icmp global 198.76.28.11 inside
10.0.0.1
Ping the Client_A public network address 198.76.28.11 on the server. The server can ping
the Client A.
Step 3 Check connectivity.
C:\>ping 198.76.28.11
Pinging 198.76.28.11 with 32 bytes of data: edu. mo
TTI=126
Reply from 198.76.28.11: bytes=32 time=1ms TTL=126
Reply from 198.76.28.11: bytes=32 time=1ms
Reply from 198.76.28.11: bytes=32 time=1ms TTL=126
Reply from 198.76.28.11: bytes=32 time=1ms TTL=126
Ping statistics for 198.76.28.11:
Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
Approximate round trip times in milli-seconds:
Minimum = 1ms, Maximum = 1ms, Average = 1ms
Step 4 Check the NAT entries.
Check the NAT Server entries on the RTА.
[RTA]display nat session verbose
Initiator:
Source IP/port: 198.76.29.4/236
Destination IP/port: 198.76.28.11/2048
DS-Lite tunnel peer: -
VPN instance/VLAN ID/VLL ID: -/-/-
Protocol: ICMP (1)
Responder:
Source IP/port: 10.0.0.1/236
Destination IP/port: 198.76.29.4/0
DS-Lite tunnel peer:
VPN instance/VLAN ID/VLL ID: -/-/-
Protocol: ICMP(1)
State: ICMP REPLY
Application: OTHER
Start time: 2014-11-13 10:31:45 TTL: 26s
Interface(in) : GigabitEthernet0/1 
Interface (out): GigabitEthernet0/0
Initiator->Responder: 5 packets
Responder->Initiator: 5 packets
420 bytes
420 bytes
Total sessions found: 1
[RTA] display nat server
Server in private network information:
There are currently 1 internal servers
Interface: GigabitEthernet0/1, Protocol:1(icmp),
[global] 198.76.28.11: ---- [local]10.0.0.1:
Based on the previous information, the public network address maps to the private network
address one by one.
Step 5 Restore the configuration.
Delete the NAT Server configuration on the RTA.
[RTA]interface GigabitEthernet0/1
[RTA-GigabitEthernet0/1]undo nat server protocol icmp global 198.76.28.11
The NAT Server is to meet the requirement of a public network client to access a private
network server. The NAT Server maps the private network address/port to a static public
network address/port for the public network client to access. In practical application, if the
web server or FTP server in a private network needs to provide services for public network
customers, the NAT Server can be used to map a public network address to a private
network server. If the Client_A pings the server, can the ping be successfully? If the
Client_B pings the server, can the ping be successfully as well? amm
Based on the NAT Server configuration command on the RTA, if the Client_A is an FTP
server, can it provide FTP services externally? The answer is yes. Modify the NAT Server
configuration. The NAT Server configuration is as follows:
[RTB-GigabitEthernet0/1]nat server protocol tcp global 198.76.28.11 ftp inside
10.0.0.1 ftp
[RTB]interface GigabitEthernet 0/1
```

## command reference
![](https://github.com/eddylin2015/H3C-CM446-10-2025-C/blob/main/img/lab13commandreference.png?raw=true)




