# Lab 1 Getting Started With H3C NetWork Devices

## Purpose

After completing the Lab, you will be able to:

- log in to a device via a console port.
- log in to a device via Telnet
- basic commands to operate a system.
- basic commands to operate a file.

## Lab Diagram

Figure 1-1 Lab diagram
```console
         Console Cable          
  ┌───────┐ ─────────────────── ┌───────────────────────────────┐     
  │   COM │                     │  Console port                 │
  │ PC    │                     │                 Router/Switch │
  │   Nic │    Cable            │  GigabitEthernet              │   
  └───────┘ ─────────────────── └───────────────────────────────┘   
```
Equipment and Cable

Item                      |  Version  | Quantity  | Description   
--------------------------|-----------|-----------|-----------------
MSR36-20                  |
PC                        |
Console Serial Port Cable |
Cat5 UTP Ethernet Cable   |

## PC Config

![](https://90apt.com/usr/uploads/2023/05/3571188184.png)


```cmd
#
<H3C>
<H3C>system-view
[H3C]interface GigabitEthernet0/1
[H3C-GigabitEthernet0/1]port link-mode route
[H3C-GigabitEthernet0/1]combo enable copper
[H3C-GigabitEthernet0/1]ip address 10.1.1.1 255.255.255.0
[H3C-GigabitEthernet0/1]q
```
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
[lmsw]local-user test
New local user added.
[lmsw-luser-manage-test]password simple 123456789a
[lmsw-luser-manage-test]service-type telnet
[lmsw-luser-manage-test]authorization-attribute user-role level-0
[lmsw-luser-manage-test]q
[lmsw]super password role level-15 simple 123456789a
[lmsw]header login
Please input banner content, and quit with the character '%'.
lm sw %
[lmsw]line vty 0 63
[lmsw-line-vty0-63]authentication-mode scheme
[lmsw-line-vty0-63]q
[lmsw]telnet server enable
```
