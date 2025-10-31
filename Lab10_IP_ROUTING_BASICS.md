Lab10_IP_ROUTING_BASICS 

## labDiagram


![](https://github.com/eddylin2015/H3C-CM446-10-2025-C/blob/main/img/lab10_labDiagram.png?raw=true)

lab10_labDiagram.png

## Task1
RTA G0/1 192.168.1.1/24 --
RTA G0/0 192.168.0.1/24 --
RTB G0/1 192.168.1.2/24 --
RTB G0/0 192.168.2.1/24 --
PCA ---- 192.168.0.2/24 192.168.0.1
PCB ---- 192.168.2.2/24 192.168.2.1

### step1
```cmd
[RTA]disp ip routing-table
[RTA-G0/0]ip address 192.168.0.1 24
[RTA-G0/2]ip address 192.168.1.1 24
```
```cmd
[RTB]disp ip routing-table
[RTB-G0/0]ip address 192.168.2.1 24
[RTB-G0/2]ip address 192.168.1.2 24
```
```cmd
[RTA G0/0]shutdown
[RTA]disp ip routing-table
[RTA-G0/0]undo shutdown
```

## Task2

### step1
```cmd
[RTA]disp ip routing-table
[RTA]ip route-static 192.168.2.0 24 192.168.1.2
##
[RTB]ip route-static 192.168.0.0 24 192.168.1.1
```
```cmd
[RTA]ip route-static 0.0.0.0 0.0.0.0 192.168.1.2
##
[RTB]ip route-static 0.0.0.0 0.0.0.0 192.168.1.1
```

## cmd list
- ip route-static dest-addr {mask-len|mask} {interface-type interface-num [next-hop-addr]|next-hop-addr}
- disp ip routing-table ip-addr [mask|mask-len] 
- ipconfig
