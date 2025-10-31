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


## Lab Task3:

## command reference

- router id router-id
- ospf process-id
- area area-id
- network network ip-addr wildcard-mask
- ospf dr-priority proirity
- ospf cost value
