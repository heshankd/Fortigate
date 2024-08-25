### Check CPU
```
get system performance status
```
---


### **Sniffer**
```
diag sniffer packet any 'host 76.10.177.26 and tcp port 443' <verbose_level> <sniffer_count> <timestamp_format> <frame_size> 
```
``` 
<verbose_level>    
1: print header of packets
2: print header and data from ip of packets
3: print header and data from ethernet of packets (if available)
4: print header of packets with interface name
5: print header and data from ip of packets with interface name
6: print header and data from ethernet of packets (if available) with intf name

<sniffer_count>
no of packets you need to capture

<time_stamp>
a: absolute UTC time, yyyy-mm-dd hh:mm:ss.ms
l: absolute LOCAL time, yyyy-mm-dd hh:mm:ss.ms
otherwise: relative to the start of sniffing, ss.ms
```
---


### **Flow Debugs**
#### Disable Flow Debugs
```
diag debug disable
diag debug flow trace stop
diag debug flow filter clear
diag debug reset
```
#### Enable Flow Debugs
```
diag debug flow filter saddr 10.15.12.2
diag debug flow filter daddr 10.19.19.99
diagnose debug flow trace start 100
diagnose debug flow show function-name enable
diagnose debug enable
```
---


### Routing Debugging
Check policy routes/ sdwan rules for given traffic
Ex: dia ip proute match 10.15.1.1 10.19.19.20 TXS-VMWD-NET 6 161
```
dia ip proute match <destination IP> <Source IP> <incoming interface> <proto - 6(tcp) 17(udp)> <destination port number>
```

SD-WAN debugging
```
dia sys sdwan <options> ...
```



### Fortigate LLDP command
```
diag sniff pack any 'ether proto 0x88cc' 4
```
---


### Clear Sessions
1. Filter for required session
```
diag sys session filter src <source_ip>
```
2. List all sessions from filtered IP
```
diag sys session list
```
3. Clear sessions for the filtered IP
```
diag sys session clear
```

**WARNING** - *if you do not filter first and run the clear command you will clear ALL active sessions!!*

