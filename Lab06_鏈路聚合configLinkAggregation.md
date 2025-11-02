# Lab06_配置鏈路聚合configLinkAggregation 

## 內容目標
- Link-Aggregatoin basic
- Link-Aggregatoin config

## Lab Diagram

![](https://github.com/eddylin2015/H3C-CM446-10-2025-C/blob/main/img/hcl_ab7a55429965.png?raw=true)

## Task1实验任务一:交换机静态链路聚合配置
本实验通过在交换机上配置静态链路聚合,使读者掌握静态链路聚合的配置命令和查看方
法。然后通过断开聚合组中的某条链路并观察网络连接是否中断,来加深了解链路聚合所实现
的可靠性。

如果建立物理连接后,交换机面板上的端口 LED 不停闪烁,且 Console 口对配置命令无
响应,则很可能是广播风暴导致。如有此情况,请断开交换机间的线缆,配置完成后再连接。

### 步骤二:配置静态聚合


配置 SWA:
```cmd
[SWA] interface bridge-aggregation 1
[SWA] interface GigabitEthernet 1/0/23
[SWA-GigabitEthernet1/0/23] port link-aggregation group 1
[SWA] interface GigabitEthernet 1/0/24
[SWA-GigabitEthernet1/0/24] port link-aggregation group 1
```
配置 SWB:
```cmd
[SWB] interface bridge-aggregation 1 
[SWB] interface GigabitEthernet 1/0/23
[SWB-GigabitEthernet1/0/23] port link-aggregation group 1
[SWB] interface GigabitEthernet 1/0/24
[SWB-GigabitEthernet1/0/24] port link-aggregation group 1
```
### 步骤三:查看聚合组信息


别在SWA 上查看所配置的聚合组信息。正确信息应如下所示: 
```cmd
 [SWA]display link-aggregation summary
```
### 步骤四:链路聚合组验证

### config file
```cmd
sysname PCA
interface GigabitEthernet0/0/1
 ip address 172.16.0.1 255.255.255.0
#
sysname PCB
interface GigabitEthernet0/0/1
 ip address 172.16.0.2 255.255.255.0
```

```cmd
 sysname SWA
interface Bridge-Aggregation1
interface GigabitEthernet1/0/23
 port link-mode bridge
 port link-aggregation group 1
#
interface GigabitEthernet1/0/24
 port link-mode bridge
 port link-aggregation group 1
#
```
```cmd
 sysname SWB
interface Bridge-Aggregation1
interface GigabitEthernet1/0/23
 port link-mode bridge
 port link-aggregation group 1
#
interface GigabitEthernet1/0/24
 port link-mode bridge
 port link-aggregation group 1
#

```


## cmd list
- interface bridge-aggregation interface-num
- port link-aggregation group number
- disp link-aggregation summary
## 思考題

## 總結
完成



