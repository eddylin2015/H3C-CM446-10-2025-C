# Lab14_configPPPoE 

## LabDiagram

![](https://github.com/eddylin2015/H3C-CM446-10-2025-C/blob/main/img/lab14LabDiagram.png?raw=true)

## LabProcedure

本组网模拟了实际组网中 PPPoE 的应用，PCA、PCB 及 SWB 位于私网，PCA 及 PCB 可以理解为家庭终端，网关为 PPPoE Client。PPPoE Client 同时为 NAT 设备，PPPoE Client 和 SWB 可以一起理解为家用路由器，有 1 个私网接口（GO/1）和 1 个公网接口（GO/0，可以认为是家庭路由器上的 WAN 口），公网接口通过 SWA（模拟城域网络）与公网 PPPoE Server 相连。PPPoE Server 及 Internet 位于公网，本实验用一台 PC 终端模拟 Internet，其网关为 PPPoE Server 的 GO/1 端口。

## Lab Task 1: Configuring the PPPoE server

Before the experiment, restore the router configuration to defaults.
### Step 1 Create the virtual-template interface 1, configure the IP address of the interface, and use
PAP to authenticate the peer.
```cmd
[H3C] sysname Server
[Server] interface virtual-template 1
[Server-Virtual-Templatel] ip address 1.1.1.1 24
[Server-Virtual-Templatel] ppp authentication-mode pap domain dm1

### Step 2 Enable the PPPOE server protocol on the GO/0 interface and bind it to virtual-template

Iterface1 rettaes diosiTETATnst 0/o
```cmd
[Server-GigabitEthernet0/0] pppoe-server bind virtual-template 1
```
### Step 3 Configure the DHCP address pool 1.
A PPPoE server can manage multiple PPPOE clients, to which it assigns IP addresses by
setting the address pool.
```cmd
[Server] dhcp enable mo
[Server] dhcp server ip-pool pool1
[Server-dhcp-pool-pooll] network 1.1.1.0 24 export-route
[Server-dhcp-pool-pooll] gateway-list 1.1.1.1 export-route
[Server-dhcp-pool-pool1] forbidden-ip 1.1.1.1
```
### Step 4 Configure a PPPoE user. mbc.edu. mo
Configure the local username and password on the PPPoE server. The username and
password of the peer PPPOE client should be consistent with the local user.
```cmd
[Server] local-user userl class network
[Server-luser-network-userl] password simple pass1
[Server-luser-network-userl] service-type ppp ammot
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
### Lab Task 2: Configuring the PPPoE client 
Before a PPPOE session is configured, configure a dialer interface and enable the shared
DDR on the interface. Each PPPoE session uniquely corresponds to a dialer bundle, and
each dialer bundle uniquely corresponds to a dialer interface. Therefore, a dialer interface
can create one PPPOE session.
Set dialer-group 1, use DDR to send the IP packet, create a dialer interface, and associate
### Step 1 Configure the dialer interface.
dialer-group 1 with interface dialer 1.
```cmd
[H3C] sysname Client
[Client] dialer-group 1 rule ip permi
[Client]interface dialer 1
[Client-Dialerl] dialer-group 1 
```
Configure the interface to obtain the IP address through PPP negotiation, and dialer 1 will
dynamically obtain the IP address through the PPPoE server.
```cmd
[Client-Dialerl]ip address ppp-negotiate
```
Enable shared DDR on the dialer 1 interface. 
Configure the local username and password which will be sent by the PPPoE server to the
```cmd
[Client-Dialerl] dialer bundle enable
```
PPPOE client during PAP authentication.
```cmd
[Client-Dialerl] ppp pap local-user userl password simple pass1 mail
```
Establish a PPPoE session and specify the dialer bundle corresponding to the session.

### Step 2 Configure a PPPOE session.
The serial number of this dialer bundle should be the same as the number of the dialer
```
[Client]interface GigabitEthernet 0/0
[Client-GigabitEthernet0/0]pppoe-client dial-bundle-number
```
interface.
1
Configure the PPPOE client to the permanent online mode to verify the configuration.
```cmd
[Client]interface dialer 1
[Client-Dialerl] dialer timer idle 0
```
After configuring the PPPOE client, the PPPoE client can establish a PPPOE session with
the remote PPPOE server. Perform authentication through the following command.
summary
RemoteMAC LocalMAC State
9649-900e-0705 9661-d679-0a05 SESSION
```cmd
[Client-Dialerl] display pppoe-client session
```
When the PPPoE client accesses the router via PPPoE, the client can display all DHCP
address binding information on the PPPoE server through the following command.
Bundle ID Interface1 GE0/0
```cmd
[Server] display dhcp server ip-in-use
```
IP address Client identifier/ Lease expiration Type
Hardware address
1.1.1.2 0039-3636-312e-6436- Unlimited Auto (C)
3739-2e30-6130- 352d6666-6666-6666-6666

Hence, it is known that the public network IP address assigned to the PPPoE client is 1.1.1.2.
### Step 3 Configure the NAT.
For dial-up access, the public network IP address is dynamically assigned by the ISP. It
cannot be determined beforehand and cannot be translated by the standard network
address and port translation (NAPT). Easy IP applies to dial-up access to the Internet or
dynamic acquisition of an IP address.
Define a flow with the source address belonging to the 10.0.0.0/24 network segment via
ACL.

```cmd
[Client-acl-ipv4-basic-2000] rule 0 permit source 192.168.0.0 0.0.0.255
[Client]acl basic 2000
In the dialer interface view, associate ACL 2000 with the interface and deploy NAT.
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
Configure the gateway address of the private network.
[Client]interface GigabitEthernet 0/1
[client-GigabitEthernet0/1]ip address
```
### Step 5 Check the connectivity.
192.168.0.254
PCA and PCB can ping the Internet (2.2.2.2) respectively.
After completing the previous step, check the NAT entries on the RTA.
### Step 6 Check NAT entries.
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
0 packets
0 packets
0 bytes
0 bytes
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
Start time: 2022-06-06 15:57:46
Initiator->Responder:
bytes
0 bytes
TTL: 20s
0 packets 0
0 packets
Total sessions found: 2
```
## command reference
![](https://github.com/eddylin2015/H3C-CM446-10-2025-C/blob/main/img/lab14commandreference.png?raw=true)

