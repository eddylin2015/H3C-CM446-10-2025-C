# Lab08_DHCP 

- DHCP Protocal
- DHCP server
- DHCP relay

## Lab Diagram
![](https://github.com/eddylin2015/H3C-CM446-10-2025-C/blob/main/img/hcl_fb01f9657265.png?raw=true)

## Lab Process
### TASK1实验任务一:PCA 直接通过 RTA 获得 IP 地址
####  步骤二:在路由器接口配置 IP 地址
RTA G0/0 172.16.0.1/24
```cmd
[RTA-GigabitEthernet0/0]ip address 172.16.0.1 24
```
### 步骤三:配置 RTA 作为 DHCP 服务器

```cmd
#
 [RTA]dhcp enable
 [RTA]dhcp server forbidden-ip 172.16.1.1
 [RTA]dhcp server ip-pool pool1
 [RTA-dhcp-pool-pool1]network 172.16.1.0 mask 255.255.255.0
 [RTA-dhcp-pool-pool1]gateway-list 172.16.1.1
 #配置完成后,可以用以下命令来查看 RTA 上DHCP 地址池相关配置,
 [RTA]disp dhcp server pool
Pool name: pool1
Network: 172.16.0.0 mask 255.255.255.0
expired day 1 hour 0 minute 0 second 0
gateway-list 172.16.0.1
#查看 DHCP服务器相关信息
 [RTA]disp dhcp server statistics
# RTA 上用 display dhep server ip-in-use来查看 DHCP服务器已分配的IP地址:
 [RTA]disp dhcp server ip-in-use
# 用 display dhcp server free-ip 来查看 DHCP服务器可供分配的IP地址资源:
 [RTA]disp dhcp server free-ip
# 
interface GigabitEthernet0/0
 ip address 172.16.0.1 255.255.255.0
 ip route-static 172.16.1.0 24 172.16.0.1
```
## TASK2实验任务二:PCA 通过 DHCP 中继方式获得 IP地址

### 步骤二:在设备上配置 IP地址及路由
- SWA G1/0/1 172.16.1.1/24 Vlan-interface1
- SWA G1/0/2 172.16.0.1/24 Vlan-interface2
- RTA G0/0 172.16.0.2./24
在SWA上配置 VLAN 虚接口及IP
```cmd
[SWA]vlan 2
[SWA vlan 2]port GigabitEthernet 1/0/2
[SWA]interface Vlan-interface1
[SWA interface Vlan-interface1]ip address 172.16.1.1 255.255.255.0
[SWA]interface Vlan-interface2
[SWA interface Vlan-interface2]ip address 172.16.0.1 255.255.255.0
```
在RTA 上配置接口 IP 及静态路由:
```cmd
[RTA-GigabitEthernet0/0]ip address 172.16.0.2 24
[RTA]ip route-static 172.16.1.0 24 172.16.0.1
```
### 步骤三:在RTA 上配置 DHCP服务器及在 SWA 上配置 DHCP 中继
配置 RTA:
```cmd
# 
interface GigabitEthernet0/0
 ip address 172.16.0.2 255.255.255.0
 ip route-static 172.16.1.0 24 172.16.0.1
#
 [RTA]dhcp enable
 [RTA]dhcp server forbidden-ip 172.16.1.1
 [RTA]dhcp server ip-pool pool1
 [RTA-dhcp-pool-pool1]network 172.16.1.0 mask 255.255.255.0
 [RTA-dhcp-pool-pool1]gateway-list 172.16.1.1
# 
 [RTA]disp dhcp server pool
 [RTA]disp dhcp server statistics
 [RTA]disp dhcp server ip-in-use
 [RTA]disp dhcp server free-ip
```
配置 SWA:
```cmd
[SWA]dhcp enable
[SWA]interface Vlan-interface1
[SWA interface Vlan-interface1]dhcp select relay
[SWA interface Vlan-interface1]dhcp relay server-address 172.16.0.2
#
```
### 步骤四:PCA 通过 DHCP 中继获取 IP地址

### 步骤五:查看 DHCP 中继相关信息

```cmd

<SWA>display dhcp relay server-address
Interface name Server IP address
Vlan-interfacel 172.16.0.2

<SWA>display dhcp relay statistics
```

## cmd list

- dhcp enable 使能DHCP服务
- network network-address [mask-length | mask mask] 配置动态分配的IP地址范围
- gateway-list ip-address 配置为DHCP客户端分配的网关地址
- dhcp server forbidden-ip low-ip-address [ high-ip-address ]配置DHCP地址池中不参与自动分配的IP地址
- dhcp server ip-pool pool-name 创建DHCP地址池并进入DHCP地址池视图
- dhcp server forbidden-ip low-ip-add [high-ip-add]
- dhcp relay server-group group-id ip ip-add
- dhcp select relay
- dhcp relay server-add ip-add
- disp dhcp sever free-ip
- disp dhcp sever forbidden-ip
- disp dhcp sever statistics
- disp dhcp relay server-add [interface interface-type - interface-num]
- disp dhcp relay statistics [interface interface-type - interface-num]

## 總結

完成



