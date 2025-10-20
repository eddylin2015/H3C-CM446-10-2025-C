# CM446-10-2025-c 4522

H3C认证路由交换网络工程师（H3CNE-RS+）

## 实验软件HCL


[VirtualBox6.1.50](https://www.virtualbox.org/wiki/Download_Old_Builds_6_1)

bcdedit /set hypervisorlaunchtype off

[HCL](https://www.h3c.com/cn/Home/Agreement//default.htm?t=HCL_Setup_V5.10.3&s=11068062)

## 培训教材EBook

[https://wsso.h3c.com](https://www.h3c.com/en/)

Mbc@123321

[https://c.h3c.com](https://c.h3c.com/en/)

[中文讲义](https://www.h3c.com/cn/BizPortal/TrainingPartner/TeachingMaterial/TeachingMaterialCertification.aspx)

H3C Certified Network Engineer for Routing &amp; Switching Plus(H3CNE-RS+)

- I Computer Network Fundamentals
- II Getting Started with H3C Network Devices	
- III LAN Switching	
- IV Advanced Knowledge About TCPIP	
- V Configuring IP Routing	
- VI Configuring Secure Branch Network	
- VII WAN Access and Interconnection	
- VIII The Evolution of Network Technologies	
- LabGuide	H3CNE-RS+ Routing and Switching Essentials V1.0 Lab Guide

- 第1篇_计算机网络基础	
- 第2篇_H3C网络设备操作入门	
- 第3篇_配置局域网交换	
- 第4篇_高级TCPIP知识	
- 第5篇_IP路由	
- 第6篇_构建安全的分支网络	
- 第7篇_广域网接入和互连	
- 第8篇_网络技术发展	
- 实验指导书	路由交换技术基础_实验指导书

## 时间表

- 10/6 9 13 16 20 23 27 30 
- 11/6 10 13 17 20 24 27 
- 12/1

18:45-21:45

16 lessons

## 参考

- PDAC 3.18 3.1.13

- Bandwith

Bandwidth is the maximum amount of data that can be transmitted over a network connection in a given amount of time, gigabits per second (Gbps).

How Bandwidth Works
Data Transmission Capacity: Think of bandwidth as the capacity of a pipe, not the speed of the water flowing through it. A wider pipe (higher bandwidth) can carry more water (data) at once.

What to Consider
Throughput: While bandwidth is the maximum theoretical capacity, throughput is the amount of data that actually makes it to your destination in a given time, considering factors like network speed and latency.

- MTU MSS

MTU（Maximum Transmission Unit）即最大传输单元，是指在网络中能够传输的最大数据帧大小，包括帧头和数据 payload，它是数据链路层的一个参数。
以太网 EthernetII 最大的数据帧是 1518Bytes，刨去以太网帧的帧头 14Bytes 和帧尾 CRC 校验部分 4Bytes，剩下承载上层协议的数据域最大为 1500Bytes，这个值就是以太网的 MTU。

MSS（Maximum Segment Size）即最大分段大小，是指 TCP 协议中每个分段所能承载的最大数据量，不包括 TCP 和 IP 的头部信息。以下是关于它们的详细介绍：
每一个报文段所能承载的最大数据长度。TCP 协议在实现时往往用 MTU 值减去 IP 数据包包头的大小 20Bytes 和 TCP 数据段的包头 20Bytes 来计算 MSS，所以一般情况下 MSS 为 1460Bytes。
协商过程：在 TCP 连接建立的三次握手阶段，客户端和服务器会各自在 SYN 报文中携带自己的 MSS 值，双方互相确认后，取较小的 MSS 值作为这次连接的最大 MSS 值，这样可以确保在传输过程中不会因为数据段过大而超过 MTU，从而避免 IP 分片。
计算公式：MSS 的大小是由 MTU 减去 TCP 和 IP 头部的大小得到的，即 MSS=MTU-(TCP header+IP header)。

- 十分鐘網絡流合理值

~ 1Gbps /8 *60 * 10 /2  =40G
~ 長方形積, 化作三角形積(因為常見考慮峰值形狀)的話, 10分鐘的積為40GB範圍作為合理值



  



