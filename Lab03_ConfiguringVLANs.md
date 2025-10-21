# Lab03_ConfiguringVLANs

## Purpose

- config Vlans to isolate layer2 traffic between hosts
- config access ports and trunk ports

## Lab Diagram

hcl_dccd23614469


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