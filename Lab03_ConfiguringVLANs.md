# Lab03_ConfiguringVLANs

## Purpose

- config Vlans to isolate layer2 traffic between hosts
- config access ports and trunk ports

## Lab Diagram

![](https://github.com/eddylin2015/H3C-CM446-10-2025-C/blob/main/img/hcl_dccd23614469.png?raw=true)

## Lab Process

~ task1 ACCESS Link port

~ step1
```cmd
<SWA>disp version
<SWA>reset saved-configuration
<SWA>reboot
```
## 步骤二:观察缺省 VLAN
```cmd
[SWA]disp vlan
The following VLANs exist:
1 (default)

[SWA]disp vlan 1
VLAN ID: 1
Route Interface: not confiqured
VLAN Type: static
Description: VLAN 0001
Tagged Ports: none
Untagged Ports:
GigabitEthernet1/0/1 GigabitEthernet1/0/2
GigabitEthernet1/0/3 GigabitEthernet1/0/4

[SWA]disp interface G 1/0/1
'''
PVID: 1
Mdi type: auto
Port link-type: access
  Tagged VLAN ID: none
  Untagged VLAN ID: 1
Port Priority: 0
'''
```
### 步骤三:配置 VLAN 并添加端口 
```cmd
#SWA
[SWA]vlan 2
[SWA-vlan2]port G 1/0/1

#SWB
[SWB]vlan 2
[SWB-vlan2]port G 1/0/1
```
在交换机上查看有关 VLAN 2的信息,如
```cmd
[SWB]disp vlan
Vlans exist: 1,2

[SWA]display vlan 2
VLAN ID: 2
VLAN type: Static
Route interface: Not configurea
Description: VLAN 0002
Name: VLAN 0002
Tagged Ports: none
Untagged Ports:
GigabitEthernet1/0/1

[SWB]display vlan
The following VLANs exist.
1 (default), 2

[SWB]display vlan 2
VLAN ID: 2
VLAN type: Static
Route interface: Not configured
Description: VLAN 0002
Name: VLAN 0002
Tagged ports: None
Untagged Ports:
GigabitEthernet1/0/1
```
### 步骤四:测试 VLAN 间的隔离
|设备名称 |IP地址| 网关|
|--------|-----|-----|
|PCA |172.16.0.1/24|
|PCB |172.16.0.2/24|
|PCC |172.16.0.3/24|
|PCD |172.16.0.4/24|

配置完成后,在PCA 上用 Ping 命令来测试到其它 PC 的互通性。其结果应该是PCA与
PCB 不能够互通,PCC 和PCD不能够互通。证明不同VLAN 之间不能互通,连接在同一交换
机上的PC被隔离了。


## 实验任务二:配置 Trunk 链路端口 
本实验任务是在交换机间配置 Trunk 链路端口,来使同一 VLAN 中的PC 能够跨交换机访
问。通过本实验,学员应该能够掌握 Trunk 链路端口的配置及作用。
### 步骤一:跨交换机 VLAN 互通测试
在上个实验中,PCA 和PCC 都属于VLAN 2。在PCA 上用 Ping 命令来测试与PCC 能否
互通。其结果应该是不能,
```cmd
[PCA]ping 172.16.0.3
```
PCA与PCC 之间不能互通。因为交换机之间的端口 GigabitEthernet 1/0/24 是 Access 链
路端口,且属于VLAN 1,不允许VLAN 2的数据帧通过。

要想让VLAN 2数据帧通过端口GigabitEthernet 1/0/24,需要设置端口为Trunk 链路端口。
### 步骤二:配置 Trunk 链路端口
```cmd
#@SWA
[SWA]interf G 1/0/24
[SWA-G1/0/24]port link-type trunk
[SWA-G1/0/24]port trunk permit vlan all

#@SWB
[SWB]interf G 1/0/24
[SWB-G1/0/24]port link-type trunk
[SWB-G1/0/24]port trunk permit vlan all

#配置完成后,查看 VLAN 2 信息:
<SWA>display vlan 2
VLAN ID: 2
VLAN type: Static
Route interface: Not configured
Description: VLAN 0002
Name: VLAN 0002
Tagged Ports:
  GigabitEthernet1/0/24
Untagged Ports:
  GigabitEthernet1/0/1
```
可以看到,VLAN 2中包含了端口 GigabitEthernet 1/0/24,且数据帧是以带有标签(Tagged)
的形式通过端口的。再查看端口 GigabitEthernet 1/0/24 信息:
```cmd
<SWA>display interface GigabitEthernet 1/0/24
PVID: 1
Mdi type: auto
Port link-type: trunk
VLAN passing : 1(default vlan), 2
VLAN permitted: 1(default vlan), 2-4094
Trunk port encapsulation: IEEE 802.1q
```
从以上信息可知,端口的PVID 值是1, 端口类型是 Trunk,允许所有的VLAN (1-4094)
通过,但实际上是VLAN 1和VLAN 2能够通过此端口(因为交换机上仅有VLAN 1和VLAN 2)。
SWB上VLAN 和端口 GigabitEthernet 1/0/24 的信息与此类似,不再赘述。
### 步骤三:跨交换机 VLAN 互通测试
在PCA 上用 Ping 命令来测试与PCC 能否互通。其结果应该是能够互通,如下所示:

## SWA/SWA curr-config

```cmd
[SWA]disp current-configuration
interface GigabitEthernet1/0/1
 port link-mode bridge
 port access vlan 2
 combo enable fiber
#
interface GigabitEthernet1/0/24
 port link-mode bridge
 port link-type trunk
 port trunk permit vlan all
 combo enable fiber
```

## cmd list

- disp vlan
- disp interface [interface-type [interf-number]]
- disp vlan vlan-id
- port interf-list
- port link-type {access|hybrid|trunk}
- port trunk permit vlan {vlan-id-list|all}

## 結語

完成LAB3.
