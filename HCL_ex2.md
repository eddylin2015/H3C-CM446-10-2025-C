# 小型企业网络思路规划配置分享，H3C HCL模拟器

![](https://90apt.com/usr/uploads/2021/06/1617891069.png)

采用三层网络结构，核心、汇聚三层互联，堆叠采用40G网络，汇聚10G，接入1G，网关下放到汇聚，交换机采用独立管理VLAN。

功能实现

1、核心堆叠，汇聚堆叠，动态端口聚合
2、配置DHCP服务器，配置DHCP中继，为多个VLAN提供服务
3、静态路由与OSPF，配合ACL实现不同网络的访问控制
4、外网NAT，专线静态路由
5、接入交换机Telnet、管理IP实现
6、SNMP网管服务部署
7、DHCP仿冒防御
8、端口隔离，防范网络风暴和ARP攻击

项目文件分享

模拟器为H3C官方HCL模拟器，安装并导入即可

 下载

配置详情

1、设置固定IP，配置主机名
如图片所示

2、核心堆叠，采用40G口堆叠
核心1

<hexin1>sys
System View: return to User View with Ctrl+Z.
[hexin1]int range FortyGigE 1/0/53 to FortyGigE 1/0/54
[hexin1-if-range]shu
[hexin1-if-range]quit
[hexin1]irf member 1 priority 32
[hexin1]irf-port 1/1
[hexin1-irf-port1/1]port group interface FortyGigE 1/0/53
[hexin1-irf-port1/1]port group interface FortyGigE 1/0/54
[hexin1-irf-port1/1]quit
[hexin1]irf-port-configuration active
[hexin1]int range FortyGigE 1/0/53 to FortyGigE 1/0/54
[hexin1-if-range]un sh
[hexin1-if-range]save
核心2

[hexin2]sys
[hexin2]irf member 1 renumber 2
Renumbering the member ID may result in configuration change or loss. Continue?[Y/N]:y
[hexin2]quit
<hexin2>reboot
<hexin2>sys
System View: return to User View with Ctrl+Z.
[hexin2]interface range FortyGigE 2/0/53 to FortyGigE 2/0/54
[hexin2-if-range]shu
[hexin2-if-range]quit
[hexin2]irf member 2 priority 1
[hexin2]irf-port 2/2
[hexin2-irf-port2/2]port group interface FortyGigE 2/0/53
[hexin2-irf-port2/2]port group interface FortyGigE 2/0/54
[hexin2-irf-port2/2]qui
[hexin2]irf-port-configuration  active
[hexin2]interface range FortyGigE 2/0/53 to FortyGigE 2/0/54
[hexin2-if-range]un sh
[hexin2-if-range]quit
[hexin2]save
连接堆叠线后，机器自动重启，此时两台交换机终端都会显示为 hexin1

3、车间汇聚堆叠，采用40G口
步骤与核心相同，堆叠后两台终端都会显示为 chejianhuiju1

4、按图片为交换机配置IP和VLAN，三层采用路由模式，汇聚下联trunk，接入上联trunk，下联对应vlan
车间汇聚做端口聚合

[chejianhuiju1]vlan 1004
[chejianhuiju1-vlan1004]int vlan 1004
[chejianhuiju1-Vlan-interface1004]ip add 10.0.4.254 24
[chejianhuiju1-Vlan-interface1004]quit
[chejianhuiju1]int Bridge-Aggregation 1
[chejianhuiju1-Bridge-Aggregation1]link-aggregation mode dynamic
[chejianhuiju1-Bridge-Aggregation1]quit
[chejianhuiju1]int g1/0/1
[chejianhuiju1-GigabitEthernet1/0/1]port link-aggregation group 1
[chejianhuiju1-GigabitEthernet1/0/1]int g2/0/1
[chejianhuiju1-GigabitEthernet2/0/1]port link-aggregation group 1
[chejianhuiju1-GigabitEthernet2/0/1]dis link-aggregation verbose
  GE1/0/1             0       32768    0         0x8000, 0000-0000-0000 {EF}
  GE2/0/1             0       32768    0         0x8000, 0000-0000-0000 {EF}
[chejianhuiju1-GigabitEthernet2/0/1]vlan 1004
[chejianhuiju1-vlan1004]port Bridge-Aggregation 1
[chejianhuiju1]int Bridge-Aggregation 1
[chejianhuiju1-Bridge-Aggregation1]port link-type trunk
[chejianhuiju1-Bridge-Aggregation1]port trunk permit vlan all
[chejianhuiju1-Bridge-Aggregation1]save
验证生产设备，ping 10.0.20.4 10.0.50.5 10.0.4.254 都通

5、配置OSPF，实现车间、办公、生产服务器、基础服务器互通

配置核心

<hexin1>sys
System View: return to User View with Ctrl+Z.
[hexin1]ospf
[hexin1-ospf-1]area 0
[hexin1-ospf-1-area-0.0.0.0]netwo
[hexin1-ospf-1-area-0.0.0.0]network 10.0.70.0 0.0.0.255
[hexin1-ospf-1-area-0.0.0.0]network 10.0.40.0 0.0.0.255
[hexin1-ospf-1-area-0.0.0.0]network 10.0.60.0 0.0.0.255
[hexin1-ospf-1-area-0.0.0.0]network 10.0.30.0 0.0.0.255
[hexin1-ospf-1-area-0.0.0.0]network 10.0.50.0 0.0.0.255
[hexin1-ospf-1-area-0.0.0.0]quit
配置车间汇聚

<chejianhuiju1>sys
System View: return to User View with Ctrl+Z.
[chejianhuiju1]ospf
[chejianhuiju1-ospf-1]area 0
[chejianhuiju1-ospf-1-area-0.0.0.0]network 10.0.4.0 0.0.0.255
[chejianhuiju1-ospf-1-area-0.0.0.0]network 10.0.20.0 0.0.0.255
[chejianhuiju1-ospf-1-area-0.0.0.0]network 10.0.50.0 0.0.0.255
[chejianhuiju1-ospf-1-area-0.0.0.0]quit
生产设备ping核心通，其他配置类似。

6、配置DHCP服务器
使用三层交换机搭建DHCP服务器，ping测试

[H3C]hostname dhcp
[dhcp]int g1/0/1
[dhcp-GigabitEthernet1/0/1]port link-mode route
[dhcp-GigabitEthernet1/0/1]ip add 10.0.0.1 24
[dhcp-GigabitEthernet1/0/1]save
[dhcp-GigabitEthernet1/0/1]quit
[dhcp]ip route-static 0.0.0.0 0 10.0.0.254
[dhcp]ping 10.0.0.254
Ping 10.0.0.254 (10.0.0.254): 56 data bytes, press CTRL_C to break
56 bytes from 10.0.0.254: icmp_seq=0 ttl=255 time=0.000 ms
创建DHCP池

[dhcp]dhcp enable

dhcp server ip-pool bangong
 gateway-list 10.0.3.254
 network 10.0.3.0 mask 255.255.255.0
 address range 10.0.3.100 10.0.3.200
 dns-list 8.8.8.8
 expired day 3
#
dhcp server ip-pool wuxian
 gateway-list 10.0.2.254
 network 10.0.2.0 mask 255.255.255.0
 address range 10.0.2.150 10.0.2.200
 dns-list 114.114.114.114
 expired day 3
#
沿途汇聚、核心都要开启DHCP中继，二层只要有对应VLAN并trunk即可。

[jichuhuiju]dhcp enable
#
interface Vlan-interface1002
 dhcp select relay
 dhcp relay server-address 10.0.0.1
#
interface Vlan-interface1003
 dhcp select relay
 dhcp relay server-address 10.0.0.1
#
查看客户端IP，成功获取IP

[dhcp]display dhcp server ip-in-use
IP address       Client identifier/    Lease expiration      Type
                 Hardware address
10.0.2.150       0038-6163-312e-3334-  Jun 26 20:35:43 2021  Auto(C)
                 3266-2e31-3730-362d-
                 4745-302f-302f-31
10.0.3.100       0038-6137-362e-3466-  Jun 28 20:35:31 2021  Auto(C)
                 3864-2e31-3230-362d-
                 4745-302f-302f-31
7、配置专线，仅办公和无线可以访问
办公汇聚、无线汇聚、核心、专线静态路由

[wuxianhuiju] ip route-static 10.1.0.0 24 10.0.60.10
[hexin1]ip route-static 10.1.0.0 24 10.0.90.18
[zhuanxianwangguan]ip route-static 10.0.2.0 24 10.0.90.15
测试办公和无线都可以访问专线IP10.1.0.2

8、配置办公和无线能访问外网，但外网无法直接访问内网
办公汇聚、无线汇聚、核心默认路由，外网网关静态路由

[bangonghuiju]ip route-static 0.0.0.0 0 10.0.30.6
[wuxianhuiju]ip route-static 0.0.0.0 0 10.0.60.10
[hexin1]ip route-static 0.0.0.0 0 10.0.10.1

[waibuwangguan]ip route-static 10.0.3.0 24 10.0.10.2
[waibuwangguan]ip route-static 10.0.2.0 24 10.0.10.2
配置最简单NAT访问方式Easy IP

[waibuwangguan]acl basic 200
[waibuwangguan-acl-ipv4-basic-2000]rule 0 permit source 10.0.2.0 0.0.0.255
[waibuwangguan-acl-ipv4-basic-2000]acl basic 2001
[waibuwangguan-acl-ipv4-basic-2001]rule 0 permit source 10.0.3.0 0.0.0.255
[waibuwangguan-acl-ipv4-basic-2001]quit
[waibuwangguan]int g0/0
[waibuwangguan-GigabitEthernet0/0]nat outbound 2001
[waibuwangguan-GigabitEthernet0/0]nat outbound 2000
办公和无线ping外网1.1.1.2通，外网ping内网不通

9、POE供电
受模拟器限制无法实现，实际在无线接入执行 poe enable 即可

10、办公人员通过telnet远程管理车间接入交换机
车间汇聚创建管理vlan2000

[chejianhuiju1]vlan 2000
[chejianhuiju1-vlan2000]int vlan 2000
[chejianhuiju1-Vlan-interface2000]ip add 192.168.1.254 24
车间接入创建管理vlan，开启telnet服务，设置默认路由

<chejianjieru>sys
System View: return to User View with Ctrl+Z.
[chejianjieru]vlan 2000
[chejianjieru-vlan2000]int vlan 2000
[chejianjieru-Vlan-interface2000]ip add 192.168.1.2 24
[chejianjieru-Vlan-interface2000]quit
[chejianjieru]user-interface vty 0 4
[chejianjieru-line-vty0-4]authentication-mode scheme
[chejianjieru-line-vty0-4]quit
[chejianjieru]local-user admin
New local user added.
[chejianjieru-luser-manage-admin]password simple 123456
[chejianjieru-luser-manage-admin]authorization-attribute user-role level-15
[chejianjieru-luser-manage-admin]service-type telnet
[chejianjieru-luser-manage-admin]quit
[chejianjieru]telnet server enable
[chejianjieru]save
[chejianjieru]ip route-static 0.0.0.0 0 192.168.1.254
核心添加静态路由

[hexin1]ip route-static 192.168.1.0 24 10.0.20.4
办公人员远程telnet

<bangonghuiju>telnet 192.168.1.2
Trying 192.168.1.2 ...
Press CTRL+K to abort
Connected to 192.168.1.2 ...

******************************************************************************
* Copyright (c) 2004-2017 New H3C Technologies Co., Ltd. All rights reserved.*
* Without the owner's prior written consent,                                 *
* no decompiling or reverse-engineering shall be allowed.                    *
******************************************************************************

login: admin
Password:
<chejianjieru>
11、配置snmp网络管理协议
配置向10.0.0.1发送设备信息

snmp-agent
snmp-agent community write private
snmp-agent community read public
snmp-agent sys-info version all
snmp-agent target-host trap address udp-domain 10.0.0.1 params securityname public v2c
12、配置监控网络，办公和无线可以访问监控服务器，不可访问摄像头，摄像头仅与监控服务器互相访问
核心设置静态路由，监控汇聚设置默认路由

[hexin1]ip route-static 10.0.5.0 24 10.0.80.17
[jiankonghuiju]ip route-static 0.0.0.0 0 10.0.80.16
在监控汇聚上联接口配置ACL规则，只允许访问10.0.5.1发出，其他禁止，从而达到只允许监控服务器被访问的目的

[jiankonghuiju]acl basic 2000
[jiankonghuiju-acl-ipv4-basic-2000]rule 0 permit source 10.0.5.1 0
[jiankonghuiju-acl-ipv4-basic-2000]rule 1 deny
[jiankonghuiju-acl-ipv4-basic-2000]quit
[jiankonghuiju]int Ten-GigabitEthernet1/0/49
[jiankonghuiju-Ten-GigabitEthernet1/0/49]packet-filter 2000 outbound
测试办公可以ping通10.0.5.1，不能ping通10.0.5.2

13、配置DHCP snooping，防止仿冒攻击
全局开启dhcp snooping，上联端口启用dhcp信任

[bangongjieru]dhcp snooping enable
[bangongjieru]interface GigabitEthernet1/0/2
[bangongjieru]dhcp snooping trust
14、配置端口隔离，减少接入傻瓜交换机造成的网络风暴，防御ARP攻击

[H3C]port-isolate group 2
[H3C]int g1/0/1
[H3C-GigabitEthernet1/0/1]port-isolate enable group 2
[H3C-GigabitEthernet1/0/1]int g1/0/2
[H3C-GigabitEthernet1/0/2]port-isolate enable group 2
[H3C-GigabitEthernet1/0/2]quit
[H3C]dis port-isolate group 2
Port isolation group information:
Group ID: 2
Group members:
GigabitEthernet1/0/1          GigabitEthernet1/0/2
