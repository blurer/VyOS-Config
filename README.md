# VyOS-Config
Setup and backup of my VyOS configs

# Base Config


# Interfaces
## eth0 -> WAN1
```
set interfaces ethernet eth0 address dhcp
set interfaces ethernet eth0 address dhcpv6
set interfaces ethernet eth0 description 'NTT DoCoMo'
```
## eth2 -> WAN2
```
set interfaces ethernet eth2 address dhcp
set interfaces ethernet eth2 address dhcpv6
set interfaces ethernet eth0 description 'Fiber'
```
## eth1 -> LAN
set interfaces ethernet eth1 address 10.2.1.1/24
set interfaces ethernet eth0 description 'LAN'


# System
## ssh
set service ssh port '22'

## dhcp
set service dhcp-server shared-network-name LAN subnet 10.2.1.0/24 default-router '10.2.1.1'
set service dhcp-server shared-network-name LAN subnet 10.2.1.0/24 name-server '10.2.1.254'
set service dhcp-server shared-network-name LAN subnet 10.2.1.0/24 domain-name 'lurer.lan'
set service dhcp-server shared-network-name LAN subnet 10.2.1.0/24 lease '600'
set service dhcp-server shared-network-name LAN subnet 10.2.1.0/24 range 0 start 10.2.1.100
set service dhcp-server shared-network-name LAN subnet 10.2.1.0/24 range 0 stop 10.2.1.200

# NAT
## WAN1
set nat source rule 100 outbound-interface 'eth0'
set nat source rule 100 source address '10.2.1.0/24'
set nat source rule 100 translation address masquerade

## WAN2
set nat source rule 200 outbound-interface 'eth2'
set nat source rule 200 source address '10.2.1.0/24'
set nat source rule 200 translation address masquerade
