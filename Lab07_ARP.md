# Lab07_ARP 

## Lab Diagram

![](https://github.com/eddylin2015/H3C-CM446-10-2025-C/blob/main/img/hcl_fe8510543865.png?raw=true)


## Task1

```cmd
 sysname PCA
#
interface GigabitEthernet0/0/1
 combo enable copper
 ip address 172.16.0.1 255.255.0.0
#
```


```cmd
 sysname PCB
#
interface GigabitEthernet0/0/1
 combo enable copper
 ip address 172.16.1.1 255.255.0.0
#
```

```cmd
 sysname RTA
#
vlan 1
#
interface GigabitEthernet0/0
 port link-mode route
 combo enable copper
 ip address 172.16.0.254 255.255.255.0
 proxy-arp enable
#
interface GigabitEthernet0/1
 port link-mode route
 combo enable copper
 ip address 172.16.1.254 255.255.255.0
 proxy-arp enable
#
```

## cmd list
- proxy-arp enable
- disp arp all
## 總結
完成


