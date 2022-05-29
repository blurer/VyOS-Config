# VyOS-Config
Setup and backup of my VyOS configs

# Base Config


## Interfaces
### eth0 -> WAN1
```
set interfaces ethernet eth0 address dhcp
set interfaces ethernet eth0 address dhcpv6
set interfaces ethernet eth0 description 'NTT DoCoMo'
```
### eth2 -> WAN2
```
set interfaces ethernet eth2 address dhcp
set interfaces ethernet eth2 address dhcpv6
set interfaces ethernet eth0 description 'Fiber'
```
### eth1 -> LAN
```
set interfaces ethernet eth1 address 10.2.1.1/24
set interfaces ethernet eth0 description 'LAN'
```

## System
### ssh
```
set service ssh port '22'
set system name-server 1.1.1.1
```
### dhcp
```
set service dhcp-server shared-network-name LAN subnet 10.2.1.0/24 default-router '10.2.1.1'
set service dhcp-server shared-network-name LAN subnet 10.2.1.0/24 name-server '10.2.1.254'
set service dhcp-server shared-network-name LAN subnet 10.2.1.0/24 domain-name 'lurer.lan'
set service dhcp-server shared-network-name LAN subnet 10.2.1.0/24 lease '600'
set service dhcp-server shared-network-name LAN subnet 10.2.1.0/24 range 0 start 10.2.1.100
set service dhcp-server shared-network-name LAN subnet 10.2.1.0/24 range 0 stop 10.2.1.200
```
## NAT
### WAN1
```
set nat source rule 100 outbound-interface 'eth0'
set nat source rule 100 source address '10.2.1.0/24'
set nat source rule 100 translation address masquerade
```
### WAN2
```
set nat source rule 200 outbound-interface 'eth2'
set nat source rule 200 source address '10.2.1.0/24'
set nat source rule 200 translation address masquerade
```

# Firewall Rules
## Base rules
```
set firewall name OUTSIDE-IN default-action 'drop'
set firewall name OUTSIDE-IN rule 10 action 'accept'
set firewall name OUTSIDE-IN rule 10 state established 'enable'
set firewall name OUTSIDE-IN rule 10 state related 'enable'
set firewall name OUTSIDE-LOCAL default-action 'drop'
set firewall name OUTSIDE-LOCAL rule 10 action 'accept'
set firewall name OUTSIDE-LOCAL rule 10 state established 'enable'
set firewall name OUTSIDE-LOCAL rule 10 state related 'enable'
set firewall name OUTSIDE-LOCAL rule 20 action 'accept'
set firewall name OUTSIDE-LOCAL rule 20 icmp type-name 'echo-request'
set firewall name OUTSIDE-LOCAL rule 20 protocol 'icmp'
set firewall name OUTSIDE-LOCAL rule 20 state new 'enable'
```
## Apply to Interfaces
```
set interfaces ethernet eth0 firewall in name 'OUTSIDE-IN'
set interfaces ethernet eth0 firewall local name 'OUTSIDE-LOCAL'
set interfaces ethernet eth2 firewall in name 'OUTSIDE-IN'
set interfaces ethernet eth2 firewall local name 'OUTSIDE-LOCAL'
```
## User Config
```
set system login user {user} authentication plaintext-password {password}
```
## Load Balance
```
set load-balancing wan interface-health eth0 failure-count 5
set load-balancing wan interface-health eth0 nexthop 192.168.128.1
set load-balancing wan interface-health eth0 test 10 type ping
set load-balancing wan interface-health eth0 test 10 target 192.168.128.1
set load-balancing wan interface-health eth1 failure-count 4
set load-balancing wan interface-health eth1 nexthop 192.168.150.1
set load-balancing wan interface-health eth1 test 10 type ping
set load-balancing wan interface-health eth1 test 10 target 192.168.150.1
set load-balancing wan interface-health eth1 test 20 type ping
set load-balancing wan interface-health eth1 test 20 target 66.77.88.99
set load-balancing wan rule 10 failover
set load-balancing wan rule 10 inbound-interface eth1
set load-balancing wan rule 10 interface eth2 weight 10
set load-balancing wan rule 10 interface eth0 weight 1
```
