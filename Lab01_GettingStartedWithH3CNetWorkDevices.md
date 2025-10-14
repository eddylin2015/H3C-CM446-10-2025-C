# Lab 1 Getting Started With H3C NetWork Devices

## Purpose

After completing the Lab, you will be able to:

- log in to a device via a console port.
- log in to a device via Telnet
- basic commands to operate a system.
- basic commands to operate a file.

## 真机實驗Lab_1

### 排錯

同學要留意指令版本不同:
```cmd
line vty 0 63
# 改為
user interface vty 0 63
```
### 2010年版交換機H3C S3600V2

![](https://resource.h3c.com/cn/201210/15/20121015_1422015_S3600V2-28TP-EI_755797_30003_0.jpg)

https://www.h3c.com/cn/d_201111/729703_30005_0.htm#aa_3

## HCL 虛擬機實驗 

### Lab_1 Diagram

Figure 1-1 Lab diagram

```console
         
  ┌───────┐    Console Cable    ┌───────────────────────────────┐     
  │   COM │ ─────────────────── │  Console port                 │
  │ PC    │                     │        MSR36-20 Router/Switch │
  │   Nic │ ─────────────────── │  GigabitEthernet              │   
  └───────┘    Cable            └───────────────────────────────┘

```

Equipment and Cable

Item                      |  Version  | Quantity  | Description   
--------------------------|-----------|-----------|-----------------
MSR36-20                  |
PC                        |
Console Serial Port Cable |
Cat5 UTP Ethernet Cable   |



### 實驗保充password-control
解決 telnet 連結時 super 問題.
```cmd
[lmsw]password-control super aging 365
[lmsw]password-control super length 4
[lmsw]password-control length 4
```

### MSR36-20 Console-CLI

```cmd
<H3C>
<H3C>system-view
System View: return to User View with Ctrl+Z.
[H3C]sysname lmsw
[lmsw]save
The current configuration will be written to the device. Are you sure? [Y/N]:
Before pressing ENTER you must choose 'YES' or 'NO'[Y/N]:
Before pressing ENTER you must choose 'YES' or 'NO'[Y/N]:Y
Please input the file name(*.cfg)[flash:/startup.cfg]
(To leave the existing filename unchanged, press the enter key):
flash:/startup.cfg exists, overwrite? [Y/N]:Y
Validating file. Please wait...
Configuration is saved to device successfully.
[lmsw]password-control super aging 365
[lmsw]password-control super length 4
[lmsw]password-control length 4
[lmsw]local-user test
New local user added.
[lmsw-luser-manage-test]password simple abc1
[lmsw-luser-manage-test]service-type telnet
[lmsw-luser-manage-test]authorization-attribute user-role level-0
[lmsw-luser-manage-test]q
[lmsw]super password role level-15 simple abc1
[lmsw]header login
Please input banner content, and quit with the character '%'.
Welcome LM SW %
[lmsw]line vty 0 63
[lmsw-line-vty0-63]authentication-mode scheme
[lmsw-line-vty0-63]q
[lmsw]telnet server enable
[lmsw]interface GigabitEthernet0/1
[lmsw-GigabitEthernet0/1]port link-mode route
[lmsw-GigabitEthernet0/1]combo enable copper
[lmsw-GigabitEthernet0/1]ip address 10.1.1.1 255.255.255.0
[lmsw-GigabitEthernet0/1]q
[lmsw]q
<lmsw>reboot

display interface brief
GigabitEthernet0/1 up up
```

## PC Telnet實驗

### PC需要設置靜態IP及UP STATUS

![](https://90apt.com/usr/uploads/2023/05/3571188184.png)

## Telnet指令
```cmd
[H3C]telnet 10.1.1.1
Welcome LM SW %
login:test
password:abc1
<lmsw>super
password:abc1
<lmsw>sys
[lmsw]display interface brief
GigabitEthernet0/1 up up 
```
## 總結

完成LAB1.
