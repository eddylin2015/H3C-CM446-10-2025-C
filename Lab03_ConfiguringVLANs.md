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
~ step2: VLAN
```cmd
[SWA]disp vlan
[SWA]disp vlan 1
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
~ step3 VLAN 並 port
```cmd
[SWA]vlan 2
[SWA-vlan2]port G 1/0/1
```
```cmd
[SWB]vlan 2
[SWB-vlan2]port G 1/0/1
[SWB]disp vlan
Vlans exist: 1,2
[SWB]disp vlan 2
VLAN ID:2 static 
Tagged Ports: 
   None
Untagged Ports:
   G1/0/1
```

~ step4 test VLAN

```cmd
[PCA]ping 172.16.0.3
```

~ task2 Trunk
```cmd
[SWA]interf G 1/0/24
[SWA-G1/0/24]port link-type trunk
[SWA-G1/0/24]port trunk permit vlan all
```
```cmd
[SWB]interf G 1/0/24
[SWB-G1/0/24]port link-type trunk
[SWB-G1/0/24]port trunk permit vlan all
[SWB]disp valn 2
Tagged Ports
   G1/0/24
UnTagged Ports:
   G1/0/1
[SWB]disp interf G 1/0/24
PVID:1
Mdi type: auto
Port link-type:trunk
  vlan passing:1 ,2
  vlan permited: 1,2-4094
  Trunk port encapsulation: IEEE 802.1q
```

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
