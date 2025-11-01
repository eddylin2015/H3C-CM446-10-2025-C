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

根据图 13-1 搭建测试环境，在 RTA 和 RTB 的端口上配置 IP 地址。为了对去
往 Server 的数据包提供路由,在私网出口路由器 RTA 上需要配置一条静态路由,指向公网路
由器 RTB, 下一跳为RTB 的接口 G0/0。这时RTA 应该能 ping 通 Server。
- 配置主机 Client_A的IP地址为10.0.0.1/24,网关为 10.0.0.254;
- 配置主机 Client B的IP地址为10.0.0.2/24,网关为 10.0.0.254。

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
结果显示，Client_A 和 Client_B 无法 ping 通服务器。因为 RTB 没有私网路由，所以对于从服务器发送的 ping 数据包，RTB 无法找到目的地址为网段 10.0.0.0 的路由。

### 配置基本 NAT

在 RTA 上配置 Basic NAT:

通过 acl 定义一条源地址属于 10.0.0.0/24 网段的流。
```cmd
[RTA]acl number 2000
[RTA-acl-ipv4-basic-2000]rule 0 permit source 10.0.0.0 0.0.0.255
```
配置 NAT 地址池 1,地址池中的用于地址转换的地址从198.76.28.11 到198.76.28.20 共10个。
```cmd
[RTA]nat address-group 1
[RTA-address-group-1]address 198.76.28.11 198.76.28.20
```
进入接口视图。
```cmd
[RTA]interface GigabitEthernet 0/1
```
将NAT地址池 1 与 ACL 2000 关联，,并在接口下发,方向为出方向。
```cmd
[RTA-GigabitEthernet0/1]nat outbound 2000 address-group 1 no-pat
```
由配置可见,在RTA 上配置了公网地址池 address-group 1, 地址范围为198.76.28.11~
198.76.28.20。参数 no-pat 表示使用一对一的地址转换,只转换数据包的地址而不转换端口信
息。此时路由器 RTA 会对该接口上出方向并且匹配 acl 2000 的流量做地址转换。

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

