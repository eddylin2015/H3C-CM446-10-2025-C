# H3C华三交换机学习笔记

模拟器bug
注意！注意！不要调整模拟器内设备的内存，极易造成设备出bug，比如路由器内存调小后，自己ping自己都不通！
https://zhiliao.h3c.com/questions/dispcont/8085
“配置没问题，端口也up了,也ping不通自己，是因为hcl路由器或交换机的内存不能人为的调小于520M，调成350M就容易出问题”


基础命令：

进入系统视图<H3C>system-view
路由追踪[H3C]tracert
更改主机名[H3C]hostname Switch1

显示详细路由追踪信息
[H3C]ip ttl-expires enable
[H3C]ip unreachables enable

保存[H3C]save
重启<H3C>reboot
查看接口信息[H3C]display interface brief
查看详细配置文件[H3c]display current-cohnfiguration
查看OSPF信息[SwitchA]display ospf peer verbose
查看SN序列号display device manuinfo

display counters  inbound interface
display counters outbound interface
查询所有接口包速率

开启NTP时间同步，同步需要几分钟
[H3C]dis clock
04:06:38.180 UTC Sat 01/05/2013
[H3C]sntp enable
[H3C]sntp unicast-server 172.16.21.246
[H3C]display sntp sessions
SNTP server     Stratum   Version    Last receive time
11.22.33.44   4         4          Sat, Jan  5 2013  4:07:40.856

查看交换机时间
[H3C]dis clock
08:11:36.223 UTC Mon 04/25/2022

关闭LLDP PVID检查
lldp ignore-pvid-inconsistency

打开LLDP PVID检查
undo lldp ignore-pvid-inconsistency
通过IP和MAC地址查找所在交换机
1、查看本机IP和MAC地址

以太网适配器 以太网:
   描述. . . . . . . . . . . . . . . : Realtek PCIe GbE Family Controller
   物理地址. . . . . . . . . . . . . : XX-XX-XX-XX-XX-XX
   IPv4 地址 . . . . . . . . . . . . : 172.XX.X.XXX(首选)
2、登录VLAN网关，查看IP和MAC地址
查看IP MAC对应关系

dis arp | include 172.XX.X.XXX
172.XX.X.XXX    xxxx-xxxx-xxxx 18            BAGG1                    852   D
查看MAC所在接口

dis mac-addr | include xxxx-xxxx-xxxx
xxxx-xxxx-xxxx   18         Learned          BAGG1                    Y
3、查看接口下联交换机IP
查看接口与端口绑定关系

dis cu
查看接口下lldp信息

dis lldp n v
4、依次登录下层交换机，最终确定接口所在交换机

dis mac-addr | include xxxx-xxxx-xxxx
xxxx-xxxx-xxxx   18         Learned          GE1/0/43                 Y

查看接口STP报文数量，其中G1/0/5口异常
<H3C>display stp tc
 -------------- STP slot 1 TC or TCN count -------------
 MST ID   Port                                Receive  Send
 0        Bridge-Aggregation1                 1        34537
 0        Bridge-Aggregation2                 109      9708
 0        GigabitEthernet1/0/1                10       458
 0        GigabitEthernet1/0/2                29       1580
 0        GigabitEthernet1/0/3                0        18213
 0        GigabitEthernet1/0/4                0        18243
 0        GigabitEthernet1/0/5                1496     8417
 0        GigabitEthernet1/0/6                0        18225
 0        GigabitEthernet1/0/7                423      17148
 0        GigabitEthernet1/0/8                3        3856
 0        GigabitEthernet1/0/9                36       18298
 0        GigabitEthernet1/0/10               0        18277
 0        GigabitEthernet1/0/17               185      34206
 0        GigabitEthernet1/0/18               0        34535
 0        GigabitEthernet1/0/20               0        206
 0        GigabitEthernet1/0/21               0        34535
 0        GigabitEthernet1/0/23               0        34536
 0        GigabitEthernet1/0/24               0        3306
 
查看交换机日志
<H3C>dis logbuffer

查看邻居信息
<H3C>dis lldp neighbor-information list
Chassis ID : * -- -- Nearest nontpmr bridge neighbor
             # -- -- Nearest customer bridge neighbor
             Default -- -- Nearest bridge neighbor
System Name          Local Interface Chassis ID      Port ID
XX             GE1/0/1         70c6-ddb5-905e  70c6-ddb5-9087
XX          GE1/0/2         6ce5-f71b-0754  GigabitEthernet1/0/28
XX             GE1/0/3         9ce8-955a-b540  GigabitEthernet1/0/28
XX     GE1/0/4         1cab-3479-2220  1cab-3479-2220
XX         GE1/0/5         6ce5-f71b-904c  GigabitEthernet1/0/28
H3C                  GE1/0/6         3c8c-4010-1f3e  GigabitEthernet1/0/26

查看邻居详细信息
<S-151-04>dis lldp neighbor-information verbose
LLDP neighbor-information of port 1[GigabitEthernet1/0/1]:
LLDP agent nearest-bridge:
 LLDP neighbor index : 1
 Update time         : 46 days, 6 hours, 59 minutes, 31 seconds
 Chassis type        : MAC address
 Chassis ID          : 70c6-ddb5-905e
 Port ID type        : MAC address
 Port ID             : 70c6-ddb5-9087
 Time to live        : 121
 Port description    : GigabitEthernet1/0/28 Interface
 System name         : S-178-01
 System description  : H3C Comware Platform Software, Software Version 7.1.070,
                       Release 6328P03
                       H3C S5130S-28P-HPWR-EI
                       Copyright (c) 2004-2021 New H3C Technologies Co., Ltd. Al
                       l rights reserved.
 System capabilities supported : Bridge, Router, Customer Bridge, Service Bridge
 System capabilities enabled   : Bridge, Router, Customer Bridge
 Management address type           : IPv4
 Management address                : 169.254.144.94
 Management address interface type : IfIndex
 Management address interface ID   : 635
 Management address OID            : 0


