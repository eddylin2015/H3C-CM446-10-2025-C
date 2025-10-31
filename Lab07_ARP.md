# Lab07_ARP 

## Lab Diagram

![](https://github.com/eddylin2015/H3C-CM446-10-2025-C/blob/main/img/hcl_fe8510543865.png?raw=true)


## Task1

```cmd
 sysname H3C
#
interface GigabitEthernet0/0/1
 combo enable copper
 ip address 172.16.0.1 255.255.0.0
#
```


```cmd
 sysname H3C
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
interface Serial1/0
#
interface Serial2/0
#
interface Serial3/0
#
interface Serial4/0
#
interface NULL0
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
interface GigabitEthernet0/2
 port link-mode route
 combo enable copper
#
interface GigabitEthernet5/0
 port link-mode route
 combo enable copper
#
interface GigabitEthernet5/1
 port link-mode route
 combo enable copper
#
interface GigabitEthernet6/0
 port link-mode route
 combo enable copper
#
interface GigabitEthernet6/1
 port link-mode route
 combo enable copper
#
 scheduler logfile size 16
#
line class aux
 user-role network-operator
#
line class console
 user-role network-admin
#
line class tty
 user-role network-operator
#
line class vty
 user-role network-operator
#
line aux 0
 user-role network-operator
#
line con 0
 user-role network-admin
#
line vty 0 63
 user-role network-operator
#
domain system
#
 domain default enable system
#
role name level-0
 description Predefined level-0 role
#
role name level-1
 description Predefined level-1 role
#
role name level-2
 description Predefined level-2 role
#
role name level-3
 description Predefined level-3 role
#
role name level-4
 description Predefined level-4 role
#
role name level-5
 description Predefined level-5 role
#
role name level-6
 description Predefined level-6 role
#
role name level-7
 description Predefined level-7 role
#
role name level-8
 description Predefined level-8 role
#
role name level-9
 description Predefined level-9 role
#
role name level-10
 description Predefined level-10 role
#
role name level-11
 description Predefined level-11 role
#
role name level-12
 description Predefined level-12 role
#
role name level-13
 description Predefined level-13 role
#
role name level-14
 description Predefined level-14 role
#
user-group system
#
```

## cmd list
- proxy-arp enable
- disp arp all
## 總結
完成


