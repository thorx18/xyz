# CCNA Command Reference

## 1. Passwords — [Router]

**Console password**
```
Router# config terminal
Router(config)# line console 0
Router(config-line)# password <password>
Router(config-line)# login
Router(config-line)# do wr
```

**Enable password**
```
Router(config)# enable password <password>
```

**Enable secret**
```
Router(config)# enable secret <password>
```

**VTY (telnet/ssh) password**
```
Router(config)# line vty 0 4
Router(config-line)# password <password>
Router(config-line)# login
Router(config-line)# do wr
```

---

## 2. Assign IP to Interface

**[Router] — FastEthernet (LAN side)**
```
Router(config)# interface fastEthernet 0/0
Router(config-if)# ip address 192.168.10.10 255.255.255.0
Router(config-if)# no shutdown
```

**[Router] — Serial (WAN / router-to-router link)**
```
Router(config)# interface serial 1/0
Router(config-if)# ip address <ip> <mask>
Router(config-if)# no shutdown
Router(config-if)# do wr
```

---

## 3. Static Routing — [Router]

```
Router(config)# ip route <destination-network> <subnet-mask> <next-hop-ip>
Router# show ip route
```

---

## 4. RIP — [Router]

```
Router(config)# router rip
Router(config-router)# version 2
Router(config-router)# network <directly-connected-network>
Router(config-router)# no auto-summary
```

---

## 5. OSPF — [Router]

```
Router(config)# router ospf 10
Router(config-router)# network <network> <wildcard-mask> area 0
```

---

## 6. EIGRP — [Router]

```
Router(config)# router eigrp 10
Router(config-router)# network <network>
```

---

## 7. DHCP — [Router]

**Pool**
```
Router(config)# ip dhcp pool <name>
Router(dhcp-config)# network <network> <mask>
Router(dhcp-config)# default-router <gateway-ip>
```

**Remove pool**
```
Router(config)# no ip dhcp pool <name>
```

**Relay agent — apply on FastEthernet (LAN-facing interface) of a router that does NOT host the DHCP pool**
```
Router(config)# interface fastEthernet 0/0
Router(config-if)# ip helper-address <DHCP-server-ip>
```

---

## 8. ACL

**Standard ACL — [Router], created in global config**
```
Router(config)# access-list 10 deny <source-ip> <wildcard-mask>
Router(config)# access-list 10 permit any
```
Apply on FastEthernet (interface near destination):
```
Router(config)# interface fastEthernet 0/0
Router(config-if)# ip access-group 10 out
```

**Extended ACL — [Router], created in global config**
```
Router(config)# access-list 100 deny tcp host <source-ip> host <dest-ip> eq <port>
Router(config)# access-list 100 permit ip any any
```
Apply on FastEthernet (interface near source):
```
Router(config)# interface fastEthernet 0/0
Router(config-if)# ip access-group 100 in
```

**Block ping (ICMP) — [Router]**
```
Router(config)# access-list 100 deny icmp host <source-ip> host <dest-ip> echo
Router(config)# access-list 100 permit ip any any
Router(config)# interface fastEthernet 0/0
Router(config-if)# ip access-group 100 in
```

---

## 9. NAT — [Router]

**Static NAT — global config, then FastEthernet (inside) + Serial (outside)**
```
Router(config)# ip route 0.0.0.0 0.0.0.0 serial 1/0
Router(config)# ip nat inside source static <private-ip> <public-ip>
Router(config)# interface fastEthernet 0/0
Router(config-if)# ip nat inside
Router(config)# interface serial 1/0
Router(config-if)# ip nat outside
```
Check:
```
Router# show ip nat translations
```

**Dynamic NAT**
```
Router(config)# access-list 10 permit <private-network> <wildcard-mask>
Router(config)# ip nat pool <name> <start-ip> <end-ip> netmask <mask>
Router(config)# ip nat inside source list 10 pool <name>
Router(config)# interface fastEthernet 0/0
Router(config-if)# ip nat inside
Router(config)# interface serial 1/0
Router(config-if)# ip nat outside
```

**PAT (overload)**
```
Router(config)# ip nat pool <name> <public-ip> <public-ip> netmask <mask>
Router(config)# ip nat inside source list 10 pool <name> overload
```

---

## 10. IPv6 — [Router]

**Enable + assign address**
```
Router(config)# ipv6 unicast-routing
Router(config)# interface fastEthernet 0/0
Router(config-if)# ipv6 address <ipv6>/64
Router(config-if)# no shutdown
Router(config)# interface serial 0/0/0
Router(config-if)# ipv6 address <ipv6>/64
Router(config-if)# no shutdown
```