dis process cpu  查看进程对于CPU的使用率。
dis process memory 查看进程对于内存使用率
dis cpu-usage 查看CPU使用率。
dis memory 查询内存使用率。
display fan 查看风扇
display power 查看电源
dis device 查看板卡状态  
display logbuffer  查看设备日志
display environment 查看温度
dis counter rate inbound interface 查看接口进方向的使用率
dis counter rate outbound interface 查看接口出方向的使用率
dis int | inc rate 查看接口历史使用率
dis int | inc sec 查看接口历史使用率及出入方向的字节
dis int gi 1/0/1 查看接口最近300秒使用率

子接口配置，一个VLAN配置多个IP
interface Vlan-interface X
ip address 172.16.1.1 255.255.252.0
ip address 172.16.2.1 255.255.252.0 sub
ip address 172.16.3.1 255.255.252.0 sub

包转发利用率
1.查看接口进方向的使用率:dis counter rate inbound interface
2.查看接口出方向的使用率:dis counter rate outbound interface
3.查看接口历史使用率:dis int | inc rate
4.查看接口历史使用率及出入方向的字节:dis int | inc sec
5、查看接口最近300秒使用率：dis int gi 1/0/1


修改风扇旋转反向
fan prefer-direction slot 1 port-to-power  
查看风扇状态
dis fan

查看MAC地址震荡、环路排查
dis mac-address mac-move
典型环路状态
dis mac-address mac-move
MAC address    VLAN Current port  Source port   Last time              Times
f6f6-0002-17d7   19      GE1/0/5      GE1/0/2   2024-01-02 17:00:33   922161
f6f6-0004-1d53   19      GE1/0/7      GE1/0/3   2024-01-02 17:00:39   941803
f6f6-0002-17d7   19      GE1/0/2      GE1/0/5   2024-01-02 17:00:34   922342
f6f6-0004-1d53   19      GE1/0/3      GE1/0/7   2024-01-02 17:00:38   941803


查看STP接口状态
dis stp brief

配置STP边缘节点，只适用于三层接口，稳定STP网络
interface GigabitEthernet1/0/5
 port access vlan 19
 stp edged-port


配置STP主根桥，减少网STP收敛络震荡
 stp instance 0 root primary


配置端口速率
interface GigabitEthernet1/0/13
 port access vlan 21
 speed 100

清除配置，恢复出厂
<H3C>reset saved-configuration
The saved configuration file will be erased. Are you sure? [Y/N]:y  #选择Y确认
<H3C>reboot
Start to check configuration with next startup configuration file, please wait.........DONE! Current configuration may be lost after the reboot, save current configuration? [Y/N]:n  #N选择不保存
This command will reboot the device. Continue? [Y/N]:y #Y确认重启

关闭日志
关闭终端上下线日志分为以下几种情况

（1）通过console的方式登录时
info-center source STAMGR console deny
info-center source WLANAUD console deny 

（2）通过telnet的方式登录时
info-center source STAMGR monitor deny 
info-center source WLANAUD monitor deny 

以上两种情况互不影响。这两种方式只是在总控制台不显示用户上下线的日志信息，但设备的logbuffer里还是记录的，如果想要在logbuffer也不记录日志，需采取以下两种命令关闭：
info-center source STAMGR logbuffer deny 
info-center source WLANAUD logbuffer deny 

如关闭DHCP日志
info-center source DHCPS logbuffer deny

配置日志服务器
info-center loghost 172.16.21.111

POE功率、功耗查询
<SW>dis poe pse
 PSE ID                           : 4
 Slot No.                         : 1
 SSlot No.                        : 0
 PSE Model                        : LSPPSE24B
 PSE Status                       : Enabled
 PSE Fast Power Supply            : Disabled
 Power Priority                   : Low
 Current Power                    : 36.2     W
 Average Power                    : 35.9     W
 Peak Power                       : 44.5     W
 Max Power                        : 370.0    W
 Max Allocable Power              : 370.0    W
 Remaining Guaranteed Power       : 370.0    W
 PSE CPLD Version                 : -
 PSE Software Version             : 510
 PSE Hardware Version             : 57855
 PSE Legacy PD Detection          : Disabled
 Power Utilization Threshold      : 80
 PD Power Policy                  : Disabled
 PD Disconnect-Detection Mode     : AC
 PD High Inrush                   : Disabled


<SW>dis poe interface
 Interface    PoE       Priority  CurPower  Oper      IEEE  Detection
                                  (W)                 Class Status
 GE1/0/1      Enabled   Low       2.2       On        0     Delivering Power
 GE1/0/2      Enabled   Low       0.0       Off       0     Searching
 GE1/0/3      Enabled   Low       2.9       On        0     Delivering Power
 GE1/0/4      Enabled   Low       0.0       Off       0     Searching
 GE1/0/5      Enabled   Low       2.6       On        0     Delivering Power
 GE1/0/6      Enabled   Low       2.5       On        0     Delivering Power
 GE1/0/7      Enabled   Low       2.3       On        0     Delivering Power
 GE1/0/8      Enabled   Low       2.7       On        0     Delivering Power
 GE1/0/9      Enabled   Low       2.5       On        0     Delivering Power
 GE1/0/10     Enabled   Low       2.6       On        0     Delivering Power
 GE1/0/11     Enabled   Low       2.6       On        0     Delivering Power
 GE1/0/12     Enabled   Low       2.4       On        0     Delivering Power
 GE1/0/13     Enabled   Low       2.5       On        0     Delivering Power
 GE1/0/14     Enabled   Low       2.6       On        0     Delivering Power
 GE1/0/15     Enabled   Low       2.8       On        0     Delivering Power
 GE1/0/16     Enabled   Low       0.0       Off       0     Searching
 GE1/0/17     Enabled   Low       2.4       On        0     Delivering Power
 GE1/0/18     Enabled   Low       0.0       Off       0     Searching
 GE1/0/19     Enabled   Low       0.0       Off       0     Searching
 GE1/0/20     Enabled   Low       0.0       Off       0     Searching
 GE1/0/21     Enabled   Low       0.0       Off       0     Searching
 GE1/0/22     Enabled   Low       0.0       Off       0     Searching
 GE1/0/23     Enabled   Low       0.0       Off       0     Searching
 GE1/0/24     Enabled   Low       0.0       Off       0     Searching
   ---  On State Ports: 14; Used: 35.6(W); Remaining: 334.4(W)  ---


