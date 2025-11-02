# Lab 1 網絡設備基本操作Getting Started With H3C NetWork Devices

## Purpose

After completing the Lab, you will be able to:

- log in to a device via a console port.
- log in to a device via Telnet
- basic commands to operate a system.
- basic commands to operate a file.

## 真机實驗Lab_1

### 實驗指令

putty

- text
- control-h  control-?
- serial com1-3

指令參考HCL虛擬機實驗, 留意指令版本不同:
```cmd
line vty 0 63
# 改為
user interface vty 0 63
```


交換機H3C S3600V2
![](https://github.com/eddylin2015/H3C-CM446-10-2025-C/blob/main/img/figure100.png.jpg?raw=true)


參考指令

https://www.h3c.com/cn/d_201111/729703_30005_0.htm#aa_3

## HCL 虛擬機實驗 

### Lab_1 Diagram

Figure 1-1 Lab diagram

![](https://github.com/eddylin2015/H3C-CM446-10-2025-C/blob/main/img/lab01labDiagram.png?raw=true)

Item                      |  Version  | Quantity  | Description   
--------------------------|-----------|-----------|-----------------
MSR36-20                  |
PC                        |
Console Serial Port Cable |
Cat5 UTP Ethernet Cable   |



### 補充指令password-control
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
<lmsw>display interface brief

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
