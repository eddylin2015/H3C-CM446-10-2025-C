# 中型企业网络思路规划配置分享，H3C HCL模拟器，easy-irf堆叠 OSPF优化
# 
# 中型企业网络思路规划配置分享，H3C HCL模拟器，easy-irf堆叠 OSPF优化
王忘杰
王忘杰
2024-08-16
/
0 评论
/
453 阅读
/
已收录
lzwe6xcg.png

整体规划
本架构是在小型企业网络思路上进行增强
https://90apt.com/2449

功能实现

1、easy-irf快速堆叠，配置心跳检测
2、OSPF分区域、双线配置双路由、配置p2p模式、限制收发OSPF报文端口

模拟器实验
线缆全部链接完成
m03grlc5.png

IP规划

网段划分
loopback 0
网络核心 10.100.0.1
监控核心 10.100.0.2
网络汇聚 10.100.0.3
监控汇聚 10.100.0.4

网络核心1/0/49to监控核心1/0/49 10.100.1.1/30 10.100.1.2/30
网络核心2/0/49to监控核心1/0/50 10.100.1.5/30 10.100.1.6/30
网络核心1/0/50to网络汇聚1/0/50 10.100.1.9/30 10.100.1.10/30
网络核心2/0/50to网络汇聚1/0/49 10.100.1.13/30 10.100.1.14/30
监控核心1/0/51to监控汇聚1/0/49 10.100.1.17/30 10.100.1.18/30



网络配置
网络核心
堆叠 1/0/53 to 2/0/53     1/0/54 to 2/0/54
心跳 1/0/48 to 2/0/48 模拟器限制，连接会死机，不进行连接

IP地址
网络汇聚 vlan102 10.100.102.1/24
监控汇聚 vlan103 10.100.103.1/24
网络主机 10.100.102.2/24
监控主机 10.100.103.2/24

ospf区域
网络核心
area0
10.100.1.0 0.0.0.3
10.100.1.4 0.0.0.3
10.100.1.8 0.0.0.3
10.100.1.12 0.0.0.3

监控核心
area0
10.100.1.0 0.0.0.3
10.100.1.4 0.0.0.3
10.100.1.16 0.0.0.3

网络汇聚
area0
10.100.1.8 0.0.0.3
10.100.1.12 0.0.0.3
area1
10.100.102.0 0.0.0.255

监控汇聚
area0
10.100.1.16 0.0.0.3
area1
10.100.103.0 0.0.0.255
一、进行核心堆叠
1、配置交换机名

hostname wangluo hexin
2、核心1配置

easy-irf member 1 domain 0 priority 24 irf-port1 FortyGigE 1/0/53
 FortyGigE 1/0/54a
保存配置
save
3、核心2配置
第二台核心编号重命名为2，优先级18，低于第一台24

easy-irf member 1 renumber 2 domain 0 priority 18 irf-port2 FortyGigE 1/0/5
3 FortyGigE 1/0/54
按提示，输入Y重启

4、配置堆叠心跳检测BFD MAD
由于模拟器限制，配置完后心跳线连接会死机，因此心跳线不连接
堆叠心跳检测BFD MAD

新建用于irf检测的vlan，注意，后续trunk接口要阻止此VLAN通过

vlan 4094
description irf-mad
qu
创建VPN实例，用于隔离路由路由表

ip vpn-instance mgmt
route-distinguisher 1:1
qu
配置虚接口，配置心跳检测IP，绑定VPN实例

int vlan4094
description irf-mad
ip binding vpn-instance mgmt
mad bfd enable
mad ip address 1.0.0.1 255.255.255.252 member 1
mad ip address 1.0.0.2 255.255.255.252 member 2
qu
心跳接口配置

interface GigabitEthernet 1/0/48
description irf-mad
port access vlan 4094
undo stp enable
interface GigabitEthernet 2/0/48
description irf-mad
port access vlan 4094
undo stp enable
5、IRF保留接口，一般用于上行三层接口，设备分裂后保持通讯

mad exclude interface Ten-GigabitEthernet 2/0/50
二、配置所有IP地址
三层交换机改名、配置loopback地址、配置IP地址、PC配置IP地址

interface Ten-GigabitEthernet1/0/49
 port link-mode route
 combo enable fiber
 ip address 10.100.1.1 255.255.255.252

interface LoopBack0
 ip address 10.100.0.3 255.255.255.255


[wangluo huiju-GigabitEthernet1/0/1]ping 10.100.102.2
Ping 10.100.102.2 (10.100.102.2): 56 data bytes, press CTRL_C to break
56 bytes from 10.100.102.2: icmp_seq=0 ttl=255 time=6.212 ms
56 bytes from 10.100.102.2: icmp_seq=1 ttl=255 time=3.258 ms
56 bytes from 10.100.102.2: icmp_seq=2 ttl=255 time=3.670 ms
56 bytes from 10.100.102.2: icmp_seq=3 ttl=255 time=2.772 ms
56 bytes from 10.100.102.2: icmp_seq=4 ttl=255 time=4.012 ms
m03gt6rm.png

