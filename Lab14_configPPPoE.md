# Lab14_configPPPoE 

## LabDiagram

![](https://github.com/eddylin2015/H3C-CM446-10-2025-C/blob/main/img/lab14LabDiagram.png?raw=true)

由2台 MSR3620(PPPoE Server、PPPoE Client) 路由器、2台 S5820V2(SWA、SWB) 交换机、3台 PC(PCA、PCB、Internet)组成,互连方式和IP地址分

本组网模拟了实际组网中 PPPoE 的应用，PCA、PCB 及 SWB 位于私网，PCA 及 PCB 可以理解为家庭终端，网关为 PPPoE Client。PPPoE Client 同时为 NAT 设备，PPPoE Client 和 SWB 可以一起理解为家用路由器，有 1 个私网接口（GO/1）和 1 个公网接口（GO/0，可以认为是家庭路由器上的 WAN 口），公网接口通过 SWA（模拟城域网络）与公网 PPPoE Server 相连。PPPoE Server 及 Internet 位于公网，本实验用一台 PC 终端模拟 Internet，其网关为 PPPoE Server 的 GO/1 端口。

## LabProcedure

## Lab Task 1实验任务一:PPPoE Server 配置

Configuring the PPPoE server

Before the experiment, restore the router configuration to defaults.

### 步骤一:创建虚拟模板接口 1,配置接口 IP地址,并采用 PAP 认证对端

Create the virtual-template interface 1, configure the IP address of the interface, and use PAP to authenticate the peer.
```cmd
[H3C] sysname Server
[Server] interface virtual-template 1
[Server-Virtual-Templatel] ip address 1.1.1.1 24
[Server-Virtual-Templatel] ppp authentication-mode pap domain dm1
```
### Step 2 在G0/0 接口上启用 PPPoE Server 协议,并与虚拟模板接口1绑定。

Enable the PPPOE server protocol on the GO/0 interface and bind it to virtual-template

```cmd
[Server] interface GigabitEthernet 0/0
[Server-GigabitEthernet0/0] pppoe-server bind virtual-template 1
```
### Step 3 Configure the DHCP address pool 1.
A PPPoE server can manage multiple PPPOE clients, to which it assigns IP addresses by
setting the address pool.
```cmd
[Server] dhcp enable 
[Server] dhcp server ip-pool pool1
[Server-dhcp-pool-pooll] network 1.1.1.0 24 export-route
# export-route 無效指令 [client]ip route-static 0.0.0.0 0.0.0.0 1.1.1.1
[Server-dhcp-pool-pooll] gateway-list 1.1.1.1 export-route
# export-route 無效指令
[Server-dhcp-pool-pool1] forbidden-ip 1.1.1.1
```
### Step 4 Configure a PPPoE user.
Configure the local username and password on the PPPoE server. The username and
password of the peer PPPOE client should be consistent with the local user.
```cmd
[Server] local-user userl class network
[Server-luser-network-userl] password simple pass1
[Server-luser-network-userl] service-type ppp 
```
Think about the PAP authentication process here. PAP authentication uses a two-way
handshake. First, the authenticated party sends the username and password to the
authenticating party in plain text. In this experiment, PPPOE client, as the authenticated
party, sends the username (user1) and password (pass1) to the PPPOE server (the
authenticating party) in plain text, and PPPoE server confirms the same. That's why PАР
authentication is considered less secure.
### Step 5 Under domain dm1 of the ISP, configure domain users to use the local authentication
solution and authorize the IP pool 1.
```cmd
[Server] domain dm1
[Server-isp-dml] authentication ppp local
[Server-isp-dml] authorization-attribute ip-pool pool1
```
### Step 6 Configure the IP address of the G0/1 port on the PPPoE server.
```cmd
[Server] interface GigabitEthernet 0/1
[Server-GigabitEthernet0/1] ip address 2.2.2.1 30
```
### Lab Task 2实验任务二:PPPoE Client 配置 Configuring the PPPoE client 


在配置 PPPoE 会话之前,需要先配置一个 Dialer 接口,并在接口上开启共享 DDR。每个
PPPoE 会话唯一对应一个 Dialer bundle, 而每个 Dialer bundle 又唯一对应一个 Dialer 接口。
这样就相当于通过一个 Dialer 接口可以创建一个PPPoE会话。

Before a PPPOE session is configured, configure a dialer interface and enable the shared
DDR on the interface. Each PPPoE session uniquely corresponds to a dialer bundle, and
each dialer bundle uniquely corresponds to a dialer interface. Therefore, a dialer interface
can create one PPPOE session.
Set dialer-group 1, use DDR to send the IP packet, create a dialer interface, and associate

### Step 1 Configure the dialer interface.

设置拨号访问组1,对IP协议报文进行 DDR 拨号,并创建 Dialer 接口,将拨号访问组1与接口 Dialer1 关联

dialer-group 1 with interface dialer 1.
```cmd
[H3C] sysname Client
[Client] dialer-group 1 rule ip permit
[Client]interface dialer 1
[Client-Dialerl] dialer-group 1 
```

Configure the interface to obtain the IP address through PPP negotiation, and dialer 1 will
dynamically obtain the IP address through the PPPoE server.

```cmd
[Client-Dialerl]ip address ppp-negotiate
```
Enable shared DDR on the dialer 1 interface. 
```cmd
[Client-Dialerl] dialer bundle enable
```
Configure the local username and password which will be sent by the PPPoE server to the 
PPPOE client during PAP authentication.
```cmd
[Client-Dialerl] ppp pap local-user userl password simple pass1 
```
Establish a PPPoE session and specify the dialer bundle corresponding to the session.

### Step 2 Configure a PPPOE session.
The serial number of this dialer bundle should be the same as the number of the dialer
建立一个PPPoE 会话,并且指定该会话所对应的 Dialer bundle。该 Dialer bundle 的序号
number 需要与 Dialer 接口的编号相同。
```cmd
[Client]interface GigabitEthernet 0/0
[Client-GigabitEthernet0/0]pppoe-client dial-bundle-number 1
```
配置 PPPoE Client 工作在永久在线模式,以便于测试配置效果
Configure the PPPOE client to the permanent online mode to verify the configuration.
```cmd
[Client]interface dialer 1
[Client-Dialerl] dialer timer idle 0
```

PPPoE Client 配置完成后,PPPoE Client 就可以与远端的 PPPoE Server 建立 PPPoE 会话。可通过如下命令进行验证。

After configuring the PPPOE client, the PPPoE client can establish a PPPOE session with
the remote PPPOE server. Perform authentication through the following command.
```cmd
[climt-Dialerl] display pppoe-client session summary
Bundle ID Interface VA RemoteMAC LocalMAC State
1 1 GE0/0 VAO 9649-900e-0705 9661-d679-0a05 SESSION
```
当PPPoE Client 通过 PPPoE 接入 Router 后,可在PPPoE Server 通过如下命令显示所
有DHCP地址绑定信息。
When the PPPoE client accesses the router via PPPoE, the client can display all DHCP
address binding information on the PPPoE server through the following command.
Bundle ID Interface1 GE0/0
```cmd
[Server] display dhcp server ip-in-use
IP address Client identifier/ Lease expiration Type
          Hardware address    Unlimited Auto (C)
1.1.2 0039-3636-312e-6436-
      3739-2e30-6130-352d-
      6666-6666-6666-6666
```
由此可知 PPPoE client 被分配的公网 IP地址为1.1.1.2.

Hence, it is known that the public network IP address assigned to the PPPoE client is 1.1.1.2.

### Step 3步骤三:NAT 配置 Configure the NAT.
For dial-up access, the public network IP address is dynamically assigned by the ISP. It
cannot be determined beforehand and cannot be translated by the standard network
address and port translation (NAPT). Easy IP applies to dial-up access to the Internet or
dynamic acquisition of an IP address.
Define a flow with the source address belonging to the 10.0.0.0/24 network segment via
ACL.

对于拨号接入这类常见的上网方式,其公网 IP地址是由运营商方面动态分配的,无法事先
确定,标准的NAPT 无法为其做地址转换。Easy IP 适用于拨号接入 Internet 或动态获得IP地
址的场合。

通过 acl 定义一条源地址属于10.0.0.0/24 网段的流。

```cmd
[Client]acl basic 2000
[Client-acl-ipv4-basic-2000] rule 0 permit source 192.168.0.0 0.0.0.255
```
In the dialer interface view, associate ACL 2000 with the interface and deploy NAT.
在Dialer 接口视图下将 acl 2000与接口关联下发 NAT。
```cmd
[Client]interface Dialer 1
[Client-Dialerl] nat outbound 2000
```
### Step 4 Configure a private network.
Configure the address pool of the private network.
```cmd
[Client]dhcp enable 
[Client]dhcp server forbidden-ip 192.168.0.254
[Client] dhcp server ip-pool pooll
[Client-dhcp-pool-pooll] network 192.168.0.0 mask 255.255.255.0
[Client-dhcp-pool-pooll]gateway-list 192.168.0.254
```
Configure the gateway address of the private network.
```cmd
[Client]interface GigabitEthernet 0/1
[client-GigabitEthernet0/1]ip address 192.168.0.254
```
### Step 5 Check the connectivity.

PCA and PCB can ping the Internet (2.2.2.2) respectively.

### Step 6 Check NAT entries.
After completing the previous step, check the NAT entries on the RTA.
```cmd
[RTA]display nat session verbose

Slot 0:
Initiator: 
Source IP/port: 192.168.0.2/189
Destination IP/port: 2.2.2.2/2048
DS-Lite tunnel peer:
VPN instance/VLAN ID/Inline ID: -/-/-
Protocol: ICMP(1)O
Inbound interface: GigabitEthernet0/1
Responder:
Source IP/port: 2.2.2.2/5
Destination IP/port: 1.1.1.2/0
DS-Lite tunnel peer:
VPN instance/VLAN ID/Inline ID: -/-/-
Protocol: ICMP (1)
Inbound interface: Dialer1
State: ICMP REPLY
Application: ICMP
Rule ID:-/-/-
Rule name:
Start time: 2022-06-06 15:57:53 TTL: 27s
Initiator->Responder: 0 packets 0 bytes
Responder->Initiator: 0 packets 0 bytes

Initiator:
Source IP/port: 192.168.0.1/239
Destination IP/port: 2.2.2.2/2048
DS-Lite tunnel peer: -
VPN instance/VLAN ID/Inline ID:-/-/-
Protocol: ICMP (1)
Inbound interface: GigabitEthernet0/1
Responder:
Source IP/port: 2.2.2.2/4
Destination IP/port: 1.1.1.2/0
DS-Lite tunnel peer: -
VPN instance/VLAN ID/Inline ID:
Protocol: ICMP (1)
Inbound interface: Dialerl
State: ICMP REPLY
Application: ICMP
Rule ID:-/-/-
Rule name:
Start time: 2022-06-06 15:57:46 TTL: 20s
Initiator->Responder: 0 packets 0 bytes
Responder->Initiator: 0 packets 0 bytes
Total sessions found: 2
```
## command reference
![](https://github.com/eddylin2015/H3C-CM446-10-2025-C/blob/main/img/lab14commandreference.png?raw=true)

## 結語
完成

