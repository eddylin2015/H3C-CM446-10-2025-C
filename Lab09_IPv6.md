# Lab09_IPv6 

- ipv6 addr
- ipv6 ping
- ipv6 neighbors

## Lab Diagram

RTA  G0/0------------G0/0  RTB

## Task1
### step1
```cmd
[RTA]interface G0/0
[RTA interface G0/0]ipv6 addr auto link-local
[RTA]disp ipv6 interface G0/0 brief
[RTA]disp ipv6 neighbors all
#
[RTA interface G0/0]ipv6 addr 3001::1/64
```
```cmd
[RTB]interface G0/0
[RTB interface G0/0]ipv6 addr auto link-local
[RTB]disp ipv6 interface G0/0 brief
#
[RTB interface G0/0]ipv6 addr 3001::2/64
#
[RTB]disp ipv6 interface G 0/0 brief
[RTB]ping ipv6 3001::1
```
### step2

### step3

### step4

## cmd list
- ipv6 addr {ipv6-add prefix-len|ipv6-prefix/prefix-len}
- ipv6 addr auto link-local
- disp ipv6 interface [interface-type interface-num|brief]
- ping ipv6
- disp ipv6 neighbors {ipv6-addr|all|dynamic|interface - interface-type interface-num|static|vlan vlan-id} [verbos]


