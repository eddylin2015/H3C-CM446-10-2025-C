# Lab06_配置鏈路聚合configLinkAggregation 

## 內容目標
- Link-Aggregatoin basic
- Link-Aggregatoin config

## Lab Diagram

![](https://github.com/eddylin2015/H3C-CM446-10-2025-C/blob/main/img/hcl_ab7a55429965.png?raw=true)

## Task1
```cmd
 sysname PCA
#
interface GigabitEthernet0/0/1
 combo enable copper
 ip address 172.16.0.1 255.255.255.0
#
```
```cmd
 sysname PCB
#
interface GigabitEthernet0/0/1
 combo enable copper
 ip address 172.16.0.2 255.255.255.0
#
```
```cmd
 sysname SWA
#
vlan 1
#
 stp global enable
#
interface Bridge-Aggregation1
#
interface GigabitEthernet1/0/23
 port link-mode bridge
 combo enable fiber
 port link-aggregation group 1
#
interface GigabitEthernet1/0/24
 port link-mode bridge
 combo enable fiber
 port link-aggregation group 1
#
```
```cmd
 sysname SWB
#
 irf mac-address persistent timer
 irf auto-update enable
 undo irf link-delay
 irf member 1 priority 1
#
 lldp global enable
#
 system-working-mode standard
 xbar load-single
 password-recovery enable
 lpu-type f-series
#
vlan 1
#
 stp global enable
#
interface Bridge-Aggregation1
#
interface NULL0
#
interface FortyGigE1/0/53
 port link-mode bridge
#
interface FortyGigE1/0/54
 port link-mode bridge
#
interface GigabitEthernet1/0/23
 port link-mode bridge
 combo enable fiber
 port link-aggregation group 1
#
interface GigabitEthernet1/0/24
 port link-mode bridge
 combo enable fiber
 port link-aggregation group 1
#

```
## cmd list
- interface bridge-aggregation interface-num
- port link-aggregation group number
- disp link-aggregation summary
## 思考題

## 總結
完成


