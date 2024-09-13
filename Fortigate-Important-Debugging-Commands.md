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

*Ex: dia ip proute match 10.15.1.1 10.19.19.20 TXS-VMWD-NET 6 161*
```
dia ip proute match <destination IP> <Source IP> <incoming interface> <proto - 6(tcp) 17(udp)> <destination port number>
```

SD-WAN debugging
```
dia sys sdwan <options> ...
```
---


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

---


### IPSec VPN Troubleshooting

![image](https://github.com/user-attachments/assets/4bd693d9-c27c-479f-971a-793ebacaedc6)

```
Step 1: What type of tunnel have issues?
FortiOS supports:
Site-to-Site VPN.
Dial-Up VPN .

Step 2: Is Phase-2 Status 'UP'?
No (SA=0)  - Continue to Step 3.
Yes (SA=1) - If traffic is not passing, - Jump to Step 6.
Flapping - SA is flapping between 'UP' and 'Down' state - Jump to Step 7.
 
How to identify if Phase 2 is 'UP' or 'Down':

Phase-2 status can be found from both GUI and Command Line.
From GUI:
When Phase2 is Down:
When Phase2 is UP:

From CLI:
Execute the command 'diagnose vpn tunnel list name <phase1-name>' <----- To view the phase1 status for a specific tunnel.     
['diagnose vpn tunnel list'  , can also be executed to view the phase2 status of all tunnels ].
 
When Phase2 is Down:
When Phase2 is UP:
 

Step 3: Is IKE Phase1 up:
No (State – 'Connecting') - Continue to Step 4.
Yes (State – 'Established') - Jump to Step 5.
Execute the command 'diagnose vpn ike gateway list name <phase1-name>' <----- To view the phase1 status for a specific tunnel.      
['diagnose vpn tunnel list' , can also be executed to view the phase2 status of all tunnels ].
 
When Phase1 is Down:
When Phase1 is UP:
 

Step 4:  Analyze the IKE phase 1 messages on the responder for a solution. [Phase 1 not up].
Troubleshooting IKE Phase 1 problems is best handled by reviewing VPN status messages on the responder firewall.
The responder is the 'receiver' side of the VPN that is receiving the tunnel setup requests.
The initiator is the side of the VPN that sends the initial tunnel setup requests.

Checklist:
Is there any other device upstream to the firewall.
Is the VPN Gateway configured to use the correct outgoing interface.
Is the remote IP configured correctly?
Run packet capture on the outgoing interface and confirm it is possible to see traffic from the remote peer. If not,  
Make sure if IKE traffic on port 500/4500 is allowed in the network device connected upstream
Packet capture can be run from CLI or GUI :
GUI:
CLI:
diagnose sniffer packet any 'host <remote-peer-ip> and port (500 or 4500)' 6 0 l

If it is possible to see traffic on port 500/4500, then follow the steps below to troubleshoot this issue:
Run below commands(on receiver) to capture the IKE logs and initiate tunnel/traffic from the remote end.
 
diagnose debug console timestamp enable  
diagnose debug application ike -1
diagnose debug enable 
 
Note:
Try to run the packet capture and the logs at the same time.  
If VDOMs is enabled, make sure to be in the VDOM context and then execute the above commands.

Step 5: Phase1 has established but Phase2 is down .

Checklist:
Confirm if the Encryption and Hashing algorithms match on both receiver and initiator.
Check if PFS is enabled, if yes, make sure the configuration is matched on both the units.
Make sure, if the quick mode selectors (interesting traffic) is matching on both units.
If Phase-2 is still not up, run the packet capture on port 500/4500 and run the below commands.
 
diagnose vpn ike gateway list (or diagnose vpn ike gateway list name <tunnel-name>)
diagnose debug console timestamp enable
diagnose debug application ike -1
diagnose debug enable 
 
Note:
If VDOMs is enabled, make sure to be in the VDOM context and then execute the above commands.


Step 6: Phase2 is up but traffic is not passing.
Once the tunnel is up, traffic will be encapsulated in ESP (Encapsulating Security Payload) protocol and sent to the remote peer.

Checklist:
1. Make sure the quick mode selector defined in Phase2 is configured properly to allow the traffic flow, which is having the issue.
    For example:
    Phase 2 define below allows traffic between – 192.168.1.0/24 and 192.168.2.0/24.
    Let's assume that the IP address of the PC having an issue is 10.10.100.100/24.
    If this PC is trying to reach any host in 192.168.2.0/24 network, FortiGate will drop this traffic because the phase2 quick mode selector does not have this source network included in it.
2. Check the IPv4 policies and confirm:
    If there is policy defined for this traffic flow.
    If there are any source and destination addresses defined, make sure it is configured to allow this traffic flow.
3. If the issue still persists:
Enable packet capture for remote peer’s ip address and set protocol to 50(ESP).
Open two SSH session and run the below commands:
SSH session 1:
diagnose  debug  console timestamp  enable  
diagnose  debug  flow filter addr <destination-IP>
diagnose  debug  flow filter proto <1 or 17 or 6>  (optional)  where 1=ICMP, 6 = TCP, 17 = UDP…
diagnose  debug  flow  show iprope enable
diagnose  debug  flow  trace start 1000

Note other protocol numbers can used as well for example OSPF(89).
SSH Session 2:
diagnose  vpn tunnel list  (or # diagnose vpn tunnel list name <phase2_tunnel_name>  )

Note:
If VDOMs is enabled, make sure it is not in the VDOM context and then execute the above command.
Make sure to collect packet capture and the logs mentioned above around the same and attach it to the Fortinet case updates.
Along with this information, attach network topology (if any). 
With this information, TAC will try to decrypt the ESP traffic in Wireshark.
If the remote peer is FortiGate as well, take packet capture on this unit as well which will make sure that this unit received the encrypted traffic or if it was lost in the middle.


Step 7:  Troubleshoot IPsec VPN that is flapping.
Checklist:
Does the issue affect one VPN or all configured VPNs.
If all VPN tunnels are affected:
Check Internet connection.
Run the below command to find out errors/logs associated with firewall/interface.
 
diagnose debug crashlog read
diagnose sys top 2 50, control + c to stop (run for 5 iterations)
get system performance status
diagnose hardware sysinfo conserve
diagnose hardware deviceinfo nic <interface-name>  
execute tac report

 

Note:
If VDOMs is enabled,  make sure it is not in the Global context and then execute the above commands.
 
If only one tunnel is flapping :
Collect the 'VPN Events' log as shown below:
Was the VPN stable for a period of time and now it is going up and down?
Yes - Investigate for network or unit changes or if any new network equipment has been added to the environment. If so, confirm changes/additions are correct.
No - Collect logs and packet capture as mentioned on Step 4
 

Make sure to collect packet capture and all the logs mentioned above around the same and attach it to the Fortinet case updates.

Along with this information, attach network topology (if any). With this information, TAC will investigate this issue.

Step 8: Logs to be collected and attached to TAC case.

Checklist:

SSH Session 1:
 
diagnose debug console timestamp enable  
diagnose debug application ike -1
diagnose debug enable
 
SSH Session 2:
 
diagnose debug crashlog read
diagnose sys top 2 50, control + c to stop (run for 5 iterations)
get system performance status
diagnose hardware sysinfo conserve
diagnose hardware deviceinfo nic <interface-name>  
diagnose vpn ike gateway list
diagnose vpn tunnel list
execute tac report
 
First Packet capture  - IKE Traffic on ports 500/4500.
Second Packet capture - ESP Traffic Protocol 50.
``` 
