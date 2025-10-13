# H3C无线控制器AC主备配置、AP license共享配置 、DHCP服务器Option 43配置

2025-03-20

已收录
注意：HCL模拟器当前无法完成AP license共享，本文为虚拟机+实际设备演示，虚拟机中无法执行的命令从实际设备中演示。

一、环境介绍
0、需求
两台无线AC，进行主备配置，希望在AC1宕机掉线的时候，AP自动切换到AC2上，减小网络故障时间，AC license授权进行共享。

1、运行环境
新华三模拟器HCL v5.10.3
实际设备 WX3510X两台

2、地址规划
AC1管理地址VLAN1 192.168.56.11 模拟器中AC1网页管理地址
AC2管理地址VLAN1 192.168.56.12 模拟器中AC2网页管理地址
AC1网络地址VLAN100 192.168.100.11 DHCP服务器Option 43配置地址
AC2网络地址VLAN100 192.168.100.12 DHCP服务器Option 43配置地址
PC地址VLAN110 192.168.110.2 手动获取
AP地址VLAN120 192.168.120.x 自动获取
phone地址VLAN130 192.168.130.x 自动获取
核心交换机 作为上述设备的网关
wifi SSID名称 haha 无加密，模拟器不支持输入密码，不支持自动重连wifi

3、网络拓扑
企业微信截图_20250219093114.jpg

4、前期配置 仅主要配置
核心交换机配置

 sysname core

 dhcp enable

vlan 1
#
vlan 100
#
vlan 110
#
vlan 120
#
vlan 130
#

#
dhcp server ip-pool 120
 gateway-list 192.168.120.1
 network 192.168.120.0 mask 255.255.255.0
 option 43 hex 800b000002c0a8640bc0a8640c
#
dhcp server ip-pool 130
 gateway-list 192.168.130.1
 network 192.168.130.0 mask 255.255.255.0

#
interface Vlan-interface100
 ip address 192.168.100.1 255.255.255.0
#
interface Vlan-interface110
 ip address 192.168.110.1 255.255.255.0
#
interface Vlan-interface120
 ip address 192.168.120.1 255.255.255.0
#
interface Vlan-interface130
 ip address 192.168.130.1 255.255.255.0

#
interface GigabitEthernet1/0/11
 port link-mode bridge
 port link-type trunk
 port trunk permit vlan all
 combo enable fiber
#
interface GigabitEthernet1/0/12
 port link-mode bridge
 port link-type trunk
 port trunk permit vlan all
 combo enable fiber

#
interface GigabitEthernet1/0/21
 port link-mode bridge
 port access vlan 120
 combo enable fiber

#
interface GigabitEthernet1/0/31
 port link-mode bridge
 port access vlan 110
 combo enable fiber
AC1配置


 sysname AC1

#
vlan 1
#
vlan 100
#
vlan 130
#
wlan service-template haha
 ssid haha
 vlan 130
 service-template enable

#
interface Vlan-interface1
 ip address 192.168.56.11 255.255.255.0
#
interface Vlan-interface100
 ip address 192.168.100.11 255.255.255.0
#
interface GigabitEthernet1/0/0
 port link-mode bridge
 combo enable fiber

#
interface GigabitEthernet1/0/11
 port link-mode bridge
 port link-type trunk
 port trunk permit vlan all
 combo enable fiber

#
 ip route-static 0.0.0.0 0 192.168.100.1
line vty 0 31
 user-role network-operator
 sys
 
user-group system
#
local-user admin class manage
 password hash 密码
 service-type http
 authorization-attribute user-role level-15
 authorization-attribute user-role network-operator
#
local-user ip class manage
 authorization-attribute user-role network-operator
#
 ip http enable
#
 wlan auto-ap enable
 wlan auto-persistent enable
#
wlan ap-group default-group
 vlan 1
#
wlan virtual-ap-group default-virtualapgroup
#
wlan ap ap1 model WA6320-HCL
 serial-id H3C_66-C9-40-45-04-00
 firmware-upgrade enable
 vlan 1
 radio 1
  radio enable
  service-template haha
 radio 2
  radio enable
  service-template haha
 gigabitethernet 1
#
return
AC2配置


 sysname AC2

#
vlan 1
#
vlan 100
#
vlan 130
#
wlan service-template haha
 ssid haha
 vlan 130
 service-template enable

#
interface Vlan-interface1
 ip address 192.168.56.12 255.255.255.0
#
interface Vlan-interface100
 ip address 192.168.100.12 255.255.255.0
#
interface GigabitEthernet1/0/0
 port link-mode bridge
 combo enable fiber

#
interface GigabitEthernet1/0/12
 port link-mode bridge
 port link-type trunk
 undo port trunk permit vlan 1
 port trunk permit vlan 2 to 4094
 combo enable fiber

line vty 0 31
 user-role network-operator

#
user-group system
#
local-user admin class manage
 password hash 密码
 service-type http
 authorization-attribute user-role level-15
 authorization-attribute user-role network-operator
#
 ip http enable
#
 wlan auto-ap enable
 wlan auto-persistent enable
#
wlan ap-group default-group
 vlan 1

#
wlan ap ap1 model WA6320-HCL
 serial-id H3C_66-C9-40-45-04-00
 firmware-upgrade enable
 vlan 1
 radio 1
  radio enable
  service-template haha
 radio 2
  radio enable
  service-template haha
 gigabitethernet 1
#
return
PC
手动固定IP
企业微信截图_20250219100349.jpg