环路检测排查
查看CPU使用率

dis cpu
查看MAC漂移

dis mac-address mac-move
查看STP接口状态

dis stp brief
查看lldp状态

dis lldp neighbor-information list
dis lldp neighbor-information verbose
环路检测配置
https://www.h3c.com/cn/d_202308/1905729_30005_0.htm#_Ref470192304

loopback-detection global enable vlan { vlan-id-list | all } 全局开启环路检测

interface interface-type interface-number 进入二层以太网接口/二层聚合接口视图
loopback-detection enable vlan { vlan-id-list | all } 在端口上开启环路检测功能

loopback-detection global action shutdown 全局配置环路检测的处理模式

interface interface-type interface-number 进入接口视图
loopback-detection action { block | no-learning | shutdown } 在端口上配置环路检测的处理模式

loopback-detection interval-time interval 配置检测间隔时间

display loopback-detection 显示环路检测的配置和运行情况
查询AP上线离线掉线时间
网络-操作-无线配置-AP管理-详情

lk3kgmti.png

![](https://90apt.com/usr/uploads/2023/07/2477475830.png)

基础配置-上线/离线/版本下载时间2023-07-15 13:23:29

二层端口模式
参考链接：https://blog.51cto.com/shyln/2087240

 a）access端口
       发送（从交换机内部往外发送）：
                带有vlan tag：删除tag后，发送
                不带vlan tag：不可能出现
       接收：
                带有vlan tag：若该tag等于该access端口的pvid，则可以接收，进入交换机内部       
                不带vlan tag：添加该access端口的pvid，进入交换机内部             
 b）trunk端口（允许发送native VLAN数据的时候，可以不加tag）
       发送（从交换机内部往外发送）：
                带有vlan tag：若tag等于该trunk端口的pvid，则删除tag后发送；否则保留tag直接发送
                不带vlan tag：不可能出现
       接收：
                带有vlan tag：保留该tag，进入交换机内部
                不带vlan tag：添加该trunk端口的pvid，进入交换机内部       
 c）hybrid端口（允许发送多个VLAN数据的时候，可以不加tag）
       发送（从交换机内部往外发送）：
                带有vlan tag：
                         是否带tag进行发送，取决于用户配置（用户可以配置tagged list，untagged list）
                不带vlan tag：不可能出现
       接收：
                带有vlan tag：保留该tag，进入交换机内部
              　不带vlan tag：添加该hybrid端口的pvid，进入交换机内部
QQ图片20210526211328.png

![](https://90apt.com/usr/uploads/2021/05/3148553831.png)

模拟傻瓜交换机
思路，创建全部vlan，端口启用untag。

vlan 2 to 4000 #批量创建vlan
interface GigabitEthernet1/0/1 #进入接口
 port link-type hybrid #接口类型hybrid
 port hybrid vlan 1 to 4000 untagged #撕掉vlan标签
QQ图片20210526220724.png

![](https://90apt.com/usr/uploads/2021/05/1344630434.png)


配置telnet
参考链接
https://jingyan.baidu.com/article/1876c852517425890b1376d2.html

给VLAN1配置IP

[H3C-Vlan-interface1]ip address 192.168.56.254 255.255.255.0 
[H3C-Vlan-interface1]quit
配置VTY(Virtual Teletype Terminal)虚拟终端接口的认证方式


[H3C]user-interface vty 0 4
[H3C-line-vty0-4]authentication-mode scheme     
                             //进行本地或远端用户名和口令认证。即AAA认证
                            //关于认证，一共有三种认证方式
                            //password 本地口令认证;
                            //scheme 本地或远端用户名和口令认证;
                           //none 不认证;
    
[H3C-line-vty0-4]quit
本地用户的创建与配置


[H3C]local-user admin           //设置创建本地认证的用户名
[H3C-luser-manage-admin]password simple 123456           
                                               //设置明文密码，使用命令查看当前配置时
                                               //密码会以哈希加密后显示 图3
[H3C-luser-manage-admin]authorization-attribute user-role level-15 #开启最高权限或authorization-attribute user-role network-admin 
[H3C-luser-manage-admin]service-type telnet      //用户作用于telnet服务
[H3C-luser-manage-admin]quit
[H3C]telnet server enable   //开启telnet 服务
[H3C]save                           //保存配置

静态路由

[R1]int g0/0 #进入接口或者vlan
[R1-GigabitEthernet0/0]ip add 192.168.1.1 24 #设置接口IP
[R1]ip route-static 192.168.2.0 24 192.168.1.2 #为目标网段设置网关

普通DHCP配置
360截图18720119787582.png
![](https://90apt.com/usr/uploads/2021/04/3603335211.png)

路由1

<H3C>sys #系统视图
[H3C]int g0/0 #进入接口
[H3C-GigabitEthernet0/0]ip add 192.168.1.1 24 #配置IP
[H3C-GigabitEthernet0/1]int g0/1
[H3C-GigabitEthernet0/1]ip add 192.168.2.1 24
路由2

<H3C>sys
[H3C]int g0/0
[H3C-GigabitEthernet0/0]ip add 192.168.2.2 24
路由1

[route1]dhcp server ip-pool 1 #设置DHCP地址池
[route1-dhcp-pool-1]network 192.168.0.1 mask 255.255.255.0 #地址范围为192.168.0.0/24网段的ip地址
[route1-dhcp-pool-1]gateway-list 192.168.0.1 #网关地址为192.168.0.1 
[route1-dhcp-pool-1]dns-list 192.168.0.1 #DNS服务器地址也为192.168.0.1 注意：这里设置的是一个网段的范围，在这个地址范围里可能这里面的某些地址不能够被分配出去。比如说网关的地址和一些指定的设备的ip地址。
[route1]dhcp server forbidden-ip 192.168.0.1 192.168.0.2 #不允许网关地址和DNS地址192.168.0.1分配被出去
[route1]dhcp enable #启动DHCP服务

ospf配置：
参考：https://blog.51cto.com/14219797/2402420

![](https://90apt.com/usr/uploads/2021/03/4177530111.png)

360截图17290429130352.png
![](https://90apt.com/usr/uploads/2021/04/2116553054.png)
QQ截图20210408204835.png

配置接口IP：SwitchB

<H3C>sys #进入系统视图
[H3C]hostname SwitchB #主机名重命名
[SwitchB]vlan 200 进入vlan200
[SwitchB-vlan100]port g 1/0/1 #指定vlan200端口
[SwitchB-vlan100]quit
[SwitchB]vlan 300 进入vlan300
[SwitchB-vlan200]port g 1/0/2 #指定vlan300端口
[SwitchB-vlan200]quit
[SwitchB]inter vlan 200 #进入vlan200
[SwitchB-Vlan-interface100]ip add 10.1.1.2 24 #设定IP
[SwitchB-Vlan-interface100]quit
[SwitchB]inter vlan 300 #进入vlan300
[SwitchB-Vlan-interface200]ip add 10.2.1.1 24 #设定IP
[SwitchB-Vlan-interface200]quit
配置OSPF协议：

<SwitchB> system-view #进入系统视图
[SwitchB] router id 10.2.1.1 #设定唯一标识
[SwitchB] ospf #进入ospf设置
[SwitchB-ospf-1] area 0 #配置区域0
[SwitchB-ospf-1-area-0.0.0.0] network 10.1.1.0 0.0.0.255 #通告网络，子网掩码为反码
[SwitchB-ospf-1-area-0.0.0.0] quit
[SwitchB-ospf-1] area 2 #配置区域
[SwitchB-ospf-1-area-0.0.0.2] network 10.2.1.0 0.0.0.255 #通告网络
[SwitchB-ospf-1-area-0.0.0.2] quit
[SwitchB-ospf-1] quit
SwitchA 的 OSPF 邻居：

[SwitchA]display ospf peer verbose
    
             OSPF Process 1 with Router ID 10.1.1.1
                     Neighbors
    
    
     Area 0.0.0.0 interface 10.1.1.1(Vlan-interface200)'s neighbors
     Router ID: 10.2.1.1         Address: 10.1.1.2         GR state: Normal
       State: Full  Mode: Nbr is master  Priority: 1
       DR: 10.1.1.2  BDR: 10.1.1.1  MTU: 0
       Options is 0x42 (-|O|-|-|-|-|E|-)
       Dead timer due in 31  sec
       Neighbor is up for 00:37:39
       Authentication sequence: [ 0 ]
       Neighbor state change count: 6
       BFD status: Disabled
    
    
     Area 0.0.0.1 interface 10.3.1.1(Vlan-interface100)'s neighbors
     Router ID: 10.3.1.2         Address: 10.3.1.2         GR state: Normal
       State: Full  Mode: Nbr is master  Priority: 1
       DR: 10.3.1.1  BDR: 10.3.1.2  MTU: 0
       Options is 0x42 (-|O|-|-|-|-|E|-)
       Dead timer due in 39  sec
       Neighbor is up for 00:36:50
       Authentication sequence: [ 0 ]
       Neighbor state change count: 5
       BFD status: Disabled
SwitchA 的 OSPF 路由信息：

[SwitchA]display  ospf routing
    
             OSPF Process 1 with Router ID 10.1.1.1
                      Routing Table
    
                    Topology base (MTID 0)
    
     Routing for network
     Destination        Cost     Type    NextHop         AdvRouter       Area
     10.2.1.0/24        2        Inter   10.1.1.2        10.2.1.1        0.0.0.0
     10.3.1.0/24        1        Transit 0.0.0.0         10.1.1.1        0.0.0.1
     10.1.1.0/24        1        Transit 0.0.0.0         10.2.1.1        0.0.0.0
    
     Total nets: 3
     Intra area: 2  Inter area: 1  ASE: 0  NSSA: 0
SwitchC到SwitchD进行测试连通性：

[SwitchC]ping 10.2.1.2
Ping 10.2.1.2 (10.2.1.2): 56 data bytes, press CTRL_C to break
56 bytes from 10.2.1.2: icmp_seq=0 ttl=253 time=1.651 ms
56 bytes from 10.2.1.2: icmp_seq=1 ttl=253 time=1.567 ms
56 bytes from 10.2.1.2: icmp_seq=2 ttl=253 time=1.465 ms
56 bytes from 10.2.1.2: icmp_seq=3 ttl=253 time=1.431 ms
56 bytes from 10.2.1.2: icmp_seq=4 ttl=253 time=2.635 ms
SwitchC到SwitchD进行路由追踪：

[SwitchC]tracert 10.2.1.2
traceroute to 10.2.1.2 (10.2.1.2), 30 hops at most, 40 bytes each packet, press CTRL_C to break
1  10.3.1.1 (10.3.1.1)  1.438 ms  0.424 ms  0.418 ms
2  10.1.1.2 (10.1.1.2)  1.481 ms  1.221 ms  0.695 ms
3  10.2.1.2 (10.2.1.2)  1.073 ms  1.087 ms  0.923 ms

VLAN隔离：
参考链接：
https://blog.51cto.com/14220513/2367688
http://www.023wg.com/vlan/132.html
http://www.h3c.com/cn/d_200809/615974_30005_0.htm
360截图18141221688261.png
![](https://90apt.com/usr/uploads/2021/03/1722034808.png)

配置SwitchA:

<SwitchA> system-view #系统视图
[SwitchA] vlan 100
[SwitchA-vlan100] port ge1/0/2 #添加端口
[SwitchA-vlan100] quit
[SwitchA] vlan 200
[SwitchA-vlan100] port ge1/0/3 #添加端口
[SwitchA-vlan100] quit
[SwitchA] interface ge1/0/1 #进入端口
[SwitchA-GigabitEthernet1/0/1] port link-type trunk #设置trunk模式
[SwitchA-GigabitEthernet1/0/1] port trunk permit vlan 100 200 #允许VLAN100 200通过
输入display vlan 100 和display vlan200 查看配置:

[SwtichA]display vlan 100
     VLAN ID: 100
     VLAN type: Static
     Route interface: Not configured
     Description: VLAN 0100
     Name: VLAN 0100
     Tagged ports:
        GigabitEthernet1/0/1
     Untagged ports:
        GigabitEthernet1/0/2
    
[SwtichA]display vlan 200
     VLAN ID: 200
     VLAN type: Static
     Route interface: Not configured
     Description: VLAN 0200
     Name: VLAN 0200
     Tagged ports:
        GigabitEthernet1/0/1
     Untagged ports:
        GigabitEthernet1/0/3

MSTP多生成树
MSTP默认开启，可手动配置最佳路径
参考链接：
https://www.cnblogs.com/aqicheng/p/13824682.html
QQ截图20210405212524.png
![](https://90apt.com/usr/uploads/2021/04/1040464455.png)
每个交换机创建vlan10 vlan20

[H3C]vlan 10
[H3C-vlan10]vlan 20
[H3C-vlan20]int g1/0/1
[H3C-GigabitEthernet1/0/1]port link-type trunk #所有接口设置trunk模式
[H3C-GigabitEthernet1/0/1]port trunk permit  vlan all #允许所有vlan通过
[H3C-GigabitEthernet1/0/1]int g 1/0/2
[H3C-GigabitEthernet1/0/2]port link-type trunk
[H3C-GigabitEthernet1/0/2]port trunk permit  vlan all
[H3C-GigabitEthernet1/0/2]quit
[H3C]hostname sw3
设置区域

[sw3]stp region-configuration
[sw3-mst-region]region-name h3c #区域命名
[sw3-mst-region]instance 1 vlan 10 #vlan10划入1组
[sw3-mst-region]instance 2 vlan 20 #vlan20划入2组
[sw3-mst-region]active region-configuration #激活配置
[sw3-mst-region]display this #查看以上配置
#
stp region-configuration
     region-name h3c
     instance 1 vlan 10
     instance 2 vlan 20
     active region-configuration
    #
    return
调整根桥

[sw1]stp instance 1 root primary #sw1设置为组1的根桥
[sw2]stp instance 2 root primary #sw2设置为组2的根桥
查看结果

<sw1>display stp brief
     MST ID   Port                                Role  STP State   Protection
     0        GigabitEthernet1/0/1                DESI  FORWARDING  NONE
     0        GigabitEthernet1/0/2                DESI  FORWARDING  NONE
     0        GigabitEthernet1/0/3                DESI  FORWARDING  NONE
     1        GigabitEthernet1/0/1                DESI  FORWARDING  NONE
     1        GigabitEthernet1/0/2                DESI  FORWARDING  NONE
     1        GigabitEthernet1/0/3                DESI  FORWARDING  NONE
     2        GigabitEthernet1/0/1                ROOT  FORWARDING  NONE
     2        GigabitEthernet1/0/2                DESI  FORWARDING  NONE
     2        GigabitEthernet1/0/3                DESI  FORWARDING  NONE

[sw2]display stp brief
     MST ID   Port                                Role  STP State   Protection
     0        GigabitEthernet1/0/1                ROOT  FORWARDING  NONE
     0        GigabitEthernet1/0/2                DESI  FORWARDING  NONE
     1        GigabitEthernet1/0/1                ROOT  FORWARDING  NONE
     1        GigabitEthernet1/0/2                DESI  FORWARDING  NONE
     2        GigabitEthernet1/0/1                DESI  FORWARDING  NONE
     2        GigabitEthernet1/0/2                DESI  FORWARDING  NONE

VRRP虚拟路由冗余协议
参考链接：https://www.cnblogs.com/hukey/p/13071447.html
360截图18720123326445.png
![](https://90apt.com/usr/uploads/2021/04/2707875104.png)
配置心跳线双线冗余

[SW1]int Bridge-Aggregation 1 #创建接口聚合
[SW1]int g1/0/2
[SW1-GigabitEthernet1/0/2]port link-aggregation group 1 #端口加入链路聚合
[SW1-GigabitEthernet1/0/2]int g1/0/3
[SW1-GigabitEthernet1/0/3]port link-aggregation group 1
[SW1]int Bridge-Aggregation 1
[SW1-Bridge-Aggregation1]port link-type trunk #端口允许所有vlan通过
[SW1-Bridge-Aggregation1]port trunk permit vlan all

[SW2]int Bridge-Aggregation 1 #创建链路聚合
[SW2]int g1/0/2
[SW2-GigabitEthernet1/0/2]port link-aggregation group 1 #端口加入链路聚合
[SW2-GigabitEthernet1/0/2]int g1/0/3
[SW2-GigabitEthernet1/0/3]port link-aggregation group 1
[SW2]int Bridge-Aggregation 1
[SW2-Bridge-Aggregation1]port link-type trunk #端口允许所有vlan通过
[SW2-Bridge-Aggregation1]port trunk permit vlan all
查看绑定状态

[sw1]dis int Bridge-Aggregation bri
    Brief information on interfaces in bridge mode:
    Link: ADM - administratively down; Stby - standby
    Speed: (a) - auto
    Duplex: (a)/A - auto; H - half; F - full
    Type: A - access; T - trunk; H - hybrid
    Interface            Link Speed   Duplex Type PVID Description
    BAGG1                UP   2G(a)   F(a)   T    1
配置vlan

SW3对应SW2和SW1的两个端口配置trunk，对应客户机的端口配置vlan
[SW3]vlan 10
[SW3-vlan10]port g1/0/1
    
[SW3]int range g1/0/2 to g1/0/3
[SW3-if-range]port link-type trunk
[SW3-if-range]port trunk permit vlan 10 20


[SW1]vlan 10 #创建vlan
[SW1-vlan10]vlan 20 #创建vlan
[SW1-vlan0]int g1/0/1
[SW1-GigabitEthernet1/0/1]port link-type trunk
[SW1-GigabitEthernet1/0/1]port trunk permit vlan 10 20
[SW1-GigabitEthernet1/0/1]int vlan 10 #进入vlan10
[SW1-Vlan-interface10]ip add 10.0.10.253 24 #设置IP
[SW1-Vlan-interface10]int v20 #进入vlan20
[SW1-Vlan-interface20]ip add 10.0.20.253 24 #设置IP
[SW1]dis ip int bri
[sw1]dis ip int bri
*down: administratively down
(s): spoofing  (l): loopback
Interface                Physical Protocol IP Address      Description
MGE0/0/0                 down     down     --              --
Vlan10                   up       up       10.0.10.253     --
Vlan20                   up       up       10.0.20.253     --


[SW2]vlan 10
[SW2-vlan10]vlan 20
[SW2-vlan20]int g1/0/1
[SW2-GigabitEthernet1/0/1]port link-type trunk
[SW2-GigabitEthernet1/0/1]port trunk permit vlan 10 20
[SW2-GigabitEthernet1/0/1]int v10
[SW2-Vlan-interface10]ip add 10.0.10.252 24
[SW2-Vlan-interface10]int v20
[SW2-Vlan-interface20]ip add 10.0.20.252 24
[SW2]dis ip int bri
*down: administratively down
(s): spoofing  (l): loopback
Interface                Physical Protocol IP Address      Description
MGE0/0/0                 down     down     --              --
Vlan10                   up       up       10.0.10.252     --
Vlan20                   up       up       10.0.20.252     --
配置VRRP

配置vlan10的vrrp
[SW1]int v10 #进入vlan10
[SW1-Vlan-interface10]vrrp vrid 10 virtual-ip 10.0.10.254 #配置虚拟地址
[SW1-Vlan-interface10]vrrp vrid 10 priority 105 # 配置vrrp权重，默认为100 如果要设置master则大于100即
[SW1]track 10 int Bridge-Aggregation 1 # 配置心跳线为聚合链路
    
    
[SW2]int v10
[SW2-Vlan-interface10]vrrp vrid 10 virtual-ip 10.0.10.254
[SW2]track 10 int Bridge-Aggregation 1
    
    

配置vlan20的vrrp
[SW1]int v20
[SW1-Vlan-interface20]vrrp vrid 20 virtual-ip 10.0.20.254
[SW1]track 20 int Bridge-Aggregation 1 #配置心跳线为聚合链路
    
[SW2]int v20
[SW2-Vlan-interface20]vrrp vrid 20 virtual-ip 10.0.20.254
[SW2-Vlan-interface20]vrrp vrid 20 priority 105 #设置为vlan20的master
[SW2]track 20 int Bridge-Aggregation 1 #配置心跳线为聚合链路

[sw1]dis vrrp
    IPv4 virtual router information:
     Running mode : Standard
     Total number of virtual routers : 2
     Interface          VRID  State       Running Adver     Auth     Virtual
                                          pri     timer(cs) type     IP
     ---------------------------------------------------------------------
     Vlan10             10    Master      105     100       None     10.0.10.254
     Vlan20             20    Backup      100     100       None     10.0.20.254
    
[sw2]dis vrrp
    IPv4 virtual router information:
     Running mode : Standard
     Total number of virtual routers : 2
     Interface          VRID  State       Running Adver     Auth     Virtual
                                          pri     timer(cs) type     IP
     ---------------------------------------------------------------------
     Vlan10             10    Backup      100     100       None     10.0.10.254
     Vlan20             20    Master      105     100       None     10.0.20.254

堆叠
参考链接：
CSDN博主「猫先生的早茶」的原创文章
https://blog.csdn.net/qq_43017750/article/details/89323450
QQ图片20210408211517.png
![](https://90apt.com/usr/uploads/2021/04/1106016578.png)
注意！
1、配置前必须移除两路由间连接线
2、全部配置完成后，连线，次路由会自动重启

master交换机

#sys
#interface  range  Ten-GigabitEthernet  1/0/49 to  Ten-GigabitEthernet 1/0/52 #批量管理端口
#shutdown #关闭端口
#quit 
#irf member 1 priority  32 #配置irf成员优先级，32为最高，默认是1
#irf-port 1/1 #进入irf端口1/1
#port group  interface  Ten-GigabitEthernet  1/0/49 #加入当前irf端口
#port group  interface  Ten-GigabitEthernet  1/0/50
#port group  interface  Ten-GigabitEthernet  1/0/51
#port group  interface  Ten-GigabitEthernet  1/0/52
#quit
#irf-port-configuration active #激活irf配置
#interface range Ten-GigabitEthernet  1/0/49 to Ten-GigabitEthernet  1/0/52 #批量管理端口
#undo shutdown #启动端口
#save #保存
standby交换机的命令

#sys
#irf member 1 renumber  2 #当前irf成员id重命名为2
#quit
#reboot
#sys
#interface  range Ten-GigabitEthernet  2/0/49 to  Ten-GigabitEthernet  2/0/52 #批量管理端口
#shutdown #关闭端口
#quit
#irf member 2 priority  1 #配置当前irf成员id2的优先级为1
#irf-port 2/2 #进入irf端口2/2
#port group  interface Ten-GigabitEthernet  2/0/49 #加入当前irf
#port group  interface Ten-GigabitEthernet  2/0/50
#port group  interface Ten-GigabitEthernet  2/0/51
#port group  interface Ten-GigabitEthernet  2/0/52
#quit
#irf-port-configuration active #激活irf
#interface  range Ten-GigabitEthernet  2/0/49 to Ten-GigabitEthernet  2/0/52 #批量管理端口
#undo  shutdown #启动端口
#quit
#save
验证：

[sw1]dis irf
MemberID    Role    Priority  CPU-Mac         Description
*+1        Master  32        30e7-b21f-0104  ---
  2        Standby 1         30e7-bae6-0204  ---

两台三层交换机快速IRF配置
member 1编号
renumber 2重写编号
domain 0主机名
priority 18优先级，大的为主
irf-port2 irf组，主为1则从为2，1接2


easy-irf member 1 domain 0 priority 24 irf-port1 FortyGigE 1/0/53 FortyGigE 1/0/54
*****************************************************************************
                  Configuration summary for member 1
IRF new member ID: 1
IRF domain ID    : 0
IRF priority     : 24
IRF-port 1       : FortyGigE1/0/53, FortyGigE1/0/54
IRF-port 2       : Disabled
*****************************************************************************

easy-irf member 1 renumber 2 domain 0 priority 18 irf-port2 FortyGigE 1/0/53 FortyGigE 1/0/54
*****************************************************************************
                  Configuration summary for member 1
IRF new member ID: 2
IRF domain ID    : 0
IRF priority     : 18
IRF-port 1       : Disabled
IRF-port 2       : FortyGigE1/0/53, FortyGigE1/0/54
*****************************************************************************
IRF保留接口（上行三层互联口）
IRF分裂后，保持三层接口通讯

mad exclude interface Ten-GigabitEthernet 2/0/50
删除IRF
删除irf-port接口

un irf-port xx
重置编号

irf member 2 renumber 1
OSPF配置

在接口使能ospf

int xx
ospf 1 area 0
接口配置为p2p模式

ospf network-type p2p
在vlan使能ospf

int vlan xx
ospf 1 area 1
在loopback 0使能ospf

int loopback 0
ospf 1 area 0
堆叠心跳检测BFD MAD
新建用于irf检测的vlan

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
OSPF优化
配置OSPF ID

int LoopBack 0
ip add 10.100.0.1 32
qu
配置指定接口收发OSPF报文

ospf 1
silent-interface all #关闭全部接口的OSPF报文收发
undo silent-interface Ten-GigabitEthernet 1/0/49

链路聚合
原文链接：https://blog.csdn.net/VictoryKingLIU/article/details/79560157

二层端口静态聚合模式

<H3C>system-view
[H3C]int Bridge-Aggregation 1
[H3C-Bridge-Aggregation1]quit
[H3C]int GigabitEthernet 1/0/1
[H3C-GigabitEthernet1/0/1]port link-aggregation group 1
[H3C-GigabitEthernet1/0/1]int GigabitEthernet 1/0/2
[H3C-GigabitEthernet1/0/2]port link-aggregation group 1
[H3C-GigabitEthernet1/0/2]int GigabitEthernet 1/0/3
[H3C-GigabitEthernet1/0/3]port link-aggregation group 1
[H3C]dis link-aggregation verbose
二层端口动态聚合模式

<H3C>system-view
[H3C]int Bridge-Aggregation 1
[H3C-Bridge-Aggregation1]link-aggregation mode dynamic
[H3C-Bridge-Aggregation1]quit
[H3C]int GigabitEthernet 1/0/1
[H3C-GigabitEthernet1/0/1]port link-aggregation group 1
[H3C-GigabitEthernet1/0/1]int GigabitEthernet 1/0/2
[H3C-GigabitEthernet1/0/2]port link-aggregation group 1
[H3C-GigabitEthernet1/0/2]int GigabitEthernet 1/0/3
[H3C-GigabitEthernet1/0/3]port link-aggregation group 1
    
[H3C]dis link-aggregation verbose
三层端口静态聚合

创建端口三层聚合口

[SW1]interface Route-Aggregation 1

分别把GE1/0/11,GE1/0/12加入到聚合组1

[SW1]interface GigabitEthernet 1/0/11
[SW1-GigabitEthernet1/0/11]port link-mode route
[SW1-GigabitEthernet1/0/11]port link-aggregation group 1

[SW1]interface GigabitEthernet 1/0/12
[SW1-GigabitEthernet1/0/12]port link-mode route
[SW1-GigabitEthernet1/0/12]port link-aggregation group 1

pvid 不同VLAN间通讯
QQ截图20210512195025.png

![](https://90apt.com/usr/uploads/2021/05/4017109286.png)


SW1：
    interface GigabitEthernet1/0/1
     port link-mode bridge
     port access vlan 100
     combo enable fiber
    #
    interface GigabitEthernet1/0/2
     port link-mode bridge
     port link-type trunk
     port trunk permit vlan 1 100
     port trunk pvid vlan 100
     combo enable fiber
    #
    
SW2：
    interface GigabitEthernet1/0/1
     port link-mode bridge
     port access vlan 200
     combo enable fiber
    #
    interface GigabitEthernet1/0/2
     port link-mode bridge
     port link-type trunk
     port trunk permit vlan 1 200
     port trunk pvid vlan 200
     combo enable fiber
    #

ACL控制规则

QQ截图20210513210718.png
![](https://90apt.com/usr/uploads/2021/05/471858460.png)

[H3C]acl basic 2000  #创建基础规则
[H3C-acl-ipv4-basic-2000]rule deny source 192.168.1.2 0   #编写规则内容，阻止来自192.168.1.2的包
[H3C-acl-ipv4-basic-2000]int g1/0/1   #进入接口
[H3C-GigabitEthernet1/0/1]packet-filter 2000 inbound  #应用规则 inbound入站 outbound出站
在本案例中，若要禁止192.168.1.1访问2，需要在G1/0/1应用 outbound出站规则，这样数据包在抵达2并返回到接口时会被阻止；要禁止全体访问2，则需要在G1/0/2应用inbound入站规则，这样数据包从2出发并经过接口时会被阻止。

端口控制
若要阻止vlan1所有telnet访问，则可以在vlan1中设置出站规则
QQ截图20210513213915.png
![](https://90apt.com/usr/uploads/2021/05/2680245514.png)
[H3C]acl advanced 3001
[H3C-acl-ipv4-adv-3001]rule 1 deny tcp source-port eq 23
[H3C-acl-ipv4-adv-3001]int vlan1
[H3C-Vlan-interface1]packet-filter 3001 outbound
若仅要阻止1.5或网段的telnet访问，则可以设置入站规则

[H3C-acl-ipv4-adv-3002]rule 1 deny tcp source 192.168.1.5 0 destination-port eq 23
[H3C-Vlan-interface1]packet-filter 3002 inbound

端口隔离
参考连接：https://blog.csdn.net/weixin_34110749/article/details/92738677
（特别注明：模拟器中端口隔离功能不起作用）

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
QQ图片20210524210456.png
![](https://90apt.com/usr/uploads/2021/05/803650286.png)

IRF堆叠LACP-MAD检测
参考链接：https://blog.csdn.net/qq_45662411/article/details/105983636
开启前，堆叠线断开后两设备都成为master在线，影响网络运行；开启后，LACP-MAD协议会控制在线的成员关闭端口，只保留一个master，防止网络冲突。
QQ图片20210525193359.png
![](https://90apt.com/usr/uploads/2021/05/3825677014.png)
QQ图片20210525193405.png
![](https://90apt.com/usr/uploads/2021/05/3801046702.png)
IRF设备配置：
[master]int Bridge-Aggregation 2 #创建一个名为2的聚合端口组
[master-Bridge-Aggregation2]link-aggregation mode dynamic  #将此端口组的模式改为动态
[master-Bridge-Aggregation2]mad enable #开启mad检测
[master-Bridge-Aggregation2]quit  #退出接口视图
[master]int range g1/0/1 g2/0/1  #同时进入这两个接口
[master-if-range]port link-aggregation group 2 #将他们加入到这个接口组2中

下层配置：
[H3C]int Bridge-Aggregation 2  #创建一个名为2的聚合端口组
[H3C-Bridge-Aggregation2]link-aggregation mode dynamic   #将此端口组的模式改为动态
[H3C-Bridge-Aggregation2]quit #退出接口视图
[H3C]int range g1/0/1 g1/0/2  #进入到这两个接口
[H3C-if-range]port link-aggregation group 2  #将这两个端口组加入到接口组2中

AP三层上线
所属VLAN配置DHCP Option43

option43格式简要说明：
80 07 00 00 01 02 02 02 02
80：固定值，不用改变；
07：长度字段，其后面所跟数据的字节长度；
00 00：固定值，不用改变；
01：表示后面的IP地址的个数，此处为一个IP地址；
02 02 02 02：IP地址
转换工具https://tool.520101.com/wangluo/jinzhizhuanhuan/

dhcp server ip-pool vlan5
 gateway-list 192.168.1.1
 network 192.168.1.0 mask 255.255.255.0
 dns-list 114.114.114.114
 option 43 hex 800700000103030302
本地转发性能更高更灵活，通过AC管理。
AP接口如果是access，则ap和客户端在同一个VLAN。
AP接口如果是trunk，则可以分别配置VLAN。

企业微信截图_16532923584186.png
![](https://90apt.com/usr/uploads/2022/05/2616170829.png)

UEFI PXE网络启动

dhcp server ip-pool test
 gateway-list 192.168.1.254
 network 192.168.1.0 mask 255.255.255.0
 bootfile-name \\Boot\\x64\\wdsmgfw.efi  #传统引导为\\Boot\\x64\\wdsnbp.com
 dns-list 114.114.114.114
 next-server 192.168.100.100 #WDS服务器
学习实验
acl策略
QQ图片20210517200056.png
![](https://90apt.com/usr/uploads/2021/05/631298859.png)
PVID
QQ图片20210517200103.png
![](https://90apt.com/usr/uploads/2021/05/3814974329.png)
路由口端口汇聚
QQ图片20210517200111.png
![](https://90apt.com/usr/uploads/2021/05/629043675.png)
堆叠、汇聚、ospf
QQ图片20210517200115.png
![](https://90apt.com/usr/uploads/2021/05/105739362.png)
静态路
QQ图片20210517200123.png
![](https://90apt.com/usr/uploads/2021/05/3085569477.png)
五机堆叠
QQ图片20210517200130.png
![](https://90apt.com/usr/uploads/2021/05/2956096583.png)
三机堆叠
QQ图片20210517200137.png

![](https://90apt.com/usr/uploads/2021/05/2521711192.png)