三、配置OSPF
配置OSPF之前先配置loopback地址
三层接口使能OSPF，配置为P2P模式，放在区域0

[wangluo hexin-Ten-GigabitEthernet1/0/49]dis th
#
interface Ten-GigabitEthernet1/0/49
 port link-mode route
 combo enable fiber
 ip address 10.100.1.1 255.255.255.252
 ospf network-type p2p
 ospf 1 area 0.0.0.0
loopback使能OSPF，放在区域0

interface LoopBack0
 ip address 10.100.0.3 255.255.255.255
 ospf 1 area 0.0.0.0
VLAN使能OSPF，业务网段放在区域1

[wangluo huiju-Vlan-interface102]dis th
#
interface Vlan-interface102
 ip address 10.100.102.1 255.255.255.0
 ospf 1 area 0.0.0.1
#
全部配置完成后，网络主机可ping通监控主机

<H3C>ping 10.100.103.2
Ping 10.100.103.2 (10.100.103.2): 56 data bytes, press CTRL_C to break
56 bytes from 10.100.103.2: icmp_seq=0 ttl=251 time=18.180 ms
56 bytes from 10.100.103.2: icmp_seq=1 ttl=251 time=9.608 ms
56 bytes from 10.100.103.2: icmp_seq=2 ttl=251 time=14.053 ms
56 bytes from 10.100.103.2: icmp_seq=3 ttl=251 time=13.112 ms
56 bytes from 10.100.103.2: icmp_seq=4 ttl=251 time=12.268 ms
四、OSPF优化
限制OSPF收发报文的端口

[wangluo hexin-ospf-1]dis th
#
ospf 1
 silent-interface all
 undo silent-interface Ten-GigabitEthernet1/0/49
 undo silent-interface Ten-GigabitEthernet1/0/50
 undo silent-interface Ten-GigabitEthernet2/0/49
 undo silent-interface Ten-GigabitEthernet2/0/50
 area 0.0.0.0
五、测试
网络主机ping监控主机，在ping过程中断掉双线中的一条，会丢包一个，删除多个双线中的一条，最多会丢包4个
m03gxqii.png

m03gxvbz.png

56 bytes from 10.100.103.2: icmp_seq=519 ttl=251 time=16.112 ms
56 bytes from 10.100.103.2: icmp_seq=520 ttl=251 time=20.881 ms
56 bytes from 10.100.103.2: icmp_seq=521 ttl=251 time=15.032 ms
Request time out
56 bytes from 10.100.103.2: icmp_seq=523 ttl=251 time=36.649 ms
56 bytes from 10.100.103.2: icmp_seq=524 ttl=251 time=16.727 ms
56 bytes from 10.100.103.2: icmp_seq=525 ttl=251 time=15.137 ms
56 bytes from 10.100.103.2: icmp_seq=526 ttl=251 time=17.651 ms
Request time out
Request time out
Request time out
Request time out
Request time out
56 bytes from 10.100.103.2: icmp_seq=532 ttl=251 time=13.904 ms
56 bytes from 10.100.103.2: icmp_seq=533 ttl=251 time=42.994 ms
56 bytes from 10.100.103.2: icmp_seq=534 ttl=251 time=27.916 ms
56 bytes from 10.100.103.2: icmp_seq=535 ttl=251 time=14.782 ms
六、实机测试
1、实际测试BFD防分裂能力
物理机配置BFD不存在死机问题，模拟器有BUG
m03kegvs.png

当前1为主，2为从，共连接了1/0/18 2/0/18 2/0/23，其中2/0/23配置了IRF保留接口

直接拔掉两条堆叠线
结果，终端BFD告警，交换机接口2/0/18停止工作，仅保留接口2/0/23保持工作

%Jan  7 12:29:20:050 2021 S5560X-30F-EI_09 BFD/5/BFD_CHANGE_FSM: Sess[1.0.0.1/1.0.0.2, LD/RD:32897/32897, Interface:Vlan4094, SessType:Ctrl, LinkType:INET], Ver:1, Sta: DOWN->INIT, Diag: 0 (No Diagnostic)
%Jan  7 12:29:20:055 2021 S5560X-30F-EI_09 BFD/5/BFD_CHANGE_FSM: Sess[1.0.0.1/1.0.0.2, LD/RD:32897/32897, Interface:Vlan4094, SessType:Ctrl, LinkType:INET], Ver:1, Sta: INIT->UP, Diag: 0 (No Diagnostic)
%Jan  7 12:29:22:747 2021 S5560X-30F-EI_09 DEV/3/BOARD_REMOVED: Board was removed from slot 2, type is unknown.
插回堆叠线，交换机2自动重启

2、测试堆叠切换
直接拔掉交换机1的电源线
交换机2成为主交换机继续工作

交换机1插电
交换机1成为从交换机，交换机2保持主交换机，通讯恢复

总结
