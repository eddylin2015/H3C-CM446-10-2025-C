# Lab08_DHCP 

- DHCP Protocal
- DHCP server
- DHCP relay

## Lab Diagram
![](https://github.com/eddylin2015/H3C-CM446-10-2025-C/blob/main/img/hcl_fb01f9657265.png?raw=true)

## Lab Process
### TASK1
####  RTA
RTA G0/0 172.16.0.1/24
```cmd
#
 [RTA]dhcp enable
 [RTA]dhcp server forbidden-ip 172.16.1.1
 [RTA]dhcp server ip-pool pool1
 [RTA-dhcp-pool-pool1]network 172.16.1.0 mask 255.255.255.0
 [RTA-dhcp-pool-pool1]gateway-list 172.16.1.1
 [RTA]disp dhcp server pool
#
 [RTA]disp dhcp server statistics
# 
 [RTA]disp dhcp server ip-in-use
# 
 [RTA]disp dhcp server free-ip
# 
interface GigabitEthernet0/0
 ip address 172.16.0.1 255.255.255.0
 ip route-static 172.16.1.0 24 172.16.0.1
```
### TASK2
- SWA G1/0/1 172.16.1.1/24 Vlan-interface1
- SWA G1/0/2 172.16.0.1/24 Vlan-interface2
- RTA G0/0 172.16.0.2./24
#### SWA
```cmd
[SWA]vlan 2
[SWA vlan 2]port GigabitEthernet 1/0/2
[SWA]interface Vlan-interface1
[SWA interface Vlan-interface1]ip address 172.16.1.1 255.255.255.0
[SWA]interface Vlan-interface2
[SWA interface Vlan-interface2]ip address 172.16.0.1 255.255.255.0

[SWA]dhcp enable
[SWA]interface Vlan-interface1
[SWA interface Vlan-interface1]dhcp select relay
[SWA interface Vlan-interface1]dhcp relay server-address 172.16.0.2
#
```


####  RTA
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
#
 [RTA]disp dhcp server statistics
# 
 [RTA]disp dhcp server ip-in-use
# 
 [RTA]disp dhcp server free-ip
```

## cmd list

- dhcp enable
- network net-add [mask-len|mask]
- gateway-list ip-add
- dhcp server forbidden-ip low-ip-add [high-ip-add]
- dhcp server ip-pool pool-name
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