Total entries found:2
```
从显示信息中可以看出,该 ICMP 报文的源地址 10.0.0.1 已经转换成公网地址
198.76.28.12,源端口号为249,目的端口号为2048。源地址 10.0.0.2 已经转换成公网地址
198.76.28.11,源端口号为210,目的端口号为2048。一分钟以后再次观察此表项,发现表中
后两项消失了,四分钟以后再次观察,发现表项全部消失,显示如下:
```cmd
<RTA>display nat session
Total sessions found: 0
```
The NAT entries have aging time (aging-time). After the aging time expires. 

这是因为 NAT 表项具有一定的老化时间(aging-time),一旦超过老化时间,NAT 会删除
表项。可以通过命令 display session aging-time state 查看路由器会话的默认老化时间:

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

虽然理论上每个IP地址有65535个端口,除去协议已占用和保留端口外,实际可用于地
址转换的端口远少于理论值。

### Step 3 Restore the configuration.
Delete the Basic NAT configuration on the RTA.
```cmd
# Delete the NAT address pool.
[RTA]undo nat address-group 1
# Delete the NAT binding under a port.
[RTA]interface GigabitEthernet0/1 
[RTA-GigabitEthernet0/1lundo nat outbound 2000
```

## LabTask2: config NAPT


私网客户端 Client A、Client B需要访问公网服务器 Server,但由于公网地址有限,在
RTA 上配置的公网地址池范围为 198.76.28.11~198.76.28.11,因此配置 NAPT,动态地为
Client_A、Client_B 分配公网地址和协议端口。

The private network clients Client_A and Client_B need to access the public network server.

Since the public network addresses are limited, the public network address range
configured on the RTA is 198.76.28.11~198.76.28.11.

Configure NAPT on the RTA to dynamically allocate public network addresses and ports to Client_A and Client_В.

### Step 1 Build a test environment.
Build a test environment. See steps 1 and 2 in task 1. 

Ping the server (IP address 198.76.29.4) on Client_A and Client_B, respectively. The
output information is as follows:
### Step 2 Check connectivity.
```cmd
C:\>ping 198.76.29.4
Ping 198.76.29.4 with 32-byte data:
Request timeout.
Request timeout.
Request timeout.mmon Request timeout.
Ping statistics of 198.76.29.4:
Packets: sent = 4, received = 0, lost = 4 (100% lost),
```
Based on the previous information, Client_A and Client_B cannot ping the server.

### Step 3 Configure NAPT.

Define flow with the source address located in the network segment 10.0.0.0/24
using ACLS.

Configure NAPT on the RTA
```cmd
[RTA-acl-ipv4-basic-2000] rule 0 permit source 10.0.0.0 0.0.0.255
[RTA]acl number 2000
```

Configure NAT address pool 1 with one address 198.76.28.11.
```cmd
[RTA-address-group-1]address 198.76.28.11 198.76.28.11
[RTA] nat address-group 1
```

Bind the NAT address with acl 2000 in the interface view, and deliver the addresses.
```cmd
[RTA]interface GigabitEthernet0/1
[RTA-GigabitEthernet0/1] nat outbound 2000 address-group 1
```
此时未携带 no-pat关键字,意味着 NAT 要对数据包进行端口的转换.
The parameter no-pat is not carried, indicating that the NAT will convert the ports in the
packets.

### 步骤四:检查连通性
Ping the server on Client_A and Client_B, respectively. The Client_A and Client_B can ping
the server.

```cmd
C:\>ping 198.76.29.4 
Ping 198.76.29.4 with 32-byte data:
Reply from 198.76.29.4: byte=32 time=46ms TTL=253
Reply from 198.76.29.4: byte=32 time=1ms TTL=253
Reply from 198.76.29.4: byte=32 time=1ms TTL=253
Reply from 198.76.29.4: byte=32 time=1ms TTL=253
Ping statistics of 198.76.29.4:
Packets: sent = 4, received = 4, lost = 0 (0% lost)
Estimated round trip time (unit in ms):
Shortest = 1ms, longest = 46ms, average = 12ms
```
## Step 5 Check the NAT entries.
Check the NAT entries on the RTA.
```cmd
[RTA]display nat session verbose 
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
Start time: 2014-11-13 10:19:04 TTL:15s
Interface (in) : GigabitEthernet0/0
Interface (out): GigabitEthernet0/1
Initiator->Responder: 5 packets 420 packets
Responder->Initiator: 5 packets 420 packets
Initiator:
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
Start time: 2014-11-13 10:19:09 TTL: 22s
Interface(in) : GigabitEthernet0/0
Interface (out): GigabitEthernet0/1
Initiator->Responder: 4 packets 336 bytes
Responder->Initiator: 4 packets 336 bytes

Total sessions found: 2
```
Based on the previous information, the source IP addresses 10.0.0.1 and 10.0.0.2 are
converted to the same public network address 198.76.28.11. But, the port for 10.0.0.1 is
12289, and the port for 10.0.0.2 is 12288. When the RTA receives reply packets destined to
198.76.28.11, the RTA distinguishes whether to forward the packets to 10.0.0.1 or 10.0.0.2
based on the port specified for conversion. The NAPT uses this method to convert packets
on the IP layer and transport layer, which significantly improves the usage of public
addresses.

### Step 3 Restore the configuration.
Delete the NAPT configuration on the RTA.
```cmd
[RTA]undo nat address-group 1
[RTA]interface GigabitEthernet 0
[RTA-GigabitEthernet0/1]undo nat outbound 2000
```
### LabTask3: Easy IP

私网客户端 Client_A、Client_B 需要访问公网服务器 Server,使用公网接口 IP地址动态
为 Client_A、Client_B分配公网地址和协议端口。

The private network clients Client_A and Client_B need to access the public network server.
Use public network port IP addresses to dynamically allocate public network addresses
and ports to Client_A and Client_B.

### Step 1 Build a test environment.

Build a test environment. See steps 1 and 2 in task 1. 

### Step 2 Check connectivity.
Ping the server (IP address 198.76.29.4) on Client_A and Client_B, respectively. The
Client_A and Client_B cannot ping the server.

### Step 3 Configure Easy IP. 
通过 acl 定义一条源地址属于10.0.0.0/24 网段的流。
Define flow with the source address located in the network segment 10.0.0.0/24 using ACLS.
Configure Easy IP on the RTA.
```cmd
[RTA-acl-ipv4-basic-20001 rule 0 permit source 10.0.0.0 0.0.0.255
[RTA]acl number 2000
```
Bind the acl 2000 with a port and deliver NAT.
在接口视图下将 acl 2000与接口关联下发 NAT。
```cmd
[RTA]interface GigabitEthernet0/1
[RTA-GigabitEthernet0/1] nat outbound 2000
```
Ping the server on Client_A and Client_B, respectively. The Client_A and Client_B can ping
the server.
### Step 4 Check connectivity.
从 Client_A、Client_B 分别 ping Serv,OK
### Step 5 Check the NAT entries.
Check the NAT entries on the RTA.
```cmd
[RTA]display nat session verbose
Initiator:
Source IP/port: 10.0.0.1/255
Destination IP/port: 198.76.29.4/2048
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
Initiator->Responder: 5 packets 420 bytes
Responder->Initiator: 5 packets 420 bytes

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
itiator->Responder: 5 packets 420 bytes
Responder->Initiator: 5 packets 420 bytes
Total sessions found: 2
<RTA>display nat session brief
There are currently 2 NAT sessions:
Protocol GlobalAddr Port InsideAddr Port DestAddr Port
TCMP 198.76.28.1 12290 10.0.0.1 1024 198.76.29.4 1024
ICMP 198.76.28.1 12289 10.0.0.2 512 198.76.29.4 512
```
从显示信息中可以看到,源地址 10.0.0.1 和 10.0.0.2 都转换为RTA 的出接口地址
198.76.28.1.
Based on the previous information, the source IP addresses 10.0.0.1 and 10.0.0.2 have
been converted to the outbound port address 198.76.28.1 of the RTA.

请思考一个问题:在步骤四中,完成 NAT 配置后,从 Client_A 能够 ping 通 Server,
如果从 Server 端 ping Client_A呢?ping命令结果显示如下:
After the NAT configuration, if the Client_A can ping the server, can the server ping the
Client_A? The output information is as follows: 
```cmd
C:\ping 10.0.0.1
Ping 10.0.0.1 with 32-byte data:
Request timeout.
Request timeout.
Request timeout.
Request timeout.
Packets: sent = 4, received = 0, lost = 4 (100% lost),
Ping statistics of 10.0.0.1:
```
The server cannot ping the Client_A.Why? 

The RTA does not have a route to 10.0.0.0/24, so the server cannot ping the Client_A. The
Client_A can ping the server, because the ICMP reply packet of the server uses the server address 198.76.29.4 as the source address and the RTA outbound address 198.76.28.1 as
the destination address. The actual source address of Client_A is 10.0.0.1. That is, the
ICMP connection must be initiated by the Client_A, triggering the RTA to convert addresses
and forward packets. Note that NAT is effective to the RTA outbound port Eth 0/1, so
sending ICMP packets from the server to ping the client cannot trigger the RTA to convert
addresses. 
To know how to ping the Client_A on the server, go to task 4.
仔细思考,不难发现在 RTA 上始终没有 10.0.0.0/24 网段的路由,所以 Server 直接 ping
Client_A是不可达的。而 Client_A能 ping 通 Server 是因为,由Server 回应的ICMP 回程报
文源地址是 Server 的地址 198.76.29.4,但是目的地址是RTA 的出接口地址 198.76.28.1,而
不是 Client_A的实际源地址 10.0.0.1。也就是说这个ICMP连接必须是由 Client端来发起连接,
触发 RTA 做地址转换后转发。还记得我们在RTA 出接口 Eth 0/1 下发 NAT 配置时的那个
outbound 吗?NAT 操作是在出方向使能有效。所以,如果从 Server 端始发 ICMP 报文 ping
Client 端,是无法触发 RTA 做地址转换的。

那么,要想让 Server 端能够 ping 通 Client_A,应该怎么做呢?在实验任务四中,可以找
到答案。

### Step 6 Restore the configuration.
Delete the Easy IP configuration on the RTA.
```cmd
[RTA]undo nat address-group 1
[RTA]interface GigabitEthernet0/1
[RTA-GigabitEthernet0/1]undo nat outbound 2000
```

### LabTask4任务四: NAT Server

Client_A 需要对外提供ICMP服务,在RTA上为 Client_A静态映射公网地址和协议端口,
公网地址为198.76.28.11。

The Client_A needs to provide ICMP services externally. Map the Client_A to the static
public network address 198.76.28.11 and port on the RTA.

### Step 1 Check connectivity.
Ping the Client_A private network address 10.0.0.1 on the server. The server cannot ping
the Client_A.

### Step 2 Configure NAT Server.
Configure NAT Server on the RTA.
```cmd
[RTB]interface GigabitEthernet 0/1
```
Implement one-to-one NAT mapping for the private network server address and public
network address on the outbound port.
```cmd
[RTB-GigabitEthernet0/1]nat server protocol icmp global 198.76.28.11 inside
10.0.0.1
```
### Step 3 Check connectivity.
Ping the Client_A public network address 198.76.28.11 on the server. The server can ping
the Client A.
```cmd
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
```
### Step 4 Check the NAT entries.
Check the NAT Server entries on the RTА.
```cmd
[RTA]disp nat session verbose
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
Initiator->Responder: 5 packets 420 bytes
Responder->Initiator: 5 packets 420 bytes

Total sessions found: 1

[RTA] display nat server
Server in private network information:
There are currently 1 internal servers
Interface: GigabitEthernet0/1, Protocol:1(icmp),
[global] 198.76.28.11: ---- [local]10.0.0.1:
```
表项信息中显示出公网地址和私网地址的一对一的映射关系。

Based on the previous information, the public network address maps to the private network
address one by one.
### Step 5 Restore the configuration.
Delete the NAT Server configuration on the RTA.
```cmd
[RTA]interface GigabitEthernet0/1
[RTA-GigabitEthernet0/1]undo nat server protocol icmp global 198.76.28.11
```
NAT Server 特性就是为了满足公网客户端访问私网内部服务器的需求,将私网地址/端口
静态映射成公网地址/端口,以供公网客户端访问。比如在实际应用中,客户的私有网络中的一
台 WEB或FTP服务器需要对公网客户提供服务,这时需要使用 NAT Server 特性对外映射一
个公网地址给自己的私网服务器。请思考,这时如果 Client_A 主动 ping Server 能否ping 通?


按照上面 RTA 中的NAT Server 的配置命令,如果 Client_A 是一台 FTP服务器,能否对
外提供 FTP服务?当然可以,只要修改 NAT Server 的相关配置。NAT Server 相关配置如下所
Client_B 能否 ping 通 Server?为什么?
示:

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
```cmd
[RTB]interface GigabitEthernet 0/1
[RTB-GigabitEthernet0/1]nat server protocol tcp global 198.76.28.11 ftp inside
10.0.0.1 ftp

```

## command reference
![](https://github.com/eddylin2015/H3C-CM446-10-2025-C/blob/main/img/lab13commandreference.png?raw=true)







