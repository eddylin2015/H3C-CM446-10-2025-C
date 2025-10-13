# 2023年华三H3C HCL新版模拟器防火墙、AC、AP、Phone、Host新功能使用

当前H3C最新版模拟器加入了防火墙、AC、AP、Phone等新设备，本文重点介绍新设备的使用
lhrf228u.png

整体规划
采用三层网络结构，核心、汇聚、防火墙、AC、DHCP服务器均采用三层链接，AP采用三层上线，管理业务分离

功能实现
1、ospf三层互联，为简化网络，不配置lookback接口
2、配置DHCP服务器，配置DHCP中继，为多个VLAN提供服务
3、外网通过防火墙NAT访问

最终目标
实现Phone通过AP联网并自动获取IP，能够访问外网1.1.1.1

配置步骤

1、配置核心交换机

配置三层接口

interface GigabitEthernet1/0/1
 port link-mode route
 combo enable fiber
 ip address 10.0.0.2 255.255.255.252
#
interface GigabitEthernet1/0/2
 port link-mode route
 combo enable fiber
 ip address 10.0.0.9 255.255.255.252
#
interface GigabitEthernet1/0/3
 port link-mode route
 combo enable fiber
 ip address 10.0.0.6 255.255.255.252
#
interface GigabitEthernet1/0/4
 port link-mode route
 combo enable fiber
 ip address 10.0.0.13 255.255.255.252
配置ospf协议

 ospf 1
 area 0.0.0.0
  network 10.0.0.0 0.0.0.3
  network 10.0.0.4 0.0.0.3
  network 10.0.0.8 0.0.0.3
  network 10.0.0.12 0.0.0.3
2、配置DHCP服务器
使用路由器模拟，配置接口

interface GigabitEthernet1/0/2
 port link-mode route
 combo enable fiber
 ip address 10.0.0.10 255.255.255.252
配置ospf

ospf 1
 area 0.0.0.0
  network 10.0.0.8 0.0.0.3
配置dhcp池

 dhcp enable
dhcp server ip-pool vlan10
 gateway-list 10.0.1.254
 network 10.0.1.0 mask 255.255.255.0
 dns-list 10.0.0.1
#
dhcp server ip-pool vlan20
 gateway-list 10.0.2.254
 network 10.0.2.0 mask 255.255.255.0
 dns-list 10.0.0.1
#
dhcp server ip-pool vlan30
 gateway-list 10.0.3.254
 network 10.0.3.0 mask 255.255.255.0
 dns-list 10.0.0.1
3、配置汇聚交换机
配置VLANIF与三层接口

interface Vlan-interface10
 ip address 10.0.1.254 255.255.255.0
 dhcp select relay
 dhcp relay server-address 10.0.0.10
#
interface Vlan-interface20
 ip address 10.0.2.254 255.255.255.0
 dhcp select relay
 dhcp relay server-address 10.0.0.10
#
interface Vlan-interface30
 ip address 10.0.3.254 255.255.255.0
 dhcp select relay
 dhcp relay server-address 10.0.0.10

#
interface GigabitEthernet1/0/4
 port link-mode route
 combo enable fiber
 ip address 10.0.0.14 255.255.255.252
配置OSPF

 ospf 1
 area 0.0.0.0
  network 10.0.0.12 0.0.0.3
  network 10.0.1.0 0.0.0.255
  network 10.0.2.0 0.0.0.255
  network 10.0.3.0 0.0.0.255
开启DHCP中继服务

 dhcp enable
4、调试pc
配置汇聚交换机对应接口为vlan10

interface GigabitEthernet1/0/6
 port link-mode bridge
 port access vlan 10
 combo enable fiber
PC配置自动获取，成功获取IP
lhrgd0vp.png

5、防火墙
将 本地主机 vbox网卡接口 与 防火墙相连，登陆防火墙，为接口配置IP地址。
我的vbox网卡地址为192.168.56.254
lhrgiap6.png

为防火墙配置192.168.56.2

interface GigabitEthernet1/0/0
 port link-mode route
 combo enable copper
 ip address 192.168.56.2 255.255.255.0
配置acl规则，允许此接口进行web登录

acl advanced 3000
 rule 0 permit ip
为接口引入规则

security-zone name Management
 import interface GigabitEthernet1/0/0
#
zone-pair security source Local destination Management
 packet-filter 3000
#
zone-pair security source Management destination Local
 packet-filter 3000
