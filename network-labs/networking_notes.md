
# Networking Notes

## Subnetting Quick Reference
- /24 → 255.255.255.0 → 256 hosts (254 usable)
- /25 → 255.255.255.128 → 128 hosts (126 usable)
- /26 → 255.255.255.192 → 64 hosts (62 usable)
- /30 → 255.255.255.252 → 4 hosts (2 usable)

**Formula:**  
`Host count = 2^(32 - prefix) - 2`

## VLAN Basics
- VLAN separates broadcast domains within a switch.
- `switchport mode access` → Assign port to VLAN
- `switchport access vlan X` → Set VLAN ID

## Cisco IOS Quick Commands
```bash
enable
configure terminal
hostname SWITCH1
vlan 10
name HR
interface fa0/1
switchport mode access
switchport access vlan 10
exit
```

## Router Inter-VLAN Configuration
```bash
interface g0/0.10
encapsulation dot1Q 10
ip address 192.168.10.1 255.255.255.0
no shutdown

interface g0/0.20
encapsulation dot1Q 20
ip address 192.168.20.1 255.255.255.0
no shutdown
```
