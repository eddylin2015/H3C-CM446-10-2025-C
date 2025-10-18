# CM446-10-2025-c 4522

## h3c

bcdedit /set hypervisorlaunchtype off

[https://wsso.h3c.com](https://www.h3c.com/en/)

Mbc@123321

## EBook

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

## timetable

- 10/6 9 13 16 20 23 27 30 
- 11/6 10 13 17 20 24 27 
- 12/1

18:45-21:45

16 lessons

## PDAC 3.18 3.1.13

## Bandwith

Bandwidth is the maximum amount of data that can be transmitted over a network connection in a given amount of time, gigabits per second (Gbps).

How Bandwidth Works
Data Transmission Capacity: Think of bandwidth as the capacity of a pipe, not the speed of the water flowing through it. A wider pipe (higher bandwidth) can carry more water (data) at once.

What to Consider
Throughput: While bandwidth is the maximum theoretical capacity, throughput is the amount of data that actually makes it to your destination in a given time, considering factors like network speed and latency.

## MTU MSS

MTU（Maximum Transmission Unit）即最大传输单元，是指在网络中能够传输的最大数据帧大小，包括帧头和数据 payload，它是数据链路层的一个参数。MSS（Maximum Segment Size）即最大分段大小，是指 TCP 协议中每个分段所能承载的最大数据量，不包括 TCP 和 IP 的头部信息。以下是关于它们的详细介绍：
MTU：
定义与作用：MTU 决定了数据链路层上可以传输的数据包的大小，不同的网络媒介有不同的 MTU 值。例如，以太网 EthernetII 最大的数据帧是 1518Bytes，刨去以太网帧的帧头 14Bytes 和帧尾 CRC 校验部分 4Bytes，剩下承载上层协议的数据域最大为 1500Bytes，这个值就是以太网的 MTU。网络层协议如 IP 协议会根据 MTU 的值来决定是否对上层传下来的数据进行分片处理。
与网络传输的关系：当两台远程 PC 互联时，数据需要穿过多个路由器和不同的网络媒介，网络中不同媒介的 MTU 各不相同，就像由不同粗细的水管组成的管道，通过这段管道的最大水量由中间最细的水管决定，网络传输中的数据量也由路径中最小的 MTU 决定。
MSS：
定义与作用：MSS 是 TCP 协议里的一个概念，用于在 TCP 连接建立时，收发双方协商通信时每一个报文段所能承载的最大数据长度。为了达到最佳的传输效能，TCP 协议在实现时往往用 MTU 值减去 IP 数据包包头的大小 20Bytes 和 TCP 数据段的包头 20Bytes 来计算 MSS，所以一般情况下 MSS 为 1460Bytes。
协商过程：在 TCP 连接建立的三次握手阶段，客户端和服务器会各自在 SYN 报文中携带自己的 MSS 值，双方互相确认后，取较小的 MSS 值作为这次连接的最大 MSS 值，这样可以确保在传输过程中不会因为数据段过大而超过 MTU，从而避免 IP 分片。
两者的关系：
计算公式：MSS 的大小是由 MTU 减去 TCP 和 IP 头部的大小得到的，即 MSS=MTU-(TCP header+IP header)。
相互影响：MTU 限制了 MSS 的最大值，MTU 越大，在不考虑其他因素的情况下，MSS 也可以相应地设置得更大，从而可以一次传输更多的数据，提高传输效率。

## 十分鐘對網絡流分析積為50GB範圍作為合理值
~ 3Gbps /8 *60 * 10 250GB 1G+1G+1G 光纖
~ DATA 240GB (-20GB HEADER partiat)
~ 長方形積, 化作三角形積(因為常見考慮峰值形狀)的話, 10分鐘的積為120GB範圍作為合理值



  



