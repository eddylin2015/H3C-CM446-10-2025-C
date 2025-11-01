# Lab12_ACL_BasedPacketFiltering 包過濾
- principle of ACL
- basic config method of ACL
- config commands of ACL

## Lab Diagrams

![](https://github.com/eddylin2015/H3C-CM446-10-2025-C/blob/main/img/lab12labDiagram.png?raw=true)

## Lab Procedures
### 实验任务一:配置基本 ACL
 
 本实验任务主要是通过在路由器上实施基本 ACL 来禁止 PCA 访问本网段外的网络,使学
员熟悉基本ACL 的配置和作用。

网络拓扑
### 步骤一:建立物理连接

### 步骤二:配置 IP地址及路由
```cmd
[RTA:GE0/0:]ip addr 192.168.0.1/24 (连接ClientA)
[RTA:GE0/1:]ip addr 192.168.1.1/24 (连接RTB)

[RTB:GE0/0:]ip addr 192.168.2.1/24 (连接ClientB)
[RTB:GE0/1:]ip addr 192.168.1.2/24 (连接RTA) 

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
配置完成后,请在PCA 上通过 ping 命令来验证 PCA与路由器、PCA与PCB 之间的可达
性。应该是可达,如下:
```cmd
C:\Documents and Settings\Administrator>ping 192.168.2.2

正在 Ping 192.168.2.2 具有32 字节的数据:
来自 192.168.2.2 的回复:字节=32 时间=20ms TTL=253
```
### 步骤三:ACL 应用规划
本实验的目的是使PCA 不能访问本网段外的网络。请学员考虑如何在网络中应用ACL包
过滤的相关问题:

- 需要使用何种ACL?
- ACL 规则的动作是deny 还是 permit?
- ACL 规则中的反掩码应该是什么?
- ACL 包过滤应该应用在路由器的哪个接口的哪个方向上?
下面是有关 ACL 规划的答案:

- 仅使用源 IP地址就能够识别 PCA发出的数据报文,因此使用基本ACL 即可;
- 目的是要使 PCA 不能访问本网段外的网络,因此ACL 规则的动作是deny:
- 只需要限制从单台 PC 发出的报文,因此反掩码设置为0.0.0.0;
- 因为需要禁止PCA访问本网
### 步骤四: 配置基本 ACL 并应用
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

### 步骤五:验证防火墙作用

验证结果分析连通性测试
```cmd
C:\> ping 192.168.2.2
Request timeout. (100% lost)
```
PCA (192.168.0.2) 无法ping通 PCB (192.168.2.2)

证明ACL规则生效

统计信息验证ACL匹配统计
```cmd
[RTA] display acl 2001
rule 0 deny source 192.168.0.2 0 (8 times matched)
```
规则已匹配8次（对应ping测试的4个请求+4个回复被拒绝）

包过滤统计
```cmd
[RTA] display packet-filter statistics sum inbound
Interface: GigabitEthernet0/0
Tammou
In-bound policy:
IPv4 ACL 2001
IPv4 default action: Permit

[RTA] display packet-filter statistics sum inbound 2001
Sum: In-bound IPV4 ACL poicy:S 2001
rule 0 deny source 192.168.0.2 0 (429 packets)
Totally 0 packets permitted, 429 packets denied
Totally 0% permitted, 100% denied
```
可以看到,路由器启用了包过滤防火墙功能,使用ACL 2001 来匹配进入接口 GE0/0 的报
文,过滤方向是inbound.

## 实验任务二:配置高级 ACL

本实验任务是通过在路由器上实施高级ACL来禁止从 PCA到网络 192.168.2.0/24 的FTP
数据流,使学员熟悉高级 ACL 的配置和作用。
### 步骤一:ACL 应用规划 
本实验的目的是禁止从 PCA 到网络 192.168.2.0/24 的FTP 数据流,但允许其它数据流通
过。请学员考虑如何在网络中应用 ACL 包过滤的相关问题:
- 需要使用何种ACL?
- ACL 规则的动作是deny 还是 permit?
- ACL 规则中的反掩码应该是什么?
- ACL 包过滤应该应用在路由器的哪个接口的哪个方向上?
下面是有关 ACL 规划的答案:
- 本实验目的是要禁止从 PCA 到网络 192.168.2.0/24 的FTP 数据流。需要使用协议端口号来识别 PCA 发出的FTP 数据报文,因此必须使用高级ACL:
- 本实验目的是要使PC之间不可达,因此ACL 规则的动作是deny:
- 本实验中只需要限制从单台 PC发出的到网络 192.168.2.0/24 的报文,
- 因此需要设置源 IP地址反掩码为0.0.0.0,目的IP反掩码为0.0.0.255:
- 因为需要禁止PCA发出的数据,所以可以在RTA连接PCA的接口 G0/0上应用ACL,方向为inbound

### 步骤二:配置高级 ACL 并应用

在路由器 RTA 上定义ACL 如下:
```cmd
[RTA]acl advanced 3002
[RTA-acl-ipv4-adv-3002] rule deny tcp source 192.168.0.2 0.0.0.0 destination
192.168.2.1 0.0.0.255 destination-port eg ftp
[RTA-acl-ipv4-adv-3002] rule permit ip source 192.168.0.2 0.0.0.0 destination
192.168.2.0 0.0.0.255
```
RTA 上包过滤防火墙功能默认开启,默认动作为permit。
在RTA 的 GigabitEthernet0/0 上应用 ACL:
```cmd
[RTA-GigabitEthernet0/0]packet-filter 3002 inbound
```

### 步骤三:验证防火墙作用

在PCA 上使用 ping 命令来测试从 PCA到PCB的可达性。结果应该是可达,如下:
步骤三:验证防火墙作用

```cmd
C:\>ping 192.168.2.2
正在 Ping 192.168.2.2 具有 32 字节的数据:
来自 192.168.2.2 的回复:字节=32 时间=27ms TTL=253
来自 192.168.2.2 的回复:字节=32 时间=1ms TTL-253
```

在PCB 上开启 FTP 服务,然后在PCA 上使用FTP客户端软件连接到PCB。结果应该是
FTP 未连接,如下: 
```cmd
C:\>ftp 192.168.2.2
ftp> dir
未连接。
```
同时,在RTA 上可以通过命令行来查看 ACL 及防火墙的状态和统计:
```cmd
[RTA]display acl 3002
Advanced IPv4 ACL 3002, 2 rules,
ACL's step is 5
rule 0 deny tcp source 192.168.0.2 0 destination 192.168.2.0 0.0.0.255
destination-port eq ftp (12 times matched)
rule 5 permit ip source 192.168.0.2 0 destination 192.168.2.0 0.0.0.255 (2 times
matched)
```
可以看到,分别有数据报文命中了ACL 3002 的两个规则。
```cmd
[RTA]display packet-filter interface inbound
Interface: GigabitEthernet0/0
In-bound policy:

[RTA] display packet-filter statistics sum inbound 3002
IPv4 ACL 3002
IPv4 default action: Permit
rule 0 deny tcp source 192.168.0.2 0 destination 192.168.2.0 0.0.0.255
destination-port eq ftp (9 packets)
Sum:
In-bound policy:
IPv4 ACL 3002
rule 5 permit ip source 192.168.0.2 0 destination 192.168.2.0 0.0.0.255
Totally 0 packets permitted, 9 packets denied
Totally 0% permitted, 100% denied
```
可以看到,路由器启用了包过滤防火墙功能,使用ACL 3002 来匹配进入接口 GE0/0的报
过滤方向是 inbound。



## command reference
![](https://github.com/eddylin2015/H3C-CM446-10-2025-C/blob/main/img/lab12commandreference.png?raw=true)

