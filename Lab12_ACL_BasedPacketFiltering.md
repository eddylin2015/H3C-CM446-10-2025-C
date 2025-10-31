# Lab12_ACL_BasedPacketFiltering 包過濾
- principle of ACL
- basic config method of ACL
- config commands of ACL

## Lab Diagrams

![](https://github.com/eddylin2015/H3C-CM446-10-2025-C/blob/main/img/lab12labDiagram.png?raw=true)

## Lab Procedures
网络拓扑
```cmd
[RTA:GE0/0:]ip addr 192.168.0.1/24 (连接ClientA)
[RTA:GE0/1:]ip addr 192.168.1.1/24 (连接RTB)

[RTB:GE0/0:]ip addr 192.168.2.1/24 (连接ClientB)
[RTB:GE0/1:]ip addr 192.168.1.2/24 (连接RTA) 【修正：原文配置有误】

配置修正
RTA配置 (正确):
```cmd
[RTA] ospf
[RTA-ospf-1] area 0
[RTA-ospf-1-area-0.0.0.0] network 192.168.0.0 0.0.0.255
[RTA-ospf-1-area-0.0.0.0] network 192.168.1.0 0.0.0.255
```
RTB配置 (需要修正):
```cmd
[RTB] ospf
[RTB-ospf-1] area 0
[RTB-ospf-1-area-0.0.0.0] network 192.168.1.0 0.0.0.255
[RTB-ospf-1-area-0.0.0.0] network 192.168.2.0 0.0.0.255
```
1. ACL规则定义
```cmd
[RTA] acl number 2001
[RTA-acl-ipv4-basic-2001] rule deny source 192.168.0.2 0.0.0.0
```
ACL 2001: 基本IPv4 ACL
规则: 拒绝源IP地址为 192.168.0.2 的所有数据包
通配符掩码: 0.0.0.0 表示精确匹配该IP地址

2. 接口应用
```cmd
[RTA-GigabitEthernet0/0] packet-filter 2001 inbound
```
在GE0/0接口的入方向应用ACL 2001

默认动作为permit（允许）

验证结果分析
连通性测试
```cmd
C:\> ping 192.168.2.2
Request timeout. (100% lost)
```
PCA (192.168.0.2) 无法ping通 PCB (192.168.2.2)

证明ACL规则生效

统计信息验证
ACL匹配统计
```cmd
[RTA] display acl 2001
rule 0 deny source 192.168.0.2 0 (8 times matched)
```
规则已匹配8次（对应ping测试的4个请求+4个回复被拒绝）

包过滤统计
```cmd
[RTA] display packet-filter statistics sum inbound 2001
rule 0 deny source 192.168.0.2 0 (429 packets)
Totally 0 packets permitted, 429 packets denied
Totally 0% permitted, 100% denied
```
429个数据包被拒绝

0个数据包被允许

拒绝率100%

工作原理
数据流路径
PCA (192.168.0.2) 发送ping包到 PCB (192.168.2.2)

数据包到达 RTA GE0/0接口（入方向）

包过滤器检查ACL 2001：

源IP 192.168.0.2 匹配rule 0

执行deny动作

数据包被丢弃，无法转发

默认行为
默认动作为 permit

只有匹配ACL规则的数据包才会被拒绝

其他所有流量正常通过

网络拓扑推断
基于配置可以推断：

PCA: 192.168.0.2/24 (连接RTA GE0/0)

RTA GE0/0: 192.168.0.1/24

RTA GE0/1: 192.168.1.1/24 (连接RTB)

RTB: 连接PCB (192.168.2.2/24)

### advance
高级ACL（扩展ACL）配置示例，实现了更精细的流量控制。以下是详细分析：
1. 高级ACL规则定义
```cmd
[RTA] acl number 3002
[RTA-acl-ipv4-adv-3002] rule deny tcp source 192.168.0.2 0.0.0.0 destination 192.168.2.1 0.0.0.255 destination-port eq ftp
[RTA-acl-adv-3002] rule permit ip source 192.168.0.2 0.0.0.0 destination 192.168.2.0 0.0.0.255
```
- 规则0: 拒绝PCA (192.168.0.2) 到PCB网络 (192.168.2.0/24) 的FTP流量
- 规则5: 允许PCA (192.168.0.2) 到PCB网络 (192.168.2.0/24) 的所有其他IP流量

2. 接口应用
```cmd
[RTA-GigabitEthernet0/0] packet-filter 3002 inbound
```
在GE0/0接口入方向应用ACL 3002

验证结果分析
连通性测试Ping测试 - 成功
```cmd
C:\> ping 192.168.2.2
Reply from 192.168.2.2: byte=32 time=1ms TTL=253
Packets: sent = 4, received = 4, lost = 0 (0% lost)
```
ICMP流量正常通过（匹配规则5的permit ip）

FTP测试 - 失败
```cmd
C:\> ftp 192.168.2.2
ftp> dir
Unconnected.
```
FTP连接被阻断（匹配规则0的deny tcp）

统计信息验证
ACL匹配统计
```cmd
[RTA] display acl 3002
rule 0 deny tcp ... (12 times matched)
rule 5 permit ip ... (2 times matched)
```
包过滤统计
```cmd
[RTA] display packet-filter statistics sum inbound 3002
rule 0 deny tcp ... (9 packets)
Totally 0 packets permitted, 9 packets denied
Totally 0% permitted, 100% denied
```
关键技术点
1. 高级ACL特性
- 协议特定: 可以基于TCP/UDP等传输层协议过滤
- 端口控制: 可以针对特定服务端口（如FTP-21）进行控制
- 精细控制: 同时允许其他类型的通信

2. 规则设计逻辑
```cmd
规则0: deny tcp 192.168.0.2 → 192.168.2.0/24 ftp    # 阻断FTP
规则5: permit ip 192.168.0.2 → 192.168.2.0/24       # 允许其他所有IP流量
```
3. 匹配顺序
ACL规则按配置顺序匹配（规则0 → 规则5）
一旦匹配就执行相应动作，不再继续匹配

## command reference
![](https://github.com/eddylin2015/H3C-CM446-10-2025-C/blob/main/img/lab12commandreference.png?raw=true)
