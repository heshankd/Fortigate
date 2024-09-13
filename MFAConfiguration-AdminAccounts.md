## Configure MFA for admins

### Configure MFA for a admin using email
1. Filter for required session
```
config system admin
    edit "adminTom"
        set two-factor email
        set email-to "itnetworkops@xyz.com"
    next
end
```
### Install Forti Token
Go to "User & Authentication" > FortiTokens >> Create New > Mobile Token >> Add the Token Key

### Troubleshoot - MFA mail is not sending by the firewall
```
diag debug enable
diag debug console timestamp enable
diag debug application alertmail -1
```
Fix - Adding a static route to "notification.fortinet.net"
```
config router static
	edit 16
        set dst 208.91.114.151 255.255.255.255
        set gateway <GW_IP>
        set device "wan1"
        set comment "FortinetMailServer"
    next
end
```
### Troubleshoot - Forti Token Installing Error
Add a static route to fortigate resitration servers
```
config router static
    edit 100
        set dst 63.137.229.3 255.255.255.255
        set distance 1
        set comment "forti registration"
        set sdwan-zone "virtual-wan-link"
    next
end

or  

config router static
	edit 44
		set dst 63.137.229.3 255.255.255.255
		set gateway <GW_IP>
		set device "port12"
    next
end
```
### Troubleshoot - FortiToken Provision Error (-7500)
Enable or Disable Anycast
```
config system fortiguard
     set fortiguard-anycast enable
     set fortiguard-anycast-source fortinet
end 

or 

config system fortiguard
     set fortiguard-anycast disable
end 
```
![image](https://github.com/user-attachments/assets/321ba8a4-7924-4e1c-8945-0686f32732ef)
![image](https://github.com/user-attachments/assets/85868772-4808-4b5f-8d73-fd3116990fbc)

Sources
https://community.fortinet.com/t5/Support-Forum/FortiGate-does-not-send-Two-Factor-activation-code/m-p/82385
---

