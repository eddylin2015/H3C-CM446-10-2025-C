# Lab05_交換機端口安全技術configPortSecurityFunctions 

## 5.1 內容目標

- 802.1X
- isolate port

## 實驗組網

## 設備

## 過程

### TASK1
```cmd
[SWA]dot1x
[SWA-G1/0/1]dot1x
[SWA]local-user abcde class network
[SWA-local-user abcde] service-type lan-access
[SWA-luser-h3c]password simple 123456

```
### TASK2
```cmd
[SWA]port-isolate group 1
[SWA-G1/0/1]port-isolate enable group 1
[SWA-G1/0/2]port-isolate enable group 1
<SWA>disp port-isolate group 1
```
- PCA 172.16.0.1/24
- PCB 172.16.0.2/24

## CMD LIST
- dot1x
- port-isolate group
- port-isolate enable group-num
- display port-isolate group
## 思考題

## 總結


