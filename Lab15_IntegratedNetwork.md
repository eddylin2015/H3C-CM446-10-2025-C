# Lab15_IntegratedNetwork  

## LabDiagram

![](https://github.com/eddylin2015/H3C-CM446-10-2025-C/blob/main/img/lab15LabDiagram.png?raw=true)

## TASK1
Deploying the internal network
Step1 Deploy the internal network of the headquarter 
The topology and port connections of the headquarter network are shown in Figure 15-9.
Internal network deployment mainly involves device naming, port connection description,
link aggregation between the core switch and the server area switch, VLAN configuration,
VLAN routing, and configuration of switch management addresses. 
Figure 15-9 Headquarter topology

1) Name and add a port description for each device according to the previous naming plan
and port description. Take MSR3620 as an example:
# Enter the interconnect port and add the port description as per the design requirements
```cmd
[H3C] sysname BJ-MSR3620-0
[BJ-MSR3620-0] interface Ten-GigabitEthernet 0/0
[BJ-MSR3620-0-Ten-GigabitEthernet0/0] description Link-To-BJ-S6520XE-0-XG0/0/1
```
2) Configure the port aggregation between the core switch and the server area switch.
The ports aggregated are XG1/0/1 and XG1/0/2. Manual link aggregation is used.
Configure the core switch BJ-S6520XE-0:
```cmd
# Create the manual aggregation group 1 and add XG1/0/1 and XG1/0/2 to the group
[BJ-S6520XE-0] interface Bridge-Aggregation 1
[BJ-S6520XE-0] interface Ten-GigabitEthernet 1/0/1 lammou
[BJ-S6520XE-0-Ten-GigabitEthernet1/0/1] port link-aggregation group 1
[BJ-S6520XE-0-Ten-GigabitEthernet1/0/1] interface Ten-GigabitEthernet 1/0/2
[BJ-S6520XE-0-Ten-GigabitEthernet1/0/2] port link-aggregation group 1
```
Configure the server area switch BJ-S5560X-0:
# Create the manual aggregation group 1 and add XG1/0/19 and XG1/0/20 to the group
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
AGG AGG Partner ID
Interface Mode
Selected Unselected Individual Share
Ports Ports Ports Type
BAGG1 S None 2 이 0 Shar
```
3) According to the previous VLAN and port allocation plan, configure the VLAN on each
access switch, and configure the uplink interface as a trunk link to allow the relevant
VLAN to pass through. Here we use BJ-S5130-0 as an example. Refer to the following
configurations for other accessswitches:
# Create VLAN 10 and VLAN 20. Assign ports G1/0/1 to G1/0/30 to VLAN 10, and ports ammou@ma
G1/0/31 to G1/0/48 to VLAN 20
```cmd
[BJ-S5130-0] vlan 10
[BJ-S5130-0-vlan10] port GigabitEthernet 1/0/1 to GigabitEthernet 1/0/30
[BJ-S5130-0-vlan10] vlan 20
[BJ-S5130-0-vlan20]port GigabitEthernet 1/0/31 to GigabitEthernet 1/0/48 mbc.edu. mo
```
# To reduce STP traffic flood and frequent convergence due to topology changes, set the
switch ports directly connected to the terminal as edge ports to improve network quality
```cmd
[BJ-S5130-0] interface range GigabitEthernet 1/0/1 to GigabitEthernet 1/0/48
[BJ-S5130-0-if-range] stp edged-port 
```
# Set the type of uplink port G1/0/49 as trunk and allow VLAN 10 and VLAN 20 to pass
through
```cmd
[BJ-S5130-0] interface Ten-GigabitEthernet 1/0/49 1. mo
[BJ-S5130-0-Ten-GigabitEthernet1/0/49] port link-type trunk
[BJ-S5130-0-GigabitEthernet1/1/1] port trunk permit VLAN 10 20 mbc.edu. mo
```
4) To enable the communication between VLANs, configure an IP address for each VLAN
on the core switch BJ-S6520XE-0 and enable inter-VLAN routing. See the previous
plan for specific IP addresses.
# Enter the L3 virtual interface of VLAN 10 and configure the IP address for the L3 interface
```cmd
[BJ-S6520XE-0] vlan 10
[BJ-S6520XE-0-vlan10] interface vlan 10
[BJ-S6520XE-0-Vlan-interface10] ip address 192.168.1.254 24
```
# Enter the L3 virtual interface of VLAN 20 and configure the IP address for the L3 interface
```cmd
[BJ-S6520XE-0-Vlan-interface10] vlan 20
[BJ-S6520XE-0-vlan20] interface vlan 20 mo
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
If a DIY device is used in H3C HCL for the experiment, the VLAN port of the device is in the
down state by default. Therefore, you need to enter the VLAN interface view and use the
undo shutdown command to set the port state to up.
Note:
# Configure the downlink ports XG1/0/3, XG1/0/5 and XG1/0/7 connecting the core switch
and the access switch, configure the type of port aggregation group 1 as trunk, and
configure the VLAN as planned.
```cmd
[BJ-S6520XE-0] interface Ten-GigabitEthernet 1/0/3
[BJ-S6520XE-0-Ten-GigabitEthernet1/0/3] port link-type trunk
[BJ-S6520XE-0-Ten-GigabitEthernet1/0/3] port trunk permit vlan 10 20
[BJ-S6520XE-0] interface Ten-GigabitEthernet 1/0/5
[BJ-S6520XE-0-Ten-GigabitEthernet1/0/5] port link-type trunk Lammou@mail, mbc. edu. mo
[BJ-S6520XE-0-Ten-GigabitEthernet1/0/5] port trunk permit vlan 20 30
[BJ-S6520XE-0] interface Ten-GigabitEthernet 1/0/7
[BJ-S6520XE-0-Ten-GigabitEthernet1/0/7] port link-type trunk
[BJ-S6520XE-0-Ten-GigabitEthernet1/0/7] port trunk permit vlan 30
[BJ-S6520XE-0] interface Bridge-Aggregation 1
[BJ-S6520XE-0-Bridge-Aggregation1] port link-type trunk
[BJ-S6520XE-0-Bridge-Aggregation1] port trunk permit vlan 40
```
After the above configuration, use the ping command to verify that the previous
configuration is correct. For verification, connect the PC to any VLAN and change the IP
address of the PC to the IP address of the VLAN.
5) According to the previous plan, it is necessary to configure the DHCP protocol on the
core switch and assign an IP address to each PC. The configuration commands are as
follows.
# Enable the DHCP function
# Create the DHCP address pool corresponding to VLAN 10, and specify the gateway
```cmd
[BJ-S6520XE-0] dhcp enable
address and DNS server address
[BJ-S6520XE-0] dhcp server ip-pool vlan10
[BJ-S6520XE-0-dhcp-pool-vlan10] network 192.168.1.0 24
[BJ-S6520XE-0-dhcp-pool-vlan10] gateway-list 192.168.1.254
[BJ-S6520XE-0-dhcp-pool-vlan10] dns-list 202.102.99.68
[BJ-S6520XE-0-dhcp-pool-vlan10] quit
# Create the DHCP address pool corresponding to VLAN 20, and specify the gateway
amm address and DNS server address
[BJ-S6520XE-0] dhcp server ip-pool vlan20
[BJ-S6520XE-0-dhcp-pool-vlan20] network 192.168.2.0 24
[BJ-S6520XE-0-dhcp-pool-vlan20] dns-list 202.102.99.68
[BJ-S6520XE-0-dhcp-pool-vlan20] gateway-list 192.168.2.254
[BJ-S6520XE-0-dhcp-pool-vlan20] quit 
# Create the DHCP address pool corresponding to VLAN 30, and specify the gateway
address and DNS server address
[BJ-S6520XE-0] dhcp server ip-pool vlan30
[BJ-S6520XE-0-dhcp-pool-vlan30] network 192.168.3.0 24
[BJ-S6520XE-0-dhcp-pool-vlan30] gateway-list 192.168.3.254
[BJ-S6520XE-0-dhcp-pool-vlan30] dns-list 202.102.99.68
[BJ-S6520XE-0-dhcp-pool-vlan30] quit
# Configure the IP addresses (gateway addresses) in the DHCP address pool that do not
participate in auto-assignment
[BJ-S6520XE-0] dhcp server forbidden-ip 192.168.1.254 mbc.edu. mo
[BJ-S6520XE-0] dhcp server forbidden-ip 192.168.2.254
[BJ-S6520XE-0] dhcp server forbidden-ip 192.168.3.254
```
To test whether DHCP is successfully configured on a PC, select Start > Run on the PC,
and enter the cmd command to enter the command line mode. In this mode, enter the
ipconfig command to view the details of the local IP address. 
6) Configure the management address of the switch. In this example, VLAN 1 is used as
the management VLAN and the access switch BJ-S5560X-0 is used. For other
switches, the configurations of this part are the same.
BJ-S6520XE-0 switch:
# Enter the L3 virtual interface of VLAN 1 and configure the IP address for the L3 interface
```cmd
[BJ-S6520XE-0] interface vlan 1
[BJ-S6520XE-0-Vlan-interface1] ip address 192.168.0.25 29
# Configure the IP address for the VLAN 1 L3 interface, configure the routing to the upperayer switch, and point the next hop to the management address of the core switch
BJ-S5560X-0 switch:
[BJ-S5560X-0] interface vlan 1
[BJ-S5560X-0-Vlan-interface1] ip address 192.168.0.26 29
[BJ-S5560X-0-Vlan-interface 1] quit
[BJ-S5560X-0] ip route-static 0.0.0.0 0 192.168.0.25 edu. mo
```
Configure the management IP addresses of other S5130 switches by referring to the BJS5560X-0 switch.
Step2 Deploy the internal network of the Shenzhen office
The network structure of the Shenzhen office is shown in Figure 15-10. The deployment of
this part of the internal network mainly consists of device naming, DHCP configuration, and
management address configuration.
Figure 15-10 Network structure of the Shenzhen office
SZ-MSR3620-0
ROUTER
XGO/0
SWITCH
XG1/0/49
SZ-S5130-0
Name the device and add the port description.
# Enter each interconnect port of the MSR3620 and add the port description as per the
design requirements
```cmd
[H3C] sysname SZ-MSR3620-0
[SZ-MSR3620-0] interface Ten-GigabitEthernet 0/0
[SZ-MSR3620-0-Ten-GigabitEthernet0/0] description Link-To-SZ-S5130-0-XGE1/0/49
# Enter each interconnect port of the switch and add the port description as per the design
requirements
[H3C] sysname SZ-S5130-0
[SZ-S5130-0] interface Ten-GigabitEthernet 1/0/49 u@mail.m
[SZ-S5130-0-Ten-GigabitEthernet1/0/49] description Link-To-SZ-MSR3620-0-GO/0
```
2) Configure the DHCP.
In the Shenzhen office, assign IP addresses to PCs accessed with the DHCP mode, and
use the MSR3620 router as the DHCP server. The specific configurations are as follows:
# Configure the XGO/0 interface with an IP address that is the gateway for all PCs on the
internal network
```cmd
[SZ-MSR3620-0] interface Ten-GigabitEthernet 0/0
[SZ-MSR3620-0-Ten-GigabitEthernet0/0]ip address 192.168.5.254 24
[SZ-MSR3620-0-Ten-GigabitEthernet0/0] quit 
# Enable the DHCP function, create the DHCP address pool 1, and specify the gateway
address and DNS server address
[SZ-MSR3620-0] dhcp enable
[SZ-MSR3620-0] dhcp server ip-pool pool1
[SZ-MSR3620-0-dhcp-pool-pool1] network 192.168.5.0 mask 255.255.255.0
[SZ-MSR3620-0-dhcp-pool-pool1] gateway-list 192.168.5.254
[SZ-MSR3620-0-dhcp-pool-pool1] dns-list 202.102.99.68
# Configure the IP addresses in the DHCP address pool that do not participate in auto-
[SZ-MSR3620-0-dhcp-pool-pool1] forbidden-ip 192.168.5.254
assignment
[SZ-MSR3620-0-dhcp-pool-pool1] forbidden-ip 192.168.5.250
```
3) Configure the management address of the switch. 
# Enter the L3 virtual interface of VLAN 1 and configure the IP address for the L3 interface
```cmd
[SZ-S5130-0] interface vlan 1
[SZ-S5130-0-Vlan-interface1] ip address 192.168.5.250 24
# Configure the routing to the upper-layer router and points the next hop to the router
[SZ-S5130-0-Vlan-interface1] quit
interface address
[SZ-S5130-0] ip route-static 0.0.0.0 0 192.168.5.254
```
Step3 Deploy the internal network of the Shanghai research institute .mbc.ed
The network structure of the Shanghai research institute is the same as that of the
Shenzhen office, as shown in Figure 15-11. The deployment of this part of the internal
network mainly consists of device naming, DHCP configuration, and management address lammo
configuration.
Figure 15-11 Network structure of the Shanghai research institute
SH-MSR3620-0
ROUTER
XG0/0
XG1/0/49
SH-S5130-0
WITO
Name the device and add the port description.
SH-MSR3620-0 router:
# Enter each interconnect port and add the port description as per the design requirements
```cmd
[H3C] sysname SH-MSR3620-0
[SH-MSR3620-0] interface Ten-GigabitEthernet 0/0
[SH-MSR3620-0-Ten-GigabitEthernet0/0] description Link-To-SH-S5130-0-XGE1/0/49
SH-S5130-0 switch:
# Enter each interconnect port and add the port description as per the design requirements
[H3C] sysname SH-S5130-0
[SH-S5130-0] interface Ten-GigabitEthernet 1/0/49
[SH-S5130-0-Ten-GigabitEthernet1/0/49] description Link-To-SH-XGE0/O
```
2) Configure the DHCP.
In the Shanghai research institute, assign IP addresses to PCs accessed with the DHCP
mode, and use MSR3620 router as the DHCP server. Please fill in the blanks below with
the specific DHCP configurations:
O
# Configure the XGO0/0 interface with an IP address that is the gateway for all PCs on the
internal network
```cmd
[SH-MSR3620-0] interface Ten-GigabitEthernet 0/0
[SH-MSR3620-0-Ten-GigabitEthernet0/0] ip address 192.168.6.254 24
[SH-MSR3620-0-Ten-GigabitEthernet0/0] quit 
# Enable the DHCP function, create the DHCP address pool 1, and specify the gateway ammo
address and DNS server address
[SH-MSR3620-0] dhcp enable
[SH-MSR3620-0] dhcp server ip-pool pool1
[SH-MSR3620-0-dhcp-pool-poo11] network 192.168.6.0 24
[SH-MSR3620-0-dhcp-pool-poo11] gateway-list 192.168.6.254
[SH-MSR3620-0-dhcp-pool-pool1] dns-list 202.102.99.68
# Configure the IP addresses in the DHCP address pool that do not participate in autoassignment
[SH-MSR3620-0-dhcp-pool-poo11] forbidden-ip 192.168.6.254
[SH-MSR3620-0-dhcp-pool-pool1] forbidden-ip 192.168.6.250
3) Configure the management address of the switch.
# Enter the L3 virtual interface of VLAN 1 and configure the IP address for the L3 interface
[SH-S5130-0] interface vlan 1
[SH-S5130-0-Vlan-interface1] ip address 192.168.6.250 24
[SH-S5130-0-Vlan-interface1] quit
#
[SH-S5130-0] ip route-static 0.0.0.0 0 192.168.6.254
```
Lab Task 2: Deploying the WAN
To ensure the security of data transmission, use private lines to connect the headquarter
and branch offices. As per customer requirements, ISPs can provide various types of private
lines such as bare fibers, L2 private lines (SDHs), and L3 private lines (VPNs) that are
commonly used and featured with high security in data transmission. Currently, small- and
medium-sized enterprises generally use VPN private lines as they are affordable.
VPN is not configured as this experiment does not involve the technical knowledge of VPN.
Configure the private network IP address of each port directly according to the plan, instead
of configuring public network IP addresses, as shown in Figure 15-12.
Figure 15-12 Connection diagram of WAN between the headquarter and branch offices
Beijing
headquarter ROUTER G0/2
G0/1
Shenzhen
office
G0/3
G0/1
Shanghai research
institute
1) Configure the BJ-MSR3620-0.
# Add descriptions of G0/2 and GO/3 interfaces, and configure IP addresses as planned
```cmd
[BJ-MSR3620-0] interface GigabitEthernet 0/2
[BJ-MSR3620-0-GigabitEthernet0/2] description Link-To-SZ-MSR3620-0-G0/1
[BJ-MSR3620-0-GigabitEthernet0/2] ip address 192.168.0.5 30
[BJ-MSR3620-0] interface GigabitEthernet 0/3
[BJ-MSR3620-0-GigabitEthernet0/3] description Link-To-SH-MSR3620-0-G0/1
[BJ-MSR3620-0-GigabitEthernet0/3] ip address 192.168.0.9 30
```
2) Configure the SZ-MSR3620-0. 
# Add the description of GO/1 interface, and configure IP address of the G0/1 interface as
planned
```cmd
[SZ-MSR3620-0] interface GigabitEthernet 0/1
[SZ-MSR3620-0-GigabitEthernet0/1] description Link-To-BJ-MSR3620-0-G0/2
[SZ-MSR3620-0-GigabitEthernet0/1] ip address 192.168.0.6 30
```
# Add the description of GO/1 interface, and configure IP address of the GO/1 interface as
3) Configure the SH-MSR3620-0.
planned
```cmd
[SH-MSR3620-0] interface GigabitEthernet 0/1
[SH-MSR3620-0-GigabitEthernet0/1] description Link-To-BJ-MSR3620-0-GO/3
[SH-MSR3620-0-GigabitEthernet0/1] ip address 192.168.0.10 30
```
Lab Task 3: Deploying the routing 
Considering the future development strategy and plan of the XX company, we adopt
dynamic OSPF protocols in the entire network when deploying the routing. We also plan
each branch office as an area; therefore, the company only needs to add areas to expand
the network, without re-planning the entire network. To facilitate the control of the branch
offices' access to the external network, the public network egress of the network is located
at the headquarter in the plan, so that employees of the company can access the external
network through the unified egress. A default routing is used between the headquarter and
the external network.
The specific network plan is shown in Figure 15-13: 
1) According to the previous routing plan, configure the OSPF routing protocol of the BJS6520XE-0 switch.
The manually specified router ID is the interface address of VLAN 1
```cmd
[BJ-S6520XE-0] router id 192.168.0.25
```
When releasing a network segment in an area, the network segment is followed by an antimask. However, it is difficult for us to calculate the anti-masks of some addresses. CMW
can automatically convert a mask to an anti-mask. To demonstrate this feature, we use a
mask when releasing the network segment and check whether it can automatically convert
the mask to an anti-mask.
```cmd
[BJ-S6520XE-0] ospf
[BJ-S6520XE-0-ospf-1] area 1
[BJ-S6520XE-0-ospf-1-area-0.0.0.1] network 192.168.1.0 255.255.255.0
Check whether it can automatically convert the mask to an anti-mask
[BJ-S6520XE-0-ospf-1-area-0.0.0.1] display this
#
area 0.0.0.1
network 192.168.1.0 0.0.0.255
#
# Continue to release the network segments where VLAN 20, VLAN 30, and VLAN 40 reside
return
[BJ-S6520XE-0-ospf-1-area-0.0.0.1] network 192.168.2.0 0.0.0.255
[BJ-S6520XE-0-ospf-1-area-0.0.0.1] network 192.168.3.0 0.0.0.255
[BJ-S6520XE-0-ospf-1-area-0.0.0.1] network 192.168.4.0 0.0.0.255
# Release the address of the interface interconnected with the BJ router
[BJ-S6520XE-0-ospf-1-area-0.0.0.1] network 192.168.0.1 0.0.0.3
#Release the network segment of the switch's management VLAN address
[BJ-S6520XE-0-ospf-1-area-0.0.0.1] network 192.168.0.24 0.0.0.7
```
Figure 15-13 Routing plan
Beijing
headquarter
OA
Area 1
Internet 
ISP private line
Shanghai
research institute
Shenzhen
office Area 0
Area 2 Area 3
2) According to the previous routing plan, configure the OSPF routing of the BJ-MSR3620-
0 router.
# Create and enter the loopback 0 interface, configure the IP address of the interface (note
that the mask has 32 bits), and specify the router ID manually
```cmd

[BJ-MSR3620-0] interface loopback 0
[BJ-MSR3620-0-LoopBack0] ip address 192.168.0.17 32
[BJ-MSR3620-0] router id 192.168.0.17 ammou@mai
# Create and enter area 1, and release the network segment interconnected with the BJS6520XE-0
[BJ-MSR3620-0] ospf
-[BJ-MSR3620-0-ospf-1] area 1
[BJ-MSR3620-0-ospf-1-area-0.0.0.1] network 192.168.0.0 0.0.0.3
ed
# Release the loopback interface address (as the network management address) to
facilitate the management
[BJ-MSR3620-0-ospf-1-area-0.0.0.1] network 192.168.0.17 0.0.0.0
# Create and enter area 0, and release the network segments interconnected with the SZMSR3620-0 and SH-MSR3620-0
[BJ-MSR3620-0-ospf-1] area 0
[BJ-MSR3620-0-ospf-1-area-0.0.0.0] network 192.168.0.4 0.0.0.3
[BJ-MSR3620-0-ospf-1-area-0.0.0.0] network 192.168.0.8 0.0.0.3
```
3) According to the previous routing plan, configure the OSPF protocol of the SZMSR3620-0.
# Create and enter the loopback 0 interface, configure the IP address of the interface (note
that the mask has 32 bits), and specify the router ID manually
```cmd
[SZ-MSR3620-0] interface loop 0
[SZ-MSR3620-0-LoopBack0] ip address 192.168.0.18 32 ail.mbc.edu.
# Enter area 0, and release the network segment interconnected with the BJ-MSR3620-0
[SZ-MSR3620-0] router id 192.168.0.18
[SZ-MSR3620-0] ospf
[SZ-MSR3620-0-ospf-1] area 0
[SZ-MSR3620-0-ospf-1-area-0.0.0.0] network 192.168.0.4 0.0.0.3
# Enter area 2, and release the network segment address interconnected with the internal
network of the Shenzhen office
[SZ-MSR3620-0-ospf-1] area 2 ed
edu.
[SZ-MSR3620-0-ospf-1-area-0.0.0.2] network 192.168.5.0 0.0.0.255
#Release the loopback address (as the network management address)
[SZ-MSR3620-0-ospf-1-area-0.0.0.2] network 192.168.0.18 0.0.0.0 
According to the previous routing plan, configure the OSPF protocol of the SHMSR3620-0.
# Create and enter the loopback 0 interface, configure the IP address of the interface (note
that the mask has 32 bits), and specify the router ID manually
[SH-MSR3620-0] interface loop 0
[SH-MSR3620-0-LoopBack0] ip address 192.168.0.19 32
# Enter area 0, and release the network segment interconnected with the BJ-MSR3620-0
[SH-MSR3620-0] router id 192.168.0.19
[SH-MSR3620-0] ospf
[SH-MSR3620-0-ospf-1] area 0
[SH-MSR3620-0-ospf-1-area-0.0.0.0] network 192.168.0.8 0.0.0.3
# Enter area 3, and release the network segment interconnected with the internal network
of the Shanghai research institute
[SH-MSR3620-0-ospf-1] area 3
[SH-MSR3620-0-ospf-1-area-0.0.0.3] network 192.168.6.0 0.0.0.255
# Release the loopback address (as the network management address)
[SH-MSR3620-0-ospf-1-area-0.0.0.3] network 192.168.0.19 0.0.0.0
```
5) Configure a routing on the egress router for the headquarter and branch offices to
# Configure a default routing, point the next hop to the gateway address provided by the
ISP, and release the default routing to OSPF
access the Internet.
```cmd
[BJ-MSR3620-0-GigabitEthernet0/1] ip address 202.38.160.2 30
ammc [BJ-MSR3620-0] ip route-static 0.0.0.0 0 202.38.160.1
[BJ-MSR3620-0] ospf
[BJ-MSR3620-0-ospf-1] default-route-advertise
```
Lab Task 4: Deploying network security mo
1) The internal network uses private addresses. To access the Internet, address
translation is required. Servers on the internal network can be released to the Internet.
# Create ACL 2000, enter the interface connected to the Internet, and use Easy IP to enable
NAT
```cmd
[BJ-MSR3620-0] acl basic 2000 ammo
[BJ-MSR3620-0-acl-ipv4-basic-2000] rule 0 permit
[BJ-MSR3620-0] interface g 0/1
[BJ-MSR3620-0-GigabitEthernet0/1] nat outbound 2000
```
# WWW and OA servers at the headquarter need to be released. The address of the WWW
server is 192.168.4.131, the address of the OA server is 192.168.4.130, and the port
number is 8080
```cmd
[BJ-MSR3620-0-GigabitEthernet0/1] nat server protocol tcp global 202.38.160.2 http inside
192.168.4.131 http
[BJ-MSR3620-0-GigabitEthernet0/1] nat server protocol tcp global 202.38.160.2 8080
inside 192.168.4.130 8080
```
2) Configure attack prevention.
# Create and configure ACL 3001 Lamm
Common viruses and ports attacked are shown below. All can be configured to ACL 3001.
The following are some configuration commands commonly used in the current network and
are for reference only.
```cmd
[BJ-MSR3620-0]acl advanced 3001
rule 0 deny tcp source-port eq 3127
rule 1 deny tcp source-port eq 1025 1
rule 2 deny tcp source-port eq 5554
rule 3 deny tcp source-port eq 9996
rule 4 deny tcp source-port eq 1068
rule 5 deny tcp source-port eq 135
rule 6 deny udp source-port eq 135
rule 7 deny tcp source-port
rule 8 deny udp source-port
eq 137
eq netbios-ns
rule 9 deny tcp source-port eq 138
rule 10 deny upp source-port
rule 11 deny tcp source-port
rule 12 deny udp source-port
rule 13 deny tcp source-port
rule 14 deny tcp source-port
eq netbios-dgm
eq 139
eq netbios-ssn
eg 593
rule 15 deny tcp source-port eq 5800
eq 4444
rule 16 deny tcp source-port eq 5900
rule 18 deny tcp
rule 19 deny tcp source-port eq 445
rule 20 deny udp source-port eq 445
source-port eq 8998
rule 21 deny udp source-port eq 1434
rule 30 deny tcp destination-port eq 3127
rule 31 deny tcp destination-port eq 1025
rule 32 deny tcp destination-port eq 5554
rule 33 deny tcp destination-port eq 9996
rule 34 deny tcp destination-port eg 1068
rule 35 deny tcp destination-port eq 135
rule 36 deny upp destination-port eq 135
rule 37 deny tcp
destination-port eq 137
rule 38 deny udp destination-port eq netbios-ns
rule 39 deny tcp destination-port eg 138
rule 40 deny udp destination-port eq netbios-dgm
rule 41 deny tcp destination-port eq 139
rule 42 deny udp destination-port eq netbios-ssn
rule 43 deny tcp destination-port eq 593
rule 44 deny tcp destination-port eq 4444
rule 45 deny tcp destination-port eg 5800
rule 46 deny tcp destination-port eq 5900
-rule 48 deny tcp destination-port eq 8998
rule 49 deny tcp destination-port eq 445
rule 50 deny udp destination-port eq 445
rule 51 deny udp destination-port eq 1434
```
# Apply ACL 3001 in the IN direction of the external network interface to prevent intrusion
from the external network e
```cmd
[BJ-MSR3620-0-GigabitEthernet0/1] packet-filter 3001 inbound
```







