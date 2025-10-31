# lab4 生成樹Config the Spanning Tree Feature

## Purpose

- Understand the basic principle of STP.
- Master the basic method of config STP.

## Lab Diagram

![](https://github.com/eddylin2015/H3C-CM446-10-2025-C/blob/main/img/hcl_aefc21344210.png?raw=true)

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

## Lab Process
```cmd
 sysname SWA
#
vlan 1
#
 stp instance 0 priority 0
 stp global enable
#
interface GigabitEthernet1/0/1
 port link-mode bridge
 combo enable fiber
 stp edged-port
#
disp stp 
```
```cmd
 sysname SWB
#
vlan 1
#
 stp instance 0 priority 4096
 stp global enable
#
interface GigabitEthernet1/0/1
 port link-mode bridge
 combo enable fiber
 stp edged-port
#
disp stp 
```

## cmd list

- stp global enable
- stp mode {mstp| pvst| rstp| stp}
- stp [instance instance-list|vlan vlan-id-list] priority priority
- stp edged-port
- disp stp [instance instance-list|vlan vlan-id-list] [interface interface-list|slot slot-number] [brief]


## 結語:

完成
