# Lab11_configOSPF 

- config an OSPF area
- config the OSPF DR priority
- config OSPF costs
- describe OSPF route selection
- config multiple OSPF area

Familiarity with OSPF working principles and basic OSPF config commands.

## Lab Diagrams

![](https://github.com/eddylin2015/H3C-CM446-10-2025-C/blob/main/img/lab11_labDiagram.png?raw=true)


## Lab Task1:config basic OSPF single-area Settings
- clientA 10.0.0.1/24 10.0.0.2
- clientB 10.1.0.1/24 10.1.0.2
```cmd
[RTA]interface g0/0
[RTA interface g0/0]ip addr 20.0.0.1 24
[RTA]interface g0/1
[RTA interface g0/1]ip addr 10.0.0.2 24
[RTA interface g0/1]interface loopback 0
[RTA  loopback 0]ip addr 1.1.1.1 32

[RTB]interface g0/0
[RTB interface g0/0]ip addr 20.0.0.2 24
[RTB]interface g0/1
[RTB interface g0/1]ip addr 10.1.0.2 24
[RTB interface g0/1]interface loopback 0
[RTB  loopback 0]ip addr 2.2.2.2 32
```
### step4 OSPF
```cmd
[RTA]router id 1.1.1.1
[RTA]ospf 1
[RTA ospf 1]area 0.0.0.0
[RTA ospf 1area 0.0.0.0]network 1.1.1.1 0.0.0.0 
[RTA ospf 1area 0.0.0.0]network 10.0.0.0 0.0.0.255 
[RTA ospf 1area 0.0.0.0]network 20.0.0.0 0.0.0.255
```
```cmd
[RTB]router id 2.2.2.2
[RTB]ospf 1
[RTB ospf 1]area 0.0.0.0
[RTB ospf 1area 0.0.0.0]network 2.2.2.2 0.0.0.0 
[RTB ospf 1area 0.0.0.0]network 10.1.0.0 0.0.0.255 
[RTB ospf 1area 0.0.0.0]network 20.0.0.0 0.0.0.255
```
### step5 neighbors and routing tables
```cmd
[RTA]disp ospf peer
[RTA]disp ospf routing
[RTA]disp ip routing-table
```
## Lab Task2:
```cmd
[RTA]interface g0/0
[RTA-GigabitEthernet0/0]ip address 20.0.0.124
[RTA-GigabitEtherneto/0]interface go/1
[RTA-GigabitEthernet0/1]ip address 10.0.0.124
[RTA-GigabitEtherneto/1]interface loopback 0
[RTA-Loopback0]ip address 1.1.1.132
[RTA-Loopback0]quit
[RTA]router id 1.1.1.1
[RTA]ospf1
[RTA-ospf-1]area 0.0.0.0
[RTA-ospf-1-area-0.0.0.0]network 1.1.1.10.0.0.0
[RTA-ospf-1-area-0.0.0.0]network10.0.0.00.0.0.255
[RTA-Ospf-1-area-0.0.0.0]network20.0.0.00.0.0.255

[RTB]interface g0/0
[RTB-GigabitEthernet0/0]ip address 20.0.0.224
[RTB-GigabitEtherneto/0]interface go/1
[RTB-GigabitEthernet0/1]ip address 10.0.0.224
[RTB-GigabitEtherneto/1]interface loopback 0
[RTB-Loopback0]ip address 2.2.2.232
[RTB-Loopback0]quit
[RTB] routerid 2.2.2.2
mbc.
[RTB]ospf1
[RTB-Ospf-1]area 0.0.0.0
[RTB-ospf-1-area-0.0.0.0]network 2.2.2.20.0.0.0
[RTB-ospf-1-area-0.0.0.0]network 10.0.0.00.0.0.255
[RTB-ospf-1-area-0.0.0.0]network20.0.0.00.0.0.255
```
### step3
```cmd
[RTA]display ospf peer
OSPE Process 1 with Router ID 1.1.1.1
Neighbor Brief Information

[RTA]display ospf routing
OSPF Process 1 with Router ID 1.1.1.1 Routing Table
Routing for network Destination Cost Type NextHop AdvRouter Area
20.0.0.0/24 10.0.0.0/24 2.2.2.2/32 11 Transit 0.0.0.0 Transit 0.0.0.0 2.2.2.2 2.2.2.2 0.0.0.0 0.0.0.0
110
2.2.2.2/32 Stub stub 10.0.0.2 20.0.0.2 2.2.2.2 2.2.2.2 0.0.0.0 0.0.0.0
1.1.1.1/32 Stub 0.0.0.0 1.1.1.1 0.0.0.0
Total nets:5
Intra area:5Inter area:0·ASE:0NSSA:0
```
### step4 modify the ospf cost of an interface
```cmd
[RTA]interface g0/0
[RTA-GigabitEthernet0/0]ospf cost150
```
### step5 
```cmd
[RTA]display ospf routing

[RTA-GigabitEthernet0/0]display ip routing-table
Destinations:18 Routes:18
Destination/Mask Proto Pre Cost NextHop Interface
1.1.1.1/32 10.0.0.0/24 0.0.0.0/32 2.2.2.2/32 Direct 0 Direct00 O_INTRA 10 Direct 0 0 0 1 0 127.0.0.1 127.0.0.1 10.0.0.2 10.0.0.1 InLoop0 GE0/1 InLoop0 GE0/1
10.0.0.0/32 10.0.0.1/32 Direct Direct 0 0 0 0 10.0.0.1 127.0.0.1 GE0/1 InLoop0
10.0.0.255/32 Direct 0 0 10.0.0.1 GE0/1
20.0.0.0/24 Direct 0 0 20.0.0.1 GE0/0
20.0.0.0/32 20.0.0.1/32 Direct Direct 0 0 0 0 20.0.0.1 127.0.0.1 GE0/0 InLoop0
20.0.0.255/32 Direct 0 0 20.0.0.1 GE0/0
127.0.0.1/32 127.0.0.0/32 224.0.0.0/4 127.255.255.255/32Direct0 127.0.0.0/8 Direct00 Direct Direct 0 Direct 0 0 0 0 0 0 127.0.0.1 0.0.0.0 127.0.0.1 127.0.0.1 127.0.0.1 NULLO InLoop0 InLoopO InLoop0 InLoopo
224.0.0.0/24 Direct0 0 0.0.0.0 NULLO
255.255.255.255/32Direct0 0 127.0.0.1 InLoop0
```
### step6 modify the ospf DR priority of an interface
```cmd
[RTB]interface g0/0
[RTB-GigabitEthernet0/1]ospf dr-priority0
```
### step7 restart the ospf process on the routers
```cmd
<RTB>reset ospf 1process
Warning:Reset OSPE process?[Y/N]:y

<RTA>reset ospf 1process
```
### step8 check the state ospf neighbors.
```cmd
[RTA]display ospf peer
OSPF Process 1 with Router ID 1.1.1.1
mbc.
Neighbor Brief Information
Area:0.0.0.0 Router ID 202.2.2 Address 20.0.0.2 Pri Dead-Time State 0 39 Ful1/DROther GE0/0 Interface
2.2.2.2 10.0.0.2 138 Ful1/DR GE0/1
```
## Lab Task3:

### step1
First, estabish the lab environment as illutrted in the lab diagram.Then,configure the IP address of Client A as 10.0.0.1/24 and specify the gateway address as 10.0.0.2.Configure the IP address of Client B as 10.1.0.1/24 and specify the gateway address as 10.1.0.2.

### step2
```cmd
[RTA]interface g0/0
[RTA-GigabitEthernet0/0]ip address 20.0.0.124
[RTA-GigabitEthernet0/0]interface go/1
[RTA-GigabitEthernet0/1]ip address 10.0.0.124
```
### step3
### step4
### step5
### step6


## command reference

- router id router-id
- ospf process-id
- area area-id
- network network ip-addr wildcard-mask
- ospf dr-priority proirity
- ospf cost value
