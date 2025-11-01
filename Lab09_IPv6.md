# Lab09_IPv6 

- ipv6 addr
- ipv6 ping
- ipv6 neighbors

## Lab Diagram

RTA  G0/0------------G0/0  RTB

## Task1实验任务一:IPv6 地址配置及查看
本实验通过让学员在路由器上配置 IPv6 地址,然后用命令行观察 IPv6 邻居表项,再用命
令行来测试 IPv6 邻居的可达性,从而让学员建立对IPv6 地址的认知,建立起对邻居发现协议
功能的初步了解。本实验中所用到的所有命令均在表9-2 中,学员可根据实验步骤中的描述进
行选取。

### 步骤一:建立物理连接 
### 步骤二:配置接口自动生成链路本地地址及测试可达性,查看邻居信息
配置 RTA:
配置接口 G0/0 自动生成链路本地地址。 
```cmd
[RTA] interface GigabitEthernet0/0
[RTA-GigabitEthernet0/0] ipv6 address auto link-local
```
配置 RTB:
配置接口 G0/0 自动生成链路本地地址。
```cmd
[RTB] interface GigabitEthernet0/0
[RTB-GigabitEthernet0/0] ipv6 address auto link-local
```
以上配置完成后,路由器会自动生成前缀为FE80::的链路本地地址。用命令来查看生成的
链路本地地址,记录下来并测试可达性。如下所示:
```cmd
[RTA] display ipv6 interface GigabitEthernet0/0 brief
*down: administratively down
(s): spoofing
Interface Physical Protocol IPv6 Address
GigabitEthernet0/00 up up FE80::72BA: EFFF: FE80:419

[RTB]display ipv6 interface GigabitEthernet0/0 brief
*down: administratively down
(s): spoofing
Interface Physical Protocol IPv6 Address
GigabitEthernet0/0 up up FE80::72BA:EFFF:FE7F:F78C

[RTA] [RTA]ping ipv6 FE80::72BA:EFFF:FE7F:F78C -i GigabitEthernet0/0
Ping6 (56 data bytes) FE80::72BA:EFFF:FE80:419 --> FE80::72BA:EFFF:FE7F:F78C,
press CTRL C to break
56 bytes from FE80::72BA:EFFF:FE7F:F78C, icmp_seq=0 hlim=64 time=0.911 ms
56 bytes from FE80::72BA:EFFF:FE7F:F78C, icmp_seq=1 hlim=64 time=0.212 ms
56 bytes from FE80::72BA:EFFF:FE7F:F78C, icmp seq=2 hlim=64 time=0.238 ms
56 bytes from FE80::72BA:EFFF:FE7F:F78C, icmp_seq=3 hlim=64 time=0.189 ms
......
```
同时,通过命令来查看路由器的邻居信息。如下所示:
```cmd
[RTA] display ipv6 neighbors all Tammo
Type: S-Static D-Dynamic 0-Openflow I-Invalid
IPv6 address Link layer VID Interface State T Age
FE80::72BA:EFFF:FE7F:F78C 70ba-ef7f-f78c N/A GE0/0 STALE D 17
```
可以看到,RTA 的 IPv6 邻居就是RTB,是通过动态(Dynamic)方式来获得的。路由器
的IPv6 链路本地地址是符合 EUI-64 规范的地址。

### 步骤三:配置接口生成全球单播地址并测试可达性,查看邻居信息
配置 RTA:
在接口 G0/0 配置全球单播地址 3001::1。
```cmd
[RTA] interface GigabitEthernet0/0 mo
[RTA-GigabitEthernet0/0] ipv6 address 3001::1/64
```
配置 RTB:
在接口 G0/0 上配置全球单播地址 3001::2.
```cmd
[RTB] interface GigabitEthernet0/0
[RTB-GigabitEthernet0/0] ipv6 address 3001::2/64
```
以上配置完成后,路由器接口会生成全球单播地址。用命令来查看生成的全球单播地址并
测试可达性。如下所示:
```cmd
[RTA] display ipv6 interface GigabitEthernet 0/0 brief
*down: administratively down
(s): spoofing
Interface Physical Protocol IPv6 Address
GigabitEthernet0/0 up up 3001::1

[RTB] display ipv6 interface GigabitEthernet 0/0 brief
*down: administratively down
(s): spoofing
Interface Physical Protocol IPv6 Address
GigabitEthernet0/0 up upo 3001::2

[RTA] ping ipv6 3001::2
Ping6 (56 data bytes) 3001::1--> 3001::2, press CTRL_C to break
56 bytes from 3001::2, icmp_seq=0 hlim=64 time=0.853 ms
56 bytes from 3001::2, icmp seq=1 hlim=64 time=0.187 ms
```
同时,通过命令来查看路由器的邻居信息。如下所示
```cmd
[RTA] display ipv6 neighbors all
Type: S-Static D-Dynamic O-Openflow I-Invalid
IPv6 address Link layer VID Interface State T Age
3001::2   70ba-ef7f-f78c N/A GE0/0 STALE D 9
FE80::72BA:EFFF:FE7F:F78C  70ba-ef7f-f78c N/A GEO/0 REACH D 29 
```
可以看到,RTA 与RTB 间除了有链路本地地址的邻居外,
还有全球单播地址的邻居。


## cmd list
- ipv6 addr {ipv6-add prefix-len|ipv6-prefix/prefix-len}
- ipv6 addr auto link-local
- disp ipv6 interface [interface-type interface-num|brief]
- ping ipv6
- disp ipv6 neighbors {ipv6-addr|all|dynamic|interface - interface-type interface-num|static|vlan vlan-id} [verbos]



