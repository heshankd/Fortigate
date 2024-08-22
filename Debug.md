### Sniffer

'diag sniffer packet any 'host 10.11.3.5 and icmp' 1'
'!'


### Flow Debugs
#### Disable Flow Debugs
  diag debug disable
  diag debug flow trace stop
  diag debug flow filter clear
  diag debug reset
  !
#### Enable Flow Debugs
  diag debug flow filter saddr 10.15.12.2
  diag debug flow filter daddr 10.19.19.99
  diagnose debug flow trace start 100
  diagnose debug flow show function-name enable
  diagnose debug enable
  !
