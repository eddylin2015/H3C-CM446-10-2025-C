# Lab10_IP_ROUTING_BASICS 

## labDiagram


![](https://github.com/eddylin2015/H3C-CM446-10-2025-C/blob/main/img/lab10_labDiagram.png?raw=true)

lab10_labDiagram.png

## Task1实验任务一:查看路由表
  本实验主要是通过在路由器上通过查看路由表,观察路由表中路由项。通过本次实验,学
员能够掌握如何使用命令来查看路由表,及了解路由项中要素的含义。

表10-2 IP地址列表
- 设备名称 接口 IP地址 网关
- RTA G0/1 192.168.1.1/24 --
- RTA G0/0 192.168.0.1/24 --
- RTB G0/1 192.168.1.2/24 --
- RTB G0/0 192.168.2.1/24 --
- PCA ---- 192.168.0.2/24 192.168.0.1
- PCB ---- 192.168.2.2/24 192.168.2.1

### 步骤二:在路由器上查看路由表
```cmd
[RTA]disp ip routing-table
Destinations : 8 Routes : 8
Destination/Mask Proto Pre Cost NextHop Interface
0.0.0.0/32 Direct 0 이 127.0.0.1 InLoop
127.0.0.0/8 Direct 이 이 127.0.0.1 InLoop0
127.0.0.0/32 Direct 00 127.0.0.1 InLoop0
127.0.0.1/32 Direct 0 0 127.0.0.1 InLoopO
127.255.255.255/32 Direct 0 0 127.0.0.1 InLoop0
224.0.0.0/4 Direct 00 0.0.0.0 NULLO
224.0.0.0/24 Direct 00 0.0.0.0 NULLO
255.255.255.255/32 Direct 00 127.0.0.1 InLoop0
```
由以上输出可知,目前路由器有8条路由,其中目的地址是127.0.0.0 的路由,是路由器的环回地址直连路由。

配置 RTA:
```cmd
[RTA-G0/0]ip address 192.168.0.1 24
[RTA-G0/2]ip address 192.168.1.1 24
```
配置 RTB:
```cmd
[RTB-G0/0]ip address 192.168.2.1 24
[RTB-G0/2]ip address 192.168.1.2 24
```
在RTA 上查看路由表,
```cmd
[RTA]disp ip routing-table
Destinations : 17 Routes: 17
Destination/Mask Proto Pre Cost NextHoр Interface
0.0.0.0/32 Direct 이 127.0.0.1 InLoop0
127.0.0.0/8 Direct Ο 0 127.0.0.1 InLoop0
127.0.0.0/32 Direct 0 이 127.0.0.1 InLoop0
127.0.0.1/32 Direct 이 이 127.0.0.1 InLoop0
127.255.255.255/32 Direct 이 이 127.0.0.1 InLoopo
192.168.0.0/24 Direct0 0 192.168.0.1 GEO/0
192.168.0.0/32 Direct0 이 192.168.0.1 GE0/0
192.168.0.1/32 Direct 0 0 127.0.0.1 InLoop0
192.168.0.255/32 Direct 0 이 192.168.0.1 GE0/0
192.168.1.0/24 Direct 0 0 192.168.1.1 GE0/1
192.168.1.0/32 Direct 0 0 192.168.1.1 GE0/1
192.168.1.1/32 Direct 0 이 127.0.0.1 InLoop0
192.168.1.2/32 Direct 0 이 192.168.1.2 GE0/1
192.168.1.255/32 Direct 0 이 192.168.1.1 GE0/1
224.0.0.0/4 Direct 이 0.0.0.0 NULLO
224.0.0.0/24 Direct 0 0.0.0.0 NULLO
255.255.255.255/32 Direct 0 02 127.0.0.1 InLoop0
```
由以上输出可知,在RTA 上配置了 IP地址 192.168.0.1 和 192.168.1.1 以及在RTB 上配
置 192.168.1.2 后,RTA 的路由表中有了直连路由192.168.0.0/24,192.168.0.1/32,
192.168.1.0/24, 192.168.1.1/32, 192.168.1.2/32。这其中,192.168.0.1/32, 192.168.1.1/32,
192.168.1.2/32 是主机路由,192.168.0.0/24,192.168.1.0/24 是子网路由。直连路由是由链路
层协议发现的路由,链路层协议 UP 后,路由器会将其加入路由表中。如果我们关闭链路层协
议,则相关直连路由也消失。

在RTA 上关闭接口,如下:

```cmd
[RTA G0/0]shutdown
[RTA]disp ip routing-table

Destinations : 13 Routes : 13
Destination/Mask Proto Pre Cost NextHoр Interface
0.0.0.0/32 Direct 이 127.0.0.1 InLoop0
127.0.0.0/8 Direct 0 0 127.0.0.1 InLoop0
127.0.0.0/32 Direct 0 이 127.0.0.1
127.0.0.1/32 Direct 0 이 127.0.0.1 InLoopo
127.255.255.255/32 Direct 0 이 127.0.0.1 InLoop0
192.168.1.0/24 Direct0 0 192.168.1.1 GE0/1
192.168.1.0/32 Direct 0 0 192.168.1.1 GE0/1
192.168.1.1/32 Direct 0 0 127.0.0.1 InLoop0
192.168.1.2/32 Direct 0 이 192.168.1.2 GE0/1
192.168.1.255/32 Direct 0 0 192.168.1.1 GE0/1
224.0.0.0/4 Direct 0.0.0.0 NULLO
224.0.0.0/24 Direct 00 0.0.0.0 NULLO
255.255.255.255/32 Direct 0 이 127.0.0.1 InLoop0

[RTA-G0/0]undo shutdown
```

## Task2实验任务二:静态路由配置

本实验主要是通过在路由器上配置静态路由,从而达到PC 之间能够互访的目的。通过本
次实验,学员能够掌握静态路由的配置,加深对路由环路产生原因的理解。

### step1步骤一:在PC 配置 IP 地址

按表 10-2所示在PC上配置 IP地址和网关。配置完成后,在Windows 操作系统的【开始】
里选择【运行】,在弹出的窗口里输入 CMD,然后在【命令提示符】下用 ipconfig 命令来查看
所配置的IP地址和网关是否正确。

在PC 上用 Ping 命令来测试到网关的可达性。例如,在PCA 上测试到网关(192.168.0.1)
的可达性,如下所示:
```cmd
>ping 192.168.0.1
Pinging 192.168.0.1 with 32 bytes of data:
Reply from 192.168.0.1: bytes=32 time<1ms TTL=255
Reply from 192.168.0.1: bytes=32 time<1ms TTL=255
```
再测试 PC 之间的可达性。例如,在 PCA 上用 Ping 命令测试到PCB 的可达性,如下:
```cmd
C:\>ping 192.168.2.2
Pinging 192.168.2.2 with 32 bytes of data:
Reply from 192.168.0.1: Destination net unreachable.
Reply from 192.168.0.1: Destination net unreachable.
```
以上输出信息显示,RTA (192.168.0.1) 返回了目的网络不可达的信息给 PCA,说明 RTA
没有到达 PCB (192.168.2.2)的路由。

```cmd
[RTA]disp ip routing-table
Destinations : 13 Routes : 13
Destination/Mask Proto Pre Cost NextHop Interface
0.0.0.0/32 Direct 0 이 127.0.0.1 InLoop0
127.0.0.0/8 Direct 이 이 127.0.0.1 InLoop0
127.0.0.0/32 Direct 이 이 127.0.0.1 InLoop0
127.0.0.1/32 Direct 00 127.0.0.1 InLoop0
127.255.255.255/32 Direct 0 0 127.0.0.1 InLoop0
192.168.1.0/24 Direct 0 0 192.168.1.1 GE0/1
192.168.1.0/32 Direct 이 0 192.168.1.1 GEO/1
192.168.1.1/32 Direct 0 0 127.0.0.1 InLoop0
192.168.1.2/32 Direct 이 192.168.1.2 GE0/1
192.168.1.255/32 Direct 0 0 192.168.1.1 GE0/1
224.0.0.0/4 Direct 0 0 0.0.0.0 NULLO
224.0.0.0/24 Direct0 o 0.0.0.0 NULLO
255.255.255.255/32 Direct 0 0 127.0.0.1 InLoop0
```
问题原因发现了,是因为RTA 路由表中没有到PCB所在网段192.168.2.0/24 的路由。PCA
发出报文到RTA 后,RTA 就会丢弃并返回不可达信息给 PCA。我们可以通过配置静态路由而使网络可达。

### 步骤二:静态路由配置规划
请学员考虑,在RTA和RTB 上应该配置到何目的网络的静态路由,其下一跳应该指向哪IP地址?
### 步骤三:配置静态路由

```cmd
[RTA]ip route-static 192.168.2.0 24 192.168.1.2
##
[RTB]ip route-static 192.168.0.0 24 192.168.1.1
##
[RTA] display ip routing-table
Destinations : 18 Routes : 18
Destination/Mask Proto Pre Cost NextHop Interface
0.0.0.0/32 Direct 0 이 127.0.0.1 InLoop0
127.0.0.0/8 Direct 0 0 127.0.0.1 InLoop0
127.0.0.0/32 Direct 0 0 127.0.0.1 InLoop0
127.0.0.1/32 Direct 0 0 127.0.0.1 InLoop0
127.255.255.255/32 Direct 127.0.0.1 InLooр0
192.168.0.0/24 Direct 0 0 192.168.0.1 GEO/0
192.168.0.0/32 Direct 이 0 192.168.0.1 GE0/0
192.168.0.1/32 Direct 0 0 127.0.0.1 InLoop0
192.168.0.255/32 Direct 00 0 192.168.0.1 GE0/0
192.168.1.0/24 Direct 00 192.168.1.1 GE0/1
192.168.1.0/32 Direct 0 이 192.168.1.1 GE0/1
192.168.1.1/32 Direct 이 이 127.0.0.1 InLoop0
192.168.1.2/32 Direct 0 이 192.168.1.2 GE0/1
192.168.1.255/32 Direct 이 이 192.168.1.1 GE0/1
192.168.2.0/24 Static 60 0 192.168.1.2 GEO/1
224.0.0.0/4 Direct 00 0.0.0.0 NULLO
224.0.0.0/24 Direct 0 0.0.0.0 NULLO
255.255.255.255/32 Direct 0 0 127.0.0.1 InLoop0
```
测试 PC 之间的可达性。例如,在 PCA 上用 Ping 命令测试到PCB 的可达性, 如下:
```cmd
C:\Documents and Settings\Administrator>ping 192.168.2.2
Pinging 192.168.2.2 with 32 bytes of data:
Reply from 192.168.2.2: bytes=32 time=20ms TTL=126
``
在PCA上用 Tracert 命令来查看到PCB的路径,
```cmd
C:\Documents and Settings\Administrator>tracert 192.168.2.2
1 <1 ms <1 ms <1 ms 192.168.0.1
2 23 ms 23 ms 23 ms 192.168.1.2
3 28 ms 27 ms 28 ms 192.1682.2
```
以上结果说明,数据报文是沿PCA->RTA->RTB-> PCB 的路径被转发的。

### 步骤四:路由环路观察
为了人为造成环路,需要在RTA 和RTB 上分别配置一条缺省路由,下一跳互相指向对方。
```cmd
[RTA]ip route-static 0.0.0.0 0.0.0.0 192.168.1.2
##
[RTB]ip route-static 0.0.0.0 0.0.0.0 192.168.1.1
###
y[RTA]display ip routing-table
Destinations : 19 Routes : 19
Destination/Mask Proto Pre Cost NextHop Interface
0.0.0.0/0 Static 60 0 0.0.0.0 GE0/1
0.0.0.0/32 Direct 00 127.0.0.1 InLoop0
127.0.0.0/8 Direct 0 이 127.0.0.1 InLoop0
127.0.0.0/32 Ο Direct 0 0 127.0.0.1 InLoop0
127.0.0.1/32 Direct 이 0 127.0.0.1 InLoop0
127.255.255.255/32 Direct 이 0 127.0.0.1 InLoop0
192.168.0.0/24 Direct 0 192.168.0.1 GE0/0
192.168.0.0/32 Direct 0 이 192.168.0.1 GE0/0
192.168.0.1/32 Direct 이 127.0.0.1 InLoop0
192.168.0.255/32 Direct 이 192.168.0.1 GEO/0
192.168.1.0/24 Direct 0 이 192.168.1.1 GE0/1
192.168.1.0/32 Direct 0 이 192.168.1.1 GE0/1
192.168.1.1/32 Direct 0 이 127.0.0.1 InLoop0
192.168.1.2/32 Direct 0 이 192.168.1.2 GE0/1
192.168.1.255/32 Direct 0 0 192.168.1.1 GE0/1
192.168.2.0/24 Static 60 이 192.168.1.2 GE0/1
224.0.0.0/4 Direct 0 0.0.0.0 NULLO
224.0.0.0/24 Direct 0 0 0.0.0.0 NULLO
255.255.255.255/32 Direct 0 0 127.0.0.1 InLoop0
```
可知,缺省路由配置成功。

然后在PC 上用 Tracert 命令来观察环路情况。例如,在PCA 上用 Tracert 命令来追踪到
目的IP地址 3.3.3.3 的路径:
```cmd
C:\Documents and Settings\Administrator>tracert 3.3.3.3
Tracing route to 3.3.3.3 over a maximum of 30 hops
1 <1 ms <1 ms <1 ms 192.168.0.1
2 23 ms 23 ms 23 ms 192.168.1.2
3 27 ms 27 ms 27 ms 192.168.1.1
4 51 ms 51 ms 50 ms 192.168.1.2
5 56 ms 55 ms 55 ms 192.168.1.1
29 385 ms 387 ms 386 ms 192.168.1.1
30 409 ms 409 ms 409 ms 192.168.1.2
Trace complete.
```
由以上输出可以看到,到目的地址3.3.3.3 的报文匹配了缺省路由,报文被转发到了 RTB
(192.168.1.2),而RTB 又根据它的缺省路由,把报文转发回了 RTA (192.168.1.1)。这样就
形成了转发环路,报文在两台路由器之间被循环转发,直到TTL 值到0后被丢弃。

所以在不同路由器上配置到相同网段的静态路由时,不要配置路由的下一跳互相指向对方,
否则就形成了环路。



## cmd list
- ip route-static dest-addr {mask-len|mask} {interface-type interface-num [next-hop-addr]|next-hop-addr}
- disp ip routing-table ip-addr [mask|mask-len] 
- ipconfig

結語

完成

