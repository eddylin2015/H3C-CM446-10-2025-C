# Lab15_IntegratedNetwork  

## LabDiagram

![](https://github.com/eddylin2015/H3C-CM446-10-2025-C/blob/main/img/lab15LabDiagram.png?raw=true)

15.2 项目背景

一些问题:

- 网络故障不断,时常出现网络瘫痪现象
- 病毒泛滥,攻击不断
- 总部同办事处发送信息不安全
- 一些员工使用P2P 工具,不能监管
- 公司的一些服务器只能托管,不能放在公司内部

## 15.3 网络规划设计
- 网络带宽升级,达到万兆骨干,千兆到桌面
目前内部网络采用是百兆交换机互连,带宽较小,升级之后变为千兆独享到桌面,总部网络骨干升级为万兆。
- 增强网络的可靠性及可用性
升级之后的整个网络要具备高可用性。网络不会因为单点故障而导致全网瘫痪;设备、拓扑等要有可靠性保障,不会因雷击而出现故障。
- 网络要易于管理、升级和扩展
升级之后的网络要易于管理,要提供图形化的管理界面和故障自动告警措施。另外考虑到公司以后的发展,网络要易于升级和扩展,要满足3~5年内因人员增加、机构增加而扩展网络的需求。
- 确保内网安全及同办事处之间交互数据的安全
升级之后的网络要确保总部和分支机构的内网安全,能够彻底解决ARP 欺骗问题,防止外部对内网的攻击,同时要保证总部和办事处之间传递数据的安全性和可靠性。另外,要能监控和过滤员工发往外部的邮件及员工访问的网站等。
- 服务器管理及访问权限控制,并能监管网络中的P2P 应用
网络升级之后,要将托管在运营商 IDC 机房的OA及WWW 服务器搬回公司,自行管理和维护。深圳办事处及上海研究所只能访问总部,深圳办事处和上海研究所之间不能互相访问。应能监控网络中的P2P 应用,应能对P2P 应用进行限制,防止网络带宽资源的占用。 

## 拓扑规划 图15-2
在此拓扑结构中,总部的一台核心交换连接3台接入层交换机和一台服务器区交换机,构成了总部内部 LAN;总部一台路由器通过租用ISP 专线将深圳办事处、上海研究所连接起来;总部的出口通过互连路由器连接到 Internet.
在办事处的路由器下挂一台48口的交换机构成办事处的内部网络。

### 设备选型
表15-1 总部及分支机构设备明细表

|办事处| 设备型号| 数量| 描述|
|------|--------|-----|----|
|北京总部| H3C MSR3620-X1 |1 |6 SFP+端口|
|北京总部| S6520XE-54QC-HI |1 |48 SFP+端口/2 QSFP+端口|
|北京总部| S5560X-30F-HI|1 |18GE SFP端口/4SFP+端口/2QSFP+端口|
|北京总部| S5130-54C-HI|3 |48GE Base-T端口/4 SFP+端口|
|深圳办事处| H3C MSR3620-X1|1 |6 SFP+端口|
|深圳办事处| S5130-54C-HI|1 | 48GE Base-T端口/ 4 SFP+端口 1|
|上海研究所| H3C MSR3620-X1|1 | 6 SFP+端口 1|
|上海研究所| S5130-54C-HI|1 | 48GE Base-T端口/4 SFP+端口 1|

### 设备命名及端口描述
表15-2 设备命名明细表
|办事处 |设备型号 |设备名称|
|------|------|------|
|北京总部  |H3C MSR3620-X1  |BJ-MSR3620-0|
|北京总部  |S6520XE-54QC-HI |BJ-S6520XE-0|
|北京总部  |S5560X-30F-HI   |BJ-S5560X-0 |
|北京总部  | S5130-54C-HI | BJ-S5130-0|
|北京总部  | S5130-54C-HI | BJ-S5130-1|
|北京总部  | S5130-54C-HI | BJ-S5130-2|
|深圳办事处 |H3C MSR3620-X1 |SZ-MSR3620-0|
|深圳办事处 |S5130-54C-HI   |SZ-S5130-0|
|上海研究所 |H3C MSR3620-X1 |SH-MSR3620-0 |
|上海研究所 |S5130-54C-HI   |SH-S5130-0|

为了方便在配置文件中能清晰地看明白设备的实际连接关系,需要对设备的连接端口添加描述。本次项目的端口描述格式如下:
其中,AAA 表示为对端设备的名称,采用统一名称格式表示:BBB 表示为对端设备的端Link-To-AAA-BBB

例如, Link-To-BJS6520XE-0-G1/0/1

### WAN 规划

根据前面的需求分析,在总部及分支机构之间使用专线连接,可以选择 ISP提供的基于IP 网络的VPN 专线。

注意:因 VPN 技术知识尚未涉及, 本实验中暂不配置,直接用以太网口或者串口将出口路由器直
接相连,配置内网IP地址直接互联,不配置公网 IP地址。

### LAN 规划

局域网规划包含VLAN 规划、端口聚合规划以及端口隔离、地址捆绑规划几个部分。 

深圳办事处及上海研究所因员工数量较少,不会出现广播风暴现象,所以不划分 VLAN。北京总部员工数量多,而且部门之间需要访问控制,所以要进行VLAN 划分。

人事行政部、财务商务部人数相对较少,而且业务比较密切,划分在1个VLAN 内即可。产品研发部和技术支持部在业务上相对独立,而且产品研发部还有访问控制要求,故将两个部门各自划分一个VLAN。为了便于服务器区的管理,也将服务器区划分为一个VLAN。部门、VLAN号、交换机端口之间的关系如表 15-3所示:

表15-3 VLAN 规划明细表
|部门      |VLAN     |所在设备 | 端口明细|
|-----------|-----------|-----------|-----------|
|人事行政部 |VLAN 10 | BJ-S5130-0  |GE1/0/1-GE1/0/10|
|财务商务部 |VLAN 10 | BJ-S5130-0  |GE1/0/11-GE1/0/30|
|产品研发部 |VLAN 20 | BJ-S5130-0  |GE1/0/31-GE1/0/48 |
|产品研发部 |VLAN 20 | BJ-S5130-1  |GE1/0/1-GE1/0/20|
|技术支持部 |VLAN 30 | BJ-S5130-1  |GE1/0/21-GE1/0/48 |
|技术支持部 |VLAN 30 | BJ-S5130-2  |GE1/0/1-GE1/0/48|
|服务器区   |VLAN 40 | BJ-S5560X-0 |G1/0/1-G1/0/18|
|互连VLAN   |VLAN 50 | BJ-MSR3620-0与BJ-S6520XE-0之间互连|
|管理VLAN   |VLAN 1  |交换机的管理VLAN,用于Telnet及SNMP|

为了便于 IP地址分配和管理,采用 DHCP地址分配方式。使用核心交换机 S6520XE 作为DHCP服务器。

### IP地址规划
地方都使用的都是192.168.0.0/24 网段,在新的网络中需
要对IP地址重新进行规划。

在新的网络中需要如下三类 IP地址——业务地址、设备互连地址和设备管理地址。根据因特网的相关规定,决定使用C类私有地址,在总部和分支机构使用多个C类地址段。其中总部每个VLAN 使用一个C网段。
- 深圳办事处使用 192.168.5.0/24,
- 上海研究所使用192.168.6.0/24 网段。
- 设备的互连地址及管理地址用 192.168.0.0/24 网段。

具体规划如下:

- 业务地址:根据实际需要,并结合未来的需求数量,规划业务地址如表 15-4所示。。
- 互连地址:互连地址主要用于设备的互连,在XX 公司网络中设备的互连地址主要有总部核心交换机与路由器互连、总部路由器与分支路由器互连等,共需要3对互连地址。如表15-5所示。
- 管理地址:用于设备的管理,各设备管理地址如表 15-6所示。

表15-4 业务地址分配表
|地点 |VLAN 网络号 IP地址范围 网关地址|
|---------|----------------------------------------|
|北京总部  |VLAN 10 192.168.1.0/24 192.168.1.1 to 192.168.1.254 192.168.1.254|
|北京总部  |VLAN 20 192.168.2.0/24 192.168.2.1 to 192.168.2.254 192.168.2.254 |
|北京总部  |VLAN 30 192.168.3.0/24 192.168.3.1 to 192.168.3.254 192.168.3.254|
|北京总部  |VLAN 40 192.168.4.0/24 192.168.4.1 to 192.168.4.254 192.168.4.254|
|深圳办事处 |192.168.5.0/24 192.168.5.1 to 192.168.5.254 192.168.5.254|
|上海研究所 |192.168.6.0/24 192.168.6.1 to 192.168.6.254 192.168.6.254|

表15-5 设备的互连地址
|本端设备      |本端 IP 地址 对端设备 对端 IP地址|
|--------------|------------------------------|
|BJ-S6520XE-0 |192.168.0.1/30 BJ-MSR3620-0 192.168.0.2/30|
|BJ-MSR3620-0 |192.168.0.5/30 SZ-MSR3620-0 192.168.0.6/30|
|BJ-MSR3620-0 |192.168.0.9/30 SH-MSR3620-0 192.168.0.10/30|

表15-6 设备的管理地址
|办事处   |  设备名称 管理 VLAN/loopback 管理地址|
|---------|----------------------------------------|
|北京总部  | BJ-MSR3620-0 Loopback 0 192.168.0.17/32|
|北京总部  | BJ-S6520XE-0 VLAN 1 192.168.0.25/29|
|北京总部  | BJ-S5560X-0 VLAN1 192.168.0.26/29|
|北京总部  | BJ-S5130-0 VLAN 1 192.168.0.27/29|
|北京总部  | BJ-S5130-1 VLAN 1 192.168.0.28/29|
|北京总部  | BJ-S5130-2 VLAN 1 192.168.0.29/29|
|深圳办事处 |SZ-MSR3620-0 Loopback 0 192.168.0.18/32|
|深圳办事处 |SZ-S5130-0 VLAN 1 192.168.5.250/24|
|上海研究所 |SH-MSR3620-0 Loopback O 192.168.0.19/32 |
|上海研究所 |SH-S5130-0 VLAN 1 192.168.6.250/24|

### 路由规划
OSPF 作如下规划,如图15-4 所示:

将总部路由器的下行接口和分支路由器的上行接口规划为 area 0,总部的局域网规划为
area 1,深圳办事处规划为 area 2,上海研究所规划为 area 3;如果再增加分支机构的话,则
可规划为area 4、area 5 area N。如果分支机构本身的规模增加,不用更改区域。
在网络的出口配置默认路由,以便访问外网,该路由下一跳为运营商路由器的接口地址。

### 安全规划
安全规划主要包含有访问控制、攻击防范和P2P 监控3块内容。访问控制可以在路由器
上通过 ACL来实现。攻击防范可以通过在出口路由器上启动攻击防范功能来实现,这样就可
以有效地阻止外部对内网的各种攻击。P2P 监控可以通过启用路由器上的ASPF 功能来实
现,发现问题可以及时阻止。

通过在出口路由器上部署 NAT,可以允许总部及分支机构访问 Internet。通过部署 NAT
Server,可以允许总部的服务器对外提供服务。

## 15.4 网络配置实施
## TASK1实验任务一:内网部署 
### 步骤一:总部内网部署 图15-5 总部拓扑结构
Deploying the internal network
Step1 Deploy the internal network of the headquarter 
The topology and port connections of the headquarter network are shown in Figure 15-9.
Internal network deployment mainly involves device naming, port connection description,
link aggregation between the core switch and the server area switch, VLAN configuration,
VLAN routing, and configuration of switch management addresses. 
Figure 15-9 Headquarter topology

### 1) Name and add a port description for each device according to the previous naming plan
and port description. Take MSR3620 as an example:

Enter the interconnect port and add the port description as per the design requirements

第1步: 按照前期的命名规划及端口描述个给每台设备命名并添加端口描述,以
MSR3620为例为设备命名及添加端口描述:
#进入互连端口,按照设计要求添加端口描述
```cmd
[H3C] sysname BJ-MSR3620-0
[BJ-MSR3620-0] interface Ten-GigabitEthernet 0/0
[BJ-MSR3620-0-Ten-GigabitEthernet0/0] description Link-To-BJ-S6520XE-0-XG0/0/1
```
### 2) Configure the port aggregation between the core switch and the server area switch.
The ports aggregated are XG1/0/1 and XG1/0/2. Manual link aggregation is used.

Configure the core switch BJ-S6520XE-0:
```cmd
# Create the manual aggregation group 1 and add XG1/0/1 and XG1/0/2 to the group
[BJ-S6520XE-0] interface Bridge-Aggregation 1
[BJ-S6520XE-0] interface Ten-GigabitEthernet 1/0/1 
[BJ-S6520XE-0-Ten-GigabitEthernet1/0/1] port link-aggregation group 1
[BJ-S6520XE-0-Ten-GigabitEthernet1/0/1] interface Ten-GigabitEthernet 1/0/2
[BJ-S6520XE-0-Ten-GigabitEthernet1/0/2] port link-aggregation group 1
```
Configure the server area switch BJ-S5560X-0:

Create the manual aggregation group 1 and add XG1/0/19 and XG1/0/20 to the group
```cmd
[BJ-S5560X-0] interface Bridge-Aggregation 1
[BJ-S5560X-0] interface Ten-GigabitEthernet 1/0/19
[BJ-S5560X-0-Ten-GigabitEthernet1/0/19] port link-aggregation group 1
[BJ-S5560X-0-Ten-GigabitEthernet1/0/19] interface Ten-GigabitEthernet 1/0/20
[BJ-S5560X-0-Ten-GigabitEthernet1/0/20] port link-aggregation group 1
```

You can run a command on the switch to verify that the aggregation is successful. Please
fill in the space below with the command you used to obtain the following results.
```cmd
<BJ-S5560X-0>display link-aggregation summary

Aggregation Interface Type:
BAGG -- Bridge-Aggregation, BLAGG -- Blade-Aggregation, RAGG -- RouteAggregation, SCH-B -- Schannel-Bundle
Aggregation Mode: S-- Static, D -- Dynamic
Loadsharing Type: Shar -- Loadsharing, Nons -- Non-Loadsharing
Actor System ID: 0x8000, 9a69-3286-0600
AGG AGG Partner ID Selected Unselected Individual Share
Interface Mode Ports Ports Ports Type
BAGG1 S None 2 0 0 Shar
```
### 3) According to the previous VLAN and port allocation plan, configure the VLAN on each
access switch, and configure the uplink interface as a trunk link to allow the relevant
VLAN to pass through. Here we use BJ-S5130-0 as an example. Refer to the following
configurations for other accessswitches:

Create VLAN 10 and VLAN 20. Assign ports G1/0/1 to G1/0/30 to VLAN 10, and ports G1/0/31 to G1/0/48 to VLAN 20
```cmd
[BJ-S5130-0] vlan 10
[BJ-S5130-0-vlan10] port GigabitEthernet 1/0/1 to GigabitEthernet 1/0/30
[BJ-S5130-0-vlan10] vlan 20
[BJ-S5130-0-vlan20]port GigabitEthernet 1/0/31 to GigabitEthernet 1/0/48 
```
To reduce STP traffic flood and frequent convergence due to topology changes, set the switch ports directly connected to the terminal as edge ports to improve network quality
```cmd
[BJ-S5130-0] interface range GigabitEthernet 1/0/1 to GigabitEthernet 1/0/48
[BJ-S5130-0-if-range] stp edged-port 
```
Set the type of uplink port G1/0/49 as trunk and allow VLAN 10 and VLAN 20 to pass through
```cmd
[BJ-S5130-0] interface Ten-GigabitEthernet 1/0/49 
[BJ-S5130-0-Ten-GigabitEthernet1/0/49] port link-type trunk
[BJ-S5130-0-GigabitEthernet1/1/1] port trunk permit VLAN 10 20 
```
### 4) To enable the communication between VLANs, configure an IP address for each VLAN
on the core switch BJ-S6520XE-0 and enable inter-VLAN routing. See the previous plan for specific IP addresses.

Enter the L3 virtual interface of VLAN 10 and configure the IP address for the L3 interface
```cmd
[BJ-S6520XE-0] vlan 10
[BJ-S6520XE-0-vlan10] interface vlan 10
[BJ-S6520XE-0-Vlan-interface10] ip address 192.168.1.254 24
```

Enter the L3 virtual interface of VLAN 20 and configure the IP address for the L3 interface
```cmd
[BJ-S6520XE-0-Vlan-interface10] vlan 20
[BJ-S6520XE-0-vlan20] interface vlan 20 
[BJ-S6520XE-0-Vlan-interface20] ip address 192.168.2.254 24
# Enter the L3 virtual interface of VLAN 30 and configure the IP address for the L3 interface
[BJ-S6520XE-0-Vlan-interface20] vlan 30
[BJ-S6520XE-0-Vlan-interface30] interface vlan 30
[BJ-S6520XE-0-Vlan-interface30] ip address 192.168.3.254 24
# Enter the L3 virtual interface of VLAN 40 and configure the IP address for the L3 interface
[BJ-S6520XE-0-Vlan-interface30] vlan 40
[BJ-S6520XE-0-Vlan-interface40] interface vlan 40
[BJ-S6520XE-0-Vlan-interface40] ip address 192.168.4.254 24
```
If a DIY device is used in H3C HCL for the experiment, the VLAN port of the device is in the down state by default. Therefore, you need to enter the VLAN interface view and use the undo shutdown command to set the port state to up.


Note:

Configure the downlink ports XG1/0/3, XG1/0/5 and XG1/0/7 connecting the core switch and the access switch, configure the type of port aggregation group 1 as trunk, and configure the VLAN as planned.
```cmd
[BJ-S6520XE-0] interface Ten-GigabitEthernet 1/0/3
[BJ-S6520XE-0-Ten-GigabitEthernet1/0/3] port link-type trunk
[BJ-S6520XE-0-Ten-GigabitEthernet1/0/3] port trunk permit vlan 10 20
[BJ-S6520XE-0] interface Ten-GigabitEthernet 1/0/5
[BJ-S6520XE-0-Ten-GigabitEthernet1/0/5] port link-type trunk 
[BJ-S6520XE-0-Ten-GigabitEthernet1/0/5] port trunk permit vlan 20 30
[BJ-S6520XE-0] interface Ten-GigabitEthernet 1/0/7
[BJ-S6520XE-0-Ten-GigabitEthernet1/0/7] port link-type trunk
[BJ-S6520XE-0-Ten-GigabitEthernet1/0/7] port trunk permit vlan 30
[BJ-S6520XE-0] interface Bridge-Aggregation 1
[BJ-S6520XE-0-Bridge-Aggregation1] port link-type trunk
[BJ-S6520XE-0-Bridge-Aggregation1] port trunk permit vlan 40
```
After the above configuration, use the ping command to verify that the previous configuration is correct. For verification, connect the PC to any VLAN and change the IP
address of the PC to the IP address of the VLAN.
### 5) According to the previous plan, it is necessary to configure the DHCP protocol on the
core switch and assign an IP address to each PC. The configuration commands are as follows.

Enable the DHCP function


```cmd
[BJ-S6520XE-0] dhcp enable
# Create the DHCP address pool corresponding to VLAN 10, and specify the gateway address and DNS server address
[BJ-S6520XE-0] dhcp server ip-pool vlan10
[BJ-S6520XE-0-dhcp-pool-vlan10] network 192.168.1.0 24
[BJ-S6520XE-0-dhcp-pool-vlan10] gateway-list 192.168.1.254
[BJ-S6520XE-0-dhcp-pool-vlan10] dns-list 202.102.99.68
[BJ-S6520XE-0-dhcp-pool-vlan10] quit
# Create the DHCP address pool corresponding to VLAN 20, and specify the gateway address and DNS server address
[BJ-S6520XE-0] dhcp server ip-pool vlan20
[BJ-S6520XE-0-dhcp-pool-vlan20] network 192.168.2.0 24
[BJ-S6520XE-0-dhcp-pool-vlan20] dns-list 202.102.99.68
[BJ-S6520XE-0-dhcp-pool-vlan20] gateway-list 192.168.2.254
[BJ-S6520XE-0-dhcp-pool-vlan20] quit 
# Create the DHCP address pool corresponding to VLAN 30, and specify the gateway address and DNS server address
[BJ-S6520XE-0] dhcp server ip-pool vlan30
[BJ-S6520XE-0-dhcp-pool-vlan30] network 192.168.3.0 24
[BJ-S6520XE-0-dhcp-pool-vlan30] gateway-list 192.168.3.254
[BJ-S6520XE-0-dhcp-pool-vlan30] dns-list 202.102.99.68
[BJ-S6520XE-0-dhcp-pool-vlan30] quit
# Configure the IP addresses (gateway addresses) in the DHCP address pool that do not participate in auto-assignment
[BJ-S6520XE-0] dhcp server forbidden-ip 192.168.1.254 
[BJ-S6520XE-0] dhcp server forbidden-ip 192.168.2.254
[BJ-S6520XE-0] dhcp server forbidden-ip 192.168.3.254
```
To test whether DHCP is successfully configured on a PC, select Start > Run on the PC,
and enter the cmd command to enter the command line mode. In this mode, enter the
ipconfig command to view the details of the local IP address. 
### 6) Configure the management address of the switch. In this example, VLAN 1 is used as
the management VLAN and the access switch BJ-S5560X-0 is used. For other
switches, the configurations of this part are the same.

BJ-S6520XE-0 switch:

Enter the L3 virtual interface of VLAN 1 and configure the IP address for the L3 interface
```cmd
[BJ-S6520XE-0] interface vlan 1
[BJ-S6520XE-0-Vlan-interface1] ip address 192.168.0.25 29
```
Configure the IP address for the VLAN 1 L3 interface, configure the routing to the upperayer switch, and point the next hop to the management address of the core switch
BJ-S5560X-0 switch:
```cmd
[BJ-S5560X-0] interface vlan 1
[BJ-S5560X-0-Vlan-interface1] ip address 192.168.0.26 29
[BJ-S5560X-0-Vlan-interface 1] quit
[BJ-S5560X-0] ip route-static 0.0.0.0 0 192.168.0.25 
```
Configure the management IP addresses of other S5130 switches by referring to the BJS5560X-0 switch.

## Step2 Deploy the internal network of the Shenzhen office
The network structure of the Shenzhen office is shown in Figure 15-10. The deployment of this part of the internal network mainly consists of device naming, DHCP configuration, and
management address configuration.

Figure 15-10 Network structure of the Shenzhen office

### 1)Name the device and add the port description.

Enter each interconnect port of the MSR3620 and add the port description as per the
design requirements
```cmd
[H3C] sysname SZ-MSR3620-0
[SZ-MSR3620-0] interface Ten-GigabitEthernet 0/0
[SZ-MSR3620-0-Ten-GigabitEthernet0/0] description Link-To-SZ-S5130-0-XGE1/0/49
# Enter each interconnect port of the switch and add the port description as per the design requirements
[H3C] sysname SZ-S5130-0
[SZ-S5130-0] interface Ten-GigabitEthernet 1/0/49 
[SZ-S5130-0-Ten-GigabitEthernet1/0/49] description Link-To-SZ-MSR3620-0-GO/0
```
### 2) Configure the DHCP.
In the Shenzhen office, assign IP addresses to PCs accessed with the DHCP mode, and use the MSR3620 router as the DHCP server. The specific configurations are as follows:

Configure the XGO/0 interface with an IP address that is the gateway for all PCs on the internal network
```cmd
[SZ-MSR3620-0] interface Ten-GigabitEthernet 0/0
[SZ-MSR3620-0-Ten-GigabitEthernet0/0]ip address 192.168.5.254 24
[SZ-MSR3620-0-Ten-GigabitEthernet0/0] quit 
# Enable the DHCP function, create the DHCP address pool 1, and specify the gateway address and DNS server address
[SZ-MSR3620-0] dhcp enable
[SZ-MSR3620-0] dhcp server ip-pool pool1
[SZ-MSR3620-0-dhcp-pool-pool1] network 192.168.5.0 mask 255.255.255.0
[SZ-MSR3620-0-dhcp-pool-pool1] gateway-list 192.168.5.254
[SZ-MSR3620-0-dhcp-pool-pool1] dns-list 202.102.99.68
# Configure the IP addresses in the DHCP address pool that do not participate in auto-assignment
[SZ-MSR3620-0-dhcp-pool-pool1] forbidden-ip 192.168.5.254
[SZ-MSR3620-0-dhcp-pool-pool1] forbidden-ip 192.168.5.250
```
### 3) Configure the management address of the switch. 
Enter the L3 virtual interface of VLAN 1 and configure the IP address for the L3 interface
```cmd
[SZ-S5130-0] interface vlan 1
[SZ-S5130-0-Vlan-interface1] ip address 192.168.5.250 24
[SZ-S5130-0-Vlan-interface1] quit
# Configure the routing to the upper-layer router and points the next hop to the router interface address
[SZ-S5130-0] ip route-static 0.0.0.0 0 192.168.5.254
```
### Step3 Deploy the internal network of the Shanghai research institute .

The network structure of the Shanghai research institute is the same as that of the Shenzhen office, as shown in Figure 15-11. The deployment of this part of the internal network mainly consists of device naming, DHCP configuration, and management address configuration.

Figure 15-11 Network structure of the Shanghai research 

### 1)Name the device and add the port description.
SH-MSR3620-0 router:
Enter each interconnect port and add the port description as per the design requirements
```cmd
[H3C] sysname SH-MSR3620-0
[SH-MSR3620-0] interface Ten-GigabitEthernet 0/0
[SH-MSR3620-0-Ten-GigabitEthernet0/0] description Link-To-SH-S5130-0-XGE1/0/49
```
SH-S5130-0 switch:

 Enter each interconnect port and add the port description as per the design requirements
```cmd
[H3C] sysname SH-S5130-0
[SH-S5130-0] interface Ten-GigabitEthernet 1/0/49
[SH-S5130-0-Ten-GigabitEthernet1/0/49] description Link-To-SH-XGE0/0
```
### 2) Configure the DHCP.
In the Shanghai research institute, assign IP addresses to PCs accessed with the DHCP mode, and use MSR3620 router as the DHCP server. Please fill in the blanks below with the specific DHCP configurations:

Configure the XGO0/0 interface with an IP address that is the gateway for all PCs on the internal network
```cmd
[SH-MSR3620-0] interface Ten-GigabitEthernet 0/0
[SH-MSR3620-0-Ten-GigabitEthernet0/0] ip address 192.168.6.254 24
[SH-MSR3620-0-Ten-GigabitEthernet0/0] quit 
# Enable the DHCP function, create the DHCP address pool 1, and specify the gateway address and DNS server address
[SH-MSR3620-0] dhcp enable
[SH-MSR3620-0] dhcp server ip-pool pool1
[SH-MSR3620-0-dhcp-pool-poo11] network 192.168.6.0 24
[SH-MSR3620-0-dhcp-pool-poo11] gateway-list 192.168.6.254
[SH-MSR3620-0-dhcp-pool-pool1] dns-list 202.102.99.68
# Configure the IP addresses in the DHCP address pool that do not participate in autoassignment
[SH-MSR3620-0-dhcp-pool-poo11] forbidden-ip 192.168.6.254
[SH-MSR3620-0-dhcp-pool-pool1] forbidden-ip 192.168.6.250
```
### 3) Configure the management address of the switch.

Enter the L3 virtual interface of VLAN 1 and configure the IP address for the L3 interface
```cmd
[SH-S5130-0] interface vlan 1
[SH-S5130-0-Vlan-interface1] ip address 192.168.6.250 24
[SH-S5130-0-Vlan-interface1] quit
##配置到达上层路由器的路由,下一跳指向路由器接口地址
[SH-S5130-0] ip route-static 0.0.0.0 0 192.168.6.254
```

## Lab Task 2: Deploying the WAN
To ensure the security of data transmission, use private lines to connect the headquarter and branch offices. As per customer requirements, ISPs can provide various types of private
lines such as bare fibers, L2 private lines (SDHs), and L3 private lines (VPNs) that are commonly used and featured with high security in data transmission. Currently, small- and
medium-sized enterprises generally use VPN private lines as they are affordable.

VPN is not configured as this experiment does not involve the technical knowledge of VPN.

Configure the private network IP address of each port directly according to the plan, instead of configuring public network IP addresses, as shown in Figure 15-12.

Figure 15-12 Connection diagram of WAN between the headquarter and branch offices 

### 1) Configure the BJ-MSR3620-0.
Add descriptions of G0/2 and GO/3 interfaces, and configure IP addresses as planned
```cmd
[BJ-MSR3620-0] interface GigabitEthernet 0/2
[BJ-MSR3620-0-GigabitEthernet0/2] description Link-To-SZ-MSR3620-0-G0/1
[BJ-MSR3620-0-GigabitEthernet0/2] ip address 192.168.0.5 30
[BJ-MSR3620-0] interface GigabitEthernet 0/3
[BJ-MSR3620-0-GigabitEthernet0/3] description Link-To-SH-MSR3620-0-G0/1
[BJ-MSR3620-0-GigabitEthernet0/3] ip address 192.168.0.9 30
```
### 2) Configure the SZ-MSR3620-0. 
Add the description of GO/1 interface, and configure IP address of the G0/1 interface as planned
```cmd
[SZ-MSR3620-0] interface GigabitEthernet 0/1
[SZ-MSR3620-0-GigabitEthernet0/1] description Link-To-BJ-MSR3620-0-G0/2
[SZ-MSR3620-0-GigabitEthernet0/1] ip address 192.168.0.6 30
```
### 3) Configure the SH-MSR3620-0.
Add the description of GO/1 interface, and configure IP address of the GO/1 interface as planned
```cmd
[SH-MSR3620-0] interface GigabitEthernet 0/1
[SH-MSR3620-0-GigabitEthernet0/1] description Link-To-BJ-MSR3620-0-GO/3
[SH-MSR3620-0-GigabitEthernet0/1] ip address 192.168.0.10 30
```
## Lab Task 3实验任务三:路由部署: Deploying the routing 

网采用动态的OSPF 协议,并规划每个办事处为一个area,这样如果以后网络规模扩大,也不需要对整网重新规划,而只需增加 area 即可;为了方便对各办事处访问外网的控制,规划这个网络的公网出口设在总部,由总部统一出口访问外网,在总部和外网之间使用缺省路由。

Considering the future development strategy and plan of the XX company, we adopt dynamic OSPF protocols in the entire network when deploying the routing. We also plan each branch office as an area; therefore, the company only needs to add areas to expand the network, without re-planning the entire network. To facilitate the control of the branch offices' access to the external network, the public network egress of the network is located at the headquarter in the plan, so that employees of the company can access the external network through the unified egress. A default routing is used between the headquarter and
the external network.
The specific network plan is shown in Figure 15-13: 
### 1) According to the previous routing plan, configure the OSPF routing protocol of the BJS6520XE-0 switch.

The manually specified router ID is the interface address of VLAN 1
```cmd
[BJ-S6520XE-0] router id 192.168.0.25
```
在区域里发布网段,后面跟的是反掩码,但有些地址我们很难口算出它的反掩码,CMW提供了这样一个特性:可以将掩码自动转化为反掩码;为此我们演示一下,发布网段时使用掩码,并验证是否能自动转换。

When releasing a network segment in an area, the network segment is followed by an antimask. However, it is difficult for us to calculate the anti-masks of some addresses. CMW
can automatically convert a mask to an anti-mask. To demonstrate this feature, we use a mask when releasing the network segment and check whether it can automatically convert the mask to an anti-mask.
```cmd
[BJ-S6520XE-0] ospf
[BJ-S6520XE-0-ospf-1] area 1
[BJ-S6520XE-0-ospf-1-area-0.0.0.1] network 192.168.1.0 255.255.255.0
# Check whether it can automatically convert the mask to an anti-mask
[BJ-S6520XE-0-ospf-1-area-0.0.0.1] display this
#
area 0.0.0.1
network 192.168.1.0 0.0.0.255
#
return
# Continue to release the network segments where VLAN 20, VLAN 30, and VLAN 40 reside

[BJ-S6520XE-0-ospf-1-area-0.0.0.1] network 192.168.2.0 0.0.0.255
[BJ-S6520XE-0-ospf-1-area-0.0.0.1] network 192.168.3.0 0.0.0.255
[BJ-S6520XE-0-ospf-1-area-0.0.0.1] network 192.168.4.0 0.0.0.255
# Release the address of the interface interconnected with the BJ router
[BJ-S6520XE-0-ospf-1-area-0.0.0.1] network 192.168.0.1 0.0.0.3
#Release the network segment of the switch's management VLAN address
[BJ-S6520XE-0-ospf-1-area-0.0.0.1] network 192.168.0.24 0.0.0.7
```
Figure 15-13 Routing plan

Area 1 Area 2 Area 3

### 2) 根据前期的路由规划,完成 BJ-MSR3620-0 路由器的OSPF路由配置。
According to the previous routing plan, configure the OSPF routing of the BJ-MSR3620-0 router.



Create and enter the loopback 0 interface, configure the IP address of the interface (note that the mask has 32 bits), and specify the router ID manually
```cmd
#创建并进入 loopback 0 接口,为 loopback0 接口配置 IP地址,注意掩码为32位,并手工指定 router id
[BJ-MSR3620-0] interface loopback 0
[BJ-MSR3620-0-LoopBack0] ip address 192.168.0.17 32
[BJ-MSR3620-0] router id 192.168.0.17 

# Create and enter area 1, and release the network segment interconnected with the BJS6520XE-0
#创建并进入 area1,发布同 BJ-S6520XE-0 的互连网
[BJ-MSR3620-0] ospf
[BJ-MSR3620-0-ospf-1] area 1
[BJ-MSR3620-0-ospf-1-area-0.0.0.1] network 192.168.0.0 0.0.0.3

# Release the loopback interface address (as the network management address) to facilitate the management
#为了便于管理,发布 loopback 接口地址(作为网管地址)
[BJ-MSR3620-0-ospf-1-area-0.0.0.1] network 192.168.0.17 0.0.0.0

#Create and enter area 0, and release the network segments interconnected with the SZMSR3620-0 and SH-MSR3620-0
#创建并进入 area 0,发布同 SZ-MSR3620-0 及SH-MSR3620-0 的互连网段
[BJ-MSR3620-0-ospf-1] area 0
[BJ-MSR3620-0-ospf-1-area-0.0.0.0] network 192.168.0.4 0.0.0.3
[BJ-MSR3620-0-ospf-1-area-0.0.0.0] network 192.168.0.8 0.0.0.3
```
### 3) According to the previous routing plan, configure the OSPF protocol of the SZMSR3620-0.
Create and enter the loopback 0 interface, configure the IP address of the interface (note that the mask has 32 bits), and specify the router ID manually
```cmd
[SZ-MSR3620-0] interface loop 0
[SZ-MSR3620-0-LoopBack0] ip address 192.168.0.18 32 
[SZ-MSR3620-0] router id 192.168.0.18
# Enter area 0, and release the network segment interconnected with the BJ-MSR3620-0
[SZ-MSR3620-0] ospf
[SZ-MSR3620-0-ospf-1] area 0
[SZ-MSR3620-0-ospf-1-area-0.0.0.0] network 192.168.0.4 0.0.0.3
# Enter area 2, and release the network segment address interconnected with the internal network of the Shenzhen office
[SZ-MSR3620-0-ospf-1] area 2 
[SZ-MSR3620-0-ospf-1-area-0.0.0.2] network 192.168.5.0 0.0.0.255
#Release the loopback address (as the network management ddress)
[SZ-MSR3620-0-ospf-1-area-0.0.0.2] network 192.168.0.18 0.0.0.0 
```

### 4)According to the previous routing plan, configure the OSPF protocol of the SHMSR3620-0.

Create and enter the loopback 0 interface, configure the IP address of the interface (note that the mask has 32 bits), and specify the router ID manually
```cmd
[SH-MSR3620-0] interface loop 0
[SH-MSR3620-0-LoopBack0] ip address 192.168.0.19 32
[SH-MSR3620-0] router id 192.168.0.19

# Enter area 0, and release the network segment interconnected with the BJ-MSR3620-0
[SH-MSR3620-0] ospf
[SH-MSR3620-0-ospf-1] area 0
[SH-MSR3620-0-ospf-1-area-0.0.0.0] network 192.168.0.8 0.0.0.3

# Enter area 3, and release the network segment interconnected with the internal network of the Shanghai research institute
[SH-MSR3620-0-ospf-1] area 3
[SH-MSR3620-0-ospf-1-area-0.0.0.3] network 192.168.6.0 0.0.0.255

# Release the loopback address (as the network management address)
[SH-MSR3620-0-ospf-1-area-0.0.0.3] network 192.168.0.19 0.0.0.0
```
### 5) 为了让总部及分支机构能访问 Internet,需要在出口路由器上配置访问Internet 的路由。

Configure a routing on the egress router for the headquarter and branch offices to Configure a default routing, point the next hop to the gateway address provided by the
ISP, and release the default routing to OSPF
access the Internet.
```cmd
#配置一条缺省路由,下一跳指向 ISP 给的网关地址,并将缺省路由发布到OSPF 中
[BJ-MSR3620-0-GigabitEthernet0/1] ip address 202.38.160.2 30
[BJ-MSR3620-0] ip route-static 0.0.0.0 0 202.38.160.1
[BJ-MSR3620-0] ospf
[BJ-MSR3620-0-ospf-1] default-route-advertise
```
## Lab Task 4实验任务四:网络安全部署: Deploying network security 

### 1) 内网全部使用的是私有地址,如需访问 Internet 需要进行地址转换,同时可以将内网中的服务器发布到 Internet.

The internal network uses private addresses. To access the Internet, address translation is required. Servers on the internal network can be released to the Internet.

Create ACL 2000, enter the interface connected to the Internet, and use Easy IP to enable NAT
```cmd
#创建 ACL 2000,进入连接 Internet 的接口,使用 Easy IP 方式使能 NAT
[BJ-MSR3620-0] acl basic 2000 
[BJ-MSR3620-0-acl-ipv4-basic-2000] rule 0 permit
[BJ-MSR3620-0] interface g 0/1
[BJ-MSR3620-0-GigabitEthernet0/1] nat outbound 2000

#在公司总部有WWW及OA 服务器需要发布,WWW 服务器地址为192.168.4.131, OA 服务器地址为192.168.4.130,端口号为8080
[BJ-MSR3620-0-GigabitEthernet0/1] nat server protocol tcp global 202.38.160.2 http inside 192.168.4.131 http
[BJ-MSR3620-0-GigabitEthernet0/1] nat server protocol tcp global 202.38.160.2 8080 inside 192.168.4.130 8080
```
### 2)  攻击防范配置Configure attack prevention.
Create and configure ACL 3001 Common viruses and ports attacked are shown below. All can be configured to ACL 3001.
The following are some configuration commands commonly used in the current network and are for reference only.

```cmd
#创建并配置 ACL 3001
[BJ-MSR3620-0]acl advanced 3001
```

常见的病毒及攻击端口如下所示,可全部配置到 acl 3001 中,以下配置为一些现网常用的配置命令,仅供参考。

```cmd
rule 0 deny tcp source-port eq 3127
rule 1 deny tcp source-port eq 1025 1
rule 2 deny tcp source-port eq 5554
rule 3 deny tcp source-port eq 9996
rule 4 deny tcp source-port eq 1068
rule 5 deny tcp source-port eq 135
rule 6 deny udp source-port eq 135
rule 7 deny tcp source-port eq 137
rule 8 deny udp source-port eq netbios-ns
rule 9 deny tcp source-port eq 138
rule 10 deny upp source-port eq netbios-dgm
rule 11 deny tcp source-port eq 139
rule 12 deny udp source-port eq netbios-ssn
rule 13 deny tcp source-port eg 593
rule 14 deny tcp source-port eq 4444
rule 15 deny tcp source-port eq 5800
rule 16 deny tcp source-port eq 5900
rule 18 deny tcp source-port eq 8998
rule 19 deny tcp source-port eq 445
rule 20 deny udp source-port eq 445
rule 21 deny udp source-port eq 1434
rule 30 deny tcp destination-port eq 3127
rule 31 deny tcp destination-port eq 1025
rule 32 deny tcp destination-port eq 5554
rule 33 deny tcp destination-port eq 9996
rule 34 deny tcp destination-port eg 1068
rule 35 deny tcp destination-port eq 135
rule 36 deny upp destination-port eq 135
rule 37 deny tcp destination-port eq 137
rule 38 deny udp destination-port eq netbios-ns
rule 39 deny tcp destination-port eg 138
rule 40 deny udp destination-port eq netbios-dgm
rule 41 deny tcp destination-port eq 139
rule 42 deny udp destination-port eq netbios-ssn
rule 43 deny tcp destination-port eq 593
rule 44 deny tcp destination-port eq 4444
rule 45 deny tcp destination-port eg 5800
rule 46 deny tcp destination-port eq 5900
rule 48 deny tcp destination-port eq 8998
rule 49 deny tcp destination-port eq 445
rule 50 deny udp destination-port eq 445
rule 51 deny udp destination-port eq 1434
```

```cmd
# Apply ACL 3001 in the IN direction of the external network interface to prevent intrusion from the external network 

#将 ACL 3001应用与连接外网接口的 IN 方向阻止外网的入侵

[BJ-MSR3620-0-GigabitEthernet0/1] packet-filter 3001 inbound
```









