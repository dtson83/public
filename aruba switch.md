# Set Factory:
### Clear password: 
on the management module/front panel of the switch > there will be a clear button  using a paper clip press it for 7-10 seconds
### Restoring the factory default configuration
You can also use the Reset button together with the Clear button (Reset+Clear) to restore the factory defaultconfiguration for the switch.
1. Press and hold the Reset button
2. While holding the Reset button, press and hold the Clear button
3. Release the Reset button and wait for about one second for the Self-Test LED to start flashing
4. When the Self-Test LED begins flashing, release the Clear button
### Clear Config
```
switch# copy running-config tftp://192.168.1.10/backup_cfg json vrf mgmt
switch# erase all zeroize
switch# copy tftp://192.168.1.10/backup_cfg running-config json vrf mgmt
switch# copy running-config startup-config
```
# Allow Unsupported SFP
```
allow-unsupported-transceiver [confirm | log-interval {none | <INTERVAL>}]
```
# stack
- Start by configuring VSF link 1 on the primary switch:
```
switch(config)# vsf member 1
switch(vsf-member-1)# link 1 1/1/49
```
- If the stack will contain more than two members, configure VSF link 2 as well:
```
switch(vsf-member-1)# link 2 1/1/50
```
- Next, specify the desired secondary member ID:
```
switch(config)# vsf secondary-member 2
This will save the configuration and reboot the specified switch.
Do you want to continue (y/n)? y
```
- Connect to the next stack member and configure VSF links (one if a 2-member stack, two if more than 2 members). Remember that all stack members start with a member ID of 1, so keep that in mind when assigning ports to the VSF links.
```
switch(config)# vsf member 1
switch(vsf-member-1)# link 1 1/1/49
switch(vsf-member-1)# link 2 1/1/50
```
- Once VSF links have been configured on the new member, renumber it to the desired member ID:
```
switch(config)# vsf renumber-to 2
This will save the VSF configuration and reboot the switch.
Do you want to continue (y/n)? y
```
### Authentication/SSH
  - default login: admin/<no password>
  - Change password
```
AOS-switch# configure
AOS-switch(config)# password manager user-name ladmin
```
```
password manager user-name George SHA12fd4e1c67a2d28fced849ee1bb76e7391b93eb12
```
  - console timeout
```
 console inactivity-timer <0|1|5|10|15|20|30|60|120>
```
```
enable ssh
6100(config)# ssh server vrf default
#enable web gui
6100(config)# https-server vrf default
```
# NTP Config
```
switch(config)# ntp authentication-key 1 md5 myPassword                                                          
switch(config)# ntp server my-ntp.mydomain.com key 10 prefer                                                            
switch(config)# ntp vrf mgmt
```
# LACP
- C???u h??nh interface lag 2 :
```
Switch2(config)# interface lag 2
Switch2(config-lag-if)# no routing
Switch2(config-lag-if)# no shutdown
Switch2(config-lag-if)# lacp mode active
Switch2(config-lag-if)# vlan trunk native 1
Switch2(config-lag-if)# vlan trunk allowed 1,2
Switch2(config-lag-if)# exit
```
- ????a c???ng 1/1/6 v?? 1/1/7 v??o interface lag 2:
```
Switch2(config)# interface 1/1/6,1/1/7
Switch2(config-if-<1/1/6,1/1/7>)# lag 2
Switch2(config-if-<1/1/6,1/1/7>)# no shutdown
Switch2(config-if-<1/1/6,1/1/7>)# exit
```
# VLAN
```
6100(config)# vlan 5,7-9
  exit
6100(config)# interface 1/1/5,1/1/7-1/1/9
6100(config-if-<1/1/5,1/1/7-1/1/9>)# vlan access 5
6100(config)# interface 1/1/10
6100(config-if)# vlan trunk allowed 5,7-9
6100(config-if)# vlan trunk native 1
```
### Ch?? ?? v??? Native VLAN:
- Tr?????ng h???p 1: c???u h??nh VLAN native l?? VLAN 1, v?? ch??? c?? VLAN 5,7,8,9 ???????c cho ph??p tr??n ???????ng trunk. N??n traffic thu???c VLAN 1 s??? b??? drop tr??n ???????ng trunk.
```
6100(config)# interface 1/1/10
6100(config-if)# vlan trunk allowed 5,7-9
6100(config-if)# vlan trunk native 1
```
- Tr?????ng h???p 2: C???u h??nh VLAN native l?? VLAN 1, v?? c?? cho ph??p c??? VLAN 1 tr??n ???????ng trunk. Tuy nhi??n th?? traffic thu???c VLAN 1 v???n b??? drop tr??n ???????ng trunk, do trong c??u l???nh native ch??a c?? option tag, n??n traffic untagged s??? b??? drop.
```
6100(config)# interface 1/1/10
6100(config-if)# vlan trunk allowed 1,5,7-9
6100(config-if)# vlan trunk native 1
```
Tr?????ng h???p 3: C???u h??nh VLAN native l?? VLAN 1 c?? option tag, v?? c?? cho ph??p c??? VLAN 1 tr??n ???????ng trunk. Do v???y th?? traffic c???a c??c vlan 1,5,7,8,9 s??? ???????c ??i qua ???????ng trunk b??nh th?????ng.
```
6100(config)# interface 1/1/10
6100(config-if)# vlan trunk allowed 1,5,7-9
6100(config-if)# vlan trunk native 1 tag
```
### Interface vlan
```
6100(config)# interface vlan 1
6100(config-if-vlan)# no shutdown
6100(config-if-vlan)# ip address 192.168.1.1/24
```

# DHCP server
```
dhcp-server pool "Test-2"
 default-router "10.10.1.1"
 option 43 ascii "10.80.2.201"
 option 60 ascii "ArubaAP"
 range 10.10.1.10 10.10.1.100 prefix-length 24
```
# DHCP realay
```
ip interface vlan 200
 ip address 20.1.1.1/24
 ip helper-address 70.70.70.5
 exit
 ```
 # Routing
```
ip default-gateway 10.5.9.1
ip route 20.1.1.0 255.255.255.0 70.70.70.6
ip route 30.1.1.0 255.255.255.0 70.70.70.6
```
# SNMP
```
snmp-server community "public" unrestricted
```
```
switch(config)# snmp-server vrf default
switch(config)# snmp-server system-contact JaniceM
switch(config)# snmp-server system-location Building2
switch(config)# snmp-server system-description LabSwitch
switch(config)# snmp-server community Lab8899X

```
# Configure the AOS switch IP address of the out-of-band management port:
- By default, the management interface is set to automatically obtain an IP address from a DHCP server, and SSH support is enabled. If there is no DHCP server on your network, you must configure a static address on the management interface:
```
interface mgmt
  ip static 10.9.0.2 255.255.255.0
  no shut
```
