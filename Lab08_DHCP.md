# Lab08_DHCP 

- DHCP Protocal
- DHCP server
- DHCP relay
## Lab Diagram
![](https://github.com/eddylin2015/H3C-CM446-10-2025-C/blob/main/img/hcl_fb01f9657265.png?raw=true)

## Lab Process
###  RTA
```cmd
#
 [RTA]dhcp enable
 [RTA]dhcp server forbidden-ip 172.16.1.1
 [RTA]dhcp server ip-pool pool1
 [RTA dhcp ip-pool pool1]network 172.16.1.0 mask 255.255.255.0
 [RTA dhcp ip-pool pool1]gateway-list 172.16.1.1
 [RTA]disp dhcp server pool
#
interface GigabitEthernet0/0
 ip address 172.16.0.2 255.255.255.0
 ip route-static 172.16.1.0 24 172.16.0.1
```

### SWA
```cmd

[SWA]vlan 2
[SWA vlan 2]port GigabitEthernet 1/0/2
interface Vlan-interface1
 ip address 172.16.1.1 255.255.255.0
 dhcp select relay
 dhcp relay server-address 172.16.0.2
#
interface Vlan-interface2
 ip address 172.16.0.1 255.255.255.0
```


## 總結

完成


