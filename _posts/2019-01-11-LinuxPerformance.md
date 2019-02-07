---
layout: post
title: 리눅스 성능 관리 툴 
categories:
  - Linux
tags :   
  - NetWork    
---
 - Statistical tools
    * sar -n TCP,ETCP,DEV 1
                 
```bash
16시 03분 14초     IFACE   rxpck/s   txpck/s    rxkB/s    txkB/s   rxcmp/s   txcmp/s  rxmcst/s
16시 03분 15초        lo      0.00      0.00      0.00      0.00      0.00      0.00      0.00
16시 03분 15초      eth0      0.99      1.98      0.06      1.27      0.00      0.00      0.00
16시 03분 15초      eth1      0.00      0.00      0.00      0.00      0.00      0.00      0.00

16시 03분 14초  active/s passive/s    iseg/s    oseg/s
16시 03분 15초      0.00      0.00      0.00      0.00
```          

 - uptime ---------------------------------> load averages
 
```bash
16:09:49 up 1 day, 21:54,  1 user,  load average: 0.07, 0.04, 0.01
``` 
 - vmstat 1 -------------------------------> overall stats by time
 
 ```bash
procs -----------memory---------- ---swap-- -----io---- -system-- ------cpu-----
 r  b   swpd   free   buff  cache   si   so    bi    bo   in   cs us sy id wa st
 1  0      0 21128616 113412 1006004    0    0     1     1   24   10  0  0 100  0  0
 0  0      0 21128716 113412 1006036    0    0     0     0  147  272  0  0 100  0  0
 0  0      0 21128700 113416 1006036    0    0     0    52  210  314  0  0 99  0  0
 0  0      0 21128700 113416 1006036    0    0     0     0  127  239  0  0 100  0  0  
```
 
 - mpstat -P ALL 1 ------------------------> CPU balance

```
6:08:47     CPU    %usr   %nice    %sys %iowait    %irq   %soft  %steal  %guest  %gnice   %idle
16:08:48     all    0.00    0.00    0.00    0.38    0.00    0.00    0.00    0.00    0.00   99.62
16:08:48       0    0.00    0.00    0.00    0.00    0.00    0.00    0.00    0.00    0.00  100.00
16:08:48       1    0.00    0.00    0.00    3.12    0.00    0.00    0.00    0.00    0.00   96.88
16:08:48       2    0.00    0.00    0.00    0.00    0.00    0.00    0.00    0.00    0.00  100.00
16:08:48       3    0.00    0.00    0.00    0.00    0.00    0.00    0.00    0.00    0.00  100.00
16:08:48       4    0.00    0.00    0.00    0.00    0.00    0.00    0.00    0.00    0.00  100.00
16:08:48       5    0.00    0.00    0.00    0.00    0.00    0.00    0.00    0.00    0.00  100.00
16:08:48       6    0.00    0.00    0.00    0.00    0.00    0.00    0.00    0.00    0.00  100.00
16:08:48       7    0.00    0.00    0.00    0.00    0.00    0.00    0.00    0.00    0.00  100.00

``` 
 - iostat -xz 1 -----------------------> disk/io
 - pidstat 1    -----------------------> process usage
 - sar -n DEV 1 -----------------------> network I/O
 - sar -n TCP,ETCP 1 ------------------> TCP stats 
 - Netflex Vector ( 호스팅 성능 모니터링 도구)   
   - http://getvector.io/docs/getting-started.html              
   - https://www.slideshare.net/brendangregg/how-netflix-tunes-ec2-instances-for-performance

