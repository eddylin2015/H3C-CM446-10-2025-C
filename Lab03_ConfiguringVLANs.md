# Lab03_ConfiguringVLANs

## Purpose

- config Vlans to isolate layer2 traffic between hosts
- config access ports and trunk ports

## Lab Diagram

![](https://github.com/eddylin2015/H3C-CM446-10-2025-C/blob/main/img/hcl_dccd23614469.png?raw=true)

## Lab Process

task1 ACCESS Link port

step1

step2

step3 VLAN 並 port

step4 test VLAN

task2 Trunk



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