phone
链接wifi，自动获取IP
企业微信截图_20250219100414.jpg

测试
前期配置后，能够完成设备的PC ping 通phone
企业微信截图_20250219100438.jpg

二、AC主备配置
主AC配置
在我文章演示的环境中，希望主AC的全部AP都能切换到备用AC中，那么主AC仅需要配置默认AP组的优先级即可，配置优先级为7(默认为4)，配置备用AC地址，配置CAPWAP主动隧道抢占功能。

wlan ap-group default-group
 priority 7
 backup-ac ip  192.168.100.12
  wlan tunnel-preempt enable
备用AC配置
在我文章演示的环境中，备用AC需在默认AP组中声明自己是主AC的备份，配置CAPWAP主动隧道抢占功能。

wlan ap-group default-group
 backup-ac ip 192.168.100.11
 wlan tunnel-preempt enable
二、AP license共享
模拟器中无法执行，配置为实际设备
local 为自身
member 为成员

主AC配置

wlan ap-license-group
 local ip 192.168.100.11
 member ip 192.168.100.12
 ap-license-synchronization enable
备用AC配置

wlan ap-license-group
 local ip 192.168.100.12
 member ip 192.168.100.11
 ap-license-synchronization enable
三、DHCP服务器Option 43配置
如使用H3C作为DHCP服务器，则Option43配置如下

H3C官方手册
https://www.h3c.com/cn/Service/Document_Software/Document_Center/Home/Server/00-Public/Configure/Typical_Configuration_Example/DHCP_Option_43_CE-9895/#

示例1：

采用PXE格式配置1个AC IPv4地址时（AC：10.23.200.1），Option选项内容各字段的配置如下。

·     80：Sub-option type，表示PXE格式的固定值。

·     07：Sub-option length，此处后面所跟数据为0000010a17c801，所以长度为7个字节。

·     0000：固定值，不可改变。

·     01：表示其后面AC IPv4地址的个数，此处为1个IPv4地址。

·     0a17c801：表示AC的十六进制IPv4地址。

最终在DHCP服务器的DHCP地址池视图下配置为option 43 hex 80070000010a17c801。

 

示例2：

采用PXE格式配置2个AC IPv4地址时（AC 1：10.23.200.1，AC 2：10.23.200.2），Option选项内容各字段的配置如下。

·     80：Sub-option type，表示PXE格式的固定值。

·     0b：Sub-option length，此处后面所跟数据为0000020a17c8010a17c802，所以长度为11个字节。

·     0000：固定值，不可改变。

·     02：表示其后面AC IPv4地址的个数，此处为2个IPv4地址。

·     0a17c8010a17c802：0a17c801表示AC 1的十六进制IPv4地址，0a17c802表示AC2的十六进制IPv4地址。

最终在DHCP服务器的DHCP地址池视图下配置为option 43 hex 800b0000020a17c8010a17c802。
核心交换机实际配置，IP转换十六进制请自行百度

dhcp server ip-pool 120
 gateway-list 192.168.120.1
 network 192.168.120.0 mask 255.255.255.0
 option 43 hex 800b000002c0a8640bc0a8640c
四、配置文件手动同步
在本文中的AC主备配置、AP license共享配置，是无法进行主备AC配置同步的，需要手动在备用AC中添加AP

每次上线新AP，都需要手动在AC2中创建一次AP

第一步
将备用AC中MAC上线的名字修改为主AC中AP的名字

wlan rename-ap 66C9-4045-0400 ap1
第二步
复制AC1中ap1的配置文件覆盖到AC2

wlan ap ap1 model WA6320-HCL
 serial-id H3C_66-C9-40-45-04-00
 firmware-upgrade enable
 vlan 1
 radio 1
  radio enable
  service-template haha
 radio 2
  radio enable
  service-template haha
 gigabitethernet 1
五、测试
PC持续ping phone

企业微信截图_20250219102117.jpg

当前AP在主AC上线，左侧为主
企业微信截图_20250219103136.jpg

此时断开主AC网络
企业微信截图_20250219103223.jpg

AP会在60秒内切换到备用AC上线
企业微信截图_20250219103519.jpg

因为模拟器不支持phone自动连接，所以需要手动让phone再次连接网络，随后ping恢复
企业微信截图_20250219103544.jpg

主AC恢复
因模拟器限制无法演示，理论上主AC优先级高，主AC恢复后，AP会从备用AC返回主AC上线

六、小结
新华三就是好啊

查找资料时发现文档小BUG，已反馈H3C
企业微信截图_17398688621946.png

暂无标签
 版权属于： 王忘杰
 本文链接： https://90apt.com/5401
 作品采用： 《 署名-非商业性使用-相同方式共享 4.0 国际 (CC BY-NC-SA 4.0) 》许可协议授权
上一篇
下一篇
评论
博主关闭了所有页面的评论
博主栏壁纸
博主头像
王忘杰
有三分钟热度，就有三分钟收获

296
文章数
179
评论量
人生倒计时
今日已经过去
12
小时
54%
这周已经过去
1
天
14%
本月已经过去
13
天
41%
今年已经过去
10
个月
83%
广告广告
舔狗日记
今天下雨了，我去你公司接你下班。看见我你不耐烦的说“烦不烦啊，不要再找我了”，一头冲进雨里就跑开了。我心里真高兴啊，你宁愿自己淋雨，都不愿让我也淋湿一点，你果然还是爱我的。
2022 - 2025 © Reach - Joe
鲁公网安备 37021102001178号 鲁ICP备2022015205号