**EIGRP for IPv6**
```
Router(config)# ipv6 router eigrp 10
Router(config-rtr)# router-id <x.x.x.x>
Router(config)# interface fastEthernet 0/0
Router(config-if)# ipv6 eigrp 10
Router(config)# interface serial 0/0/0
Router(config-if)# ipv6 eigrp 10
```

**OSPFv3**
```
Router(config)# ipv6 router ospf 10
Router(config-rtr)# router-id <x.x.x.x>
Router(config)# interface fastEthernet 0/0
Router(config-if)# ipv6 ospf 10 area 0
```

**RIPng**
```
Router(config)# interface fastEthernet 0/0
Router(config-if)# ipv6 rip RIPNG enable
```

---

## 11. PPP Authentication — [Router], applied on Serial interface

**PAP**
```
Router(config)# username <remote-hostname> password <password>
Router(config)# interface serial 0
Router(config-if)# ip address <ip> <mask>
Router(config-if)# no shutdown
Router(config-if)# encapsulation ppp
Router(config-if)# ppp authentication pap
Router(config-if)# ppp pap sent-username <local-hostname> password <password>
```

**CHAP**
```
Router(config)# username <remote-hostname> password <password>
Router(config)# interface serial 0
Router(config-if)# ip address <ip> <mask>
Router(config-if)# no shutdown
Router(config-if)# encapsulation ppp
Router(config-if)# ppp authentication chap
```

---

## 12. VLAN — [Switch]

**Create VLAN**
```
Switch(config)# vlan 10
Switch(config-vlan)# name <name>
```

**Assign FastEthernet ports to VLAN**
```
Switch(config)# interface fastEthernet 0/1
Switch(config-if)# switchport mode access
Switch(config-if)# switchport access vlan 10
```

**Trunk between switches — FastEthernet**
```
Switch(config)# interface fastEthernet 0/1
Switch(config-if)# switchport mode trunk
```

---

## 13. VTP — [Switch]

**Server**
```
Switch(config)# vtp mode server
Switch(config)# vtp domain <name>
Switch(config)# vtp password <password>
Switch(config)# vtp version 2
```

**Client**
```
Switch(config)# vlan 10
Switch(config-vlan)# name <same-name>
Switch(config)# vtp mode client
Switch(config)# vtp domain <same-domain>
Switch(config)# vtp password <same-password>
Switch(config)# vtp version 2
```

---

## 14. Inter-VLAN Routing — [Router], FastEthernet sub-interface

```
Router(config)# interface fastEthernet 0/0.10
Router(config-subif)# encapsulation dot1Q 10
Router(config-subif)# ip address 192.168.10.1 255.255.255.0
```

---

## 15. STP — [Switch]

```
Switch(config)# spanning-tree vlan 1 priority 4096
Switch(config)# spanning-tree vlan 1 root primary
Switch# show spanning-tree
```

---

## 16. Port Security — [Switch], FastEthernet

```
Switch(config)# interface fastEthernet 0/1
Switch(config-if)# switchport mode access
Switch(config-if)# switchport port-security
Switch(config-if)# switchport port-security maximum 2
Switch(config-if)# switchport port-security mac-address sticky
Switch(config-if)# switchport port-security violation shutdown
```

Restart a blocked port:
```
Switch(config-if)# shutdown
Switch(config-if)# no shutdown
```

---

## 17. TACACS+ — [Switch] (multilayer)

```
Switch(config)# interface vlan 1
Switch(config-if)# ip address <ip> <mask>
Switch(config-if)# no shutdown
Switch(config)# aaa new-model
Switch(config)# username <name> password <password>
Switch(config)# aaa authentication login default group tacacs+ local
Switch(config)# tacacs-server host <ip> key <key>
Switch(config)# line vty 0 4
Switch(config-line)# login authentication default
Switch(config)# aaa authorization exec default group tacacs+ local
```

---

## 18. RADIUS — [Router] only

```
Router(config)# username <name> password <password>
Router(config)# radius-server host <ip>
Router(config)# radius-server key <key>
Router(config)# aaa new-model
Router(config)# aaa authentication login default group radius local
Router(config)# line vty 0 4
Router(config-line)# login authentication default
```

---

## 19. Mininet — [Linux Terminal]

```
sudo apt install mininet
mn
sudo mn --topo minimal
sudo mn --topo single,4
sudo mn --topo linear,2,3
sudo mn --topo tree,2,3
```

Inside Mininet CLI:
```
net
nodes
dump
h1 ping h2
h1 ifconfig
exit
```

Cleanup (Linux terminal):
```
sudo mn -c
```