登录防火墙进行配置 admin admin
lhrgoq89.png

配置内网接口
lhrh0166.png

配置外网接口
lhrh2q4w.png

配置防火墙静态路由，从防火墙返回内网的路由
lhsh1pj7.png

配置防火墙安全策略，所有区域互通
lhsh20jv.png

配置NAT转换
lhsh3j0n.png

配置核心默认路由，声明ospf默认路由

ip route-static 0.0.0.0 0 10.0.0.1

ospf 1
 default-route-advertise
配置外网设备1.1.1.1，配置IP

interface GigabitEthernet0/2
 port link-mode route
 combo enable copper
 ip address 1.1.1.1 255.255.255.0
测试pc与防火墙内外网地址互通，实现通讯

<H3C>ping 1.1.1.2
Ping 1.1.1.2 (1.1.1.2): 56 data bytes, press CTRL_C to break
56 bytes from 1.1.1.2: icmp_seq=0 ttl=253 time=1.000 ms
注意，多外网接口通讯还需配置策略路由
lhsh5rgz.png

防火墙配置至此完成

6、AC配置
断开本地主机与防火墙的连接，将本地主机连接至AC
lhsh7s3b.png

为ac配置IP并配置web登录

[ac]int vlan 1
[ac-Vlan-interface1]ip add 192.168.56.3 24
[ac]ip http en
[ac]local-user admin
New local user added.
[ac-luser-manage-admin]password simple pass@123456
[ac-luser-manage-admin]authorization-attribute  user-role level-15
[ac-luser-manage-admin]service-type http
[ac-luser-manage-admin]save
因模拟器存在bug，将AC接口改为三层接口后所有接口失效，因此为AC和核心交换机配置trunk及vlan，若已经触发bug，需要删除AC重新添加

核心配置

interface Vlan-interface100
 ip address 10.0.0.6 255.255.255.252
interface GigabitEthernet1/0/3
 port link-mode bridge
 port link-type trunk
 port trunk permit vlan all
 combo enable fiber
AC配置

interface Vlan-interface100
 ip address 10.0.0.5 255.255.255.252

interface GigabitEthernet1/0/3
 port link-mode bridge
 port link-type trunk
 port trunk permit vlan all
为AC设置默认路由

ip route-static 0.0.0.0 0 10.0.0.6
使用pc测试，可以与AC通讯

<H3C>ping 10.0.0.5
Ping 10.0.0.5 (10.0.0.5): 56 data bytes, press CTRL_C to break
56 bytes from 10.0.0.5: icmp_seq=0 ttl=253 time=2.000 ms
为DHCP服务器配置option43选项，AP上线网段为VLAN20

option43格式简要说明：
80 07 00 00 01 02 02 02 02
80：固定值，不用改变；
07：长度字段，其后面所跟数据的字节长度；
00 00：固定值，不用改变；
01：表示后面的IP地址的个数，此处为一个IP地址；
02 02 02 02：IP地址
在线转换工具 https://tool.520101.com/wangluo/jinzhizhuanhuan/
10.0.0.5 = A000005
拼接默认字段option 43 hex 80070000010A000005，位数不够补零
DHCP服务器配置

dhcp server ip-pool vlan20
 gateway-list 10.0.2.254
 network 10.0.2.0 mask 255.255.255.0
 dns-list 10.0.0.1
 option 43 hex 80070000010a000005
汇聚为AP对应的接口配置trunk及默认pvid

interface GigabitEthernet1/0/5
 port link-mode bridge
 port link-type trunk
 port trunk permit vlan all
 port trunk pvid vlan 20
重启AP，查看获取IP情况
查看IP

dis int br
Vlan1                UP   UP       10.0.2.1
登录ac，开启自动AP
lhsif3sq.png

进行AP固化及重命名
lhsiggwa.png

创建本地转发网络
lhsikiy1.png

为ap创建map文件，vlan30为业务vlan
自定义名字officecfg.txt，因为接口配置了pvid，所以对ap来说，管理vlan是vlan1，不需要再配置

system-view
vlan 30
quit
interface GigabitEthernet 0/0/0
port link-type trunk
port trunk permit vlan 30
在AP上部署map文件
lhsjci37.png

在AP上部署无线网络，vlan填写业务vlan 30
lhsjcuu7.png

客户端联网测试，成功获取vlan30的ip
lhsje8rs.png

phone ping通外网1.1.1.1，实验完成
lhsjj5d5.png

小结
华三模拟器，牛逼！
