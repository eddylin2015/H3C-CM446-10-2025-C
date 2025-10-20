# Lab 2 connecting and Debugging Network Devices

## Purpose

After completing the Lab, you will be able to:

- Master the method of connecting a router
- Master the method of testing system connectivity using Ping and Tracert commands.
- Master the method of using debug commands

## Lab Diagram

Figure 2-1 Lab diagram
![](https://github.com/eddylin2015/H3C-CM446-10-2025-C/blob/main/figure201labdiagram.png?raw=true)

## Lab Procedure

ITEM                |  VERSION   |   QUANTITY
--------------------|------------|-------------
MSR36-20             | CMW7.1           |   2
S5820V2              | CMW7.1           |2
PC                   |            |2
Cat5UTPEthernetCable |            |4

## Lab Procedure

- routers G0/0
- router to switch G0/1

```cmd
sysname RTA
interface G0/1
ip add 192.168.0.1 24
interface G0/0
ip add 192.168.1.1 30
```
```cmd
sysname RTB
interface G0/1
ip add 192.168.2.1 24
interface G0/0
ip add 192.168.1.2 30
```
```cmd
disp ip routing-table
[RTA]ip route-static 192.168.2.0 255.255.255.0 192.168.1.2
```
```cmd
[RTB]ip route-static 192.168.0.0 255.255.255.0 192.168.1.1
```
## tracert enable cmd
```cmd
[RTA]ip unreachables enable
[RTA]ip ttl-expires enable
```
## debuggin

```cmd
<RTB>terminal monitor
<RTB>terminal debugging
<RTB>debugging ip icmp
<RTB>undo debugging all
```

## Commands Used in the Lab

Command | Description
--------|--------------
ip add|configure an ip address
iproute-static|configure a static route
ping|check connectivity
tracert|check a route
terminal monitor|enable the system monitoring function
terminal debugging|Enable the debugging information display function
debugging|Enable the debugging switch of a specified module.
