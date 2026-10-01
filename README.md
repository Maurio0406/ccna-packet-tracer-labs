# CCNA Packet Tracer Labs

## Project Overview
This repository documents my hands-on networking practice while studying for the Cisco CCNA. I use Cisco Packet Tracer to build networks, configure Cisco devices, troubleshoot connectivity problems, and connect the concepts I study to practical labs.

This repository will grow as my networking knowledge improves.

## Goals
- Strengthen my CCNA knowledge through hands-on practice
- Understand IPv4 addressing and subnetting
- Configure Cisco switches and routers
- Build and troubleshoot VLANs
- Configure trunk links
- Configure inter-VLAN routing
- Configure DHCP
- Practice secure remote management with SSH
- Configure access control lists (ACLs)
- Practice switch port security
- Develop a repeatable network troubleshooting process

## Tools
- Cisco Packet Tracer
- Cisco IOS CLI
- GitHub for documentation

## Lab Progress

### Lab 1 - Basic LAN ✅
- [x] Add two PCs and a Cisco 2960 switch
- [x] Connect the PCs to the switch with Copper Straight-Through cables
- [x] Configure static IPv4 addresses
- [x] Verify connectivity with ping
- [x] Inspect the ARP table
- [x] Inspect the switch MAC address table
- [x] Create and troubleshoot an intentional subnet mismatch

#### Topology
```text
PC1 ---------------- Cisco 2960 ---------------- PC2
192.168.10.10/24                              192.168.10.11/24
```

#### Addressing
| Device | IPv4 Address | Subnet Mask | Network |
| --- | --- | --- | --- |
| PC1 | 192.168.10.10 | 255.255.255.0 | 192.168.10.0/24 |
| PC2 | 192.168.10.11 | 255.255.255.0 | 192.168.10.0/24 |

No default gateway is required for this lab because both hosts are on the same subnet.

#### Verification
PC1 successfully pinged PC2 at `192.168.10.11` with 4 packets sent, 4 received, and 0% packet loss.

I used `arp -a` to verify the IP-to-MAC mapping learned through ARP.

On the switch, I used:
```text
enable
show mac address-table
```

The switch dynamically learned MAC addresses on its FastEthernet ports.

#### Troubleshooting Exercise
I changed PC2 from `192.168.10.11/24` to `192.168.20.11/24`. PC1 then received 100% packet loss when pinging PC2.

PC1 belonged to `192.168.10.0/24`, while PC2 belonged to `192.168.20.0/24`. Since there was no router or default gateway, the two different networks had no Layer 3 path between them.

I restored PC2 to `192.168.10.11/24` and verified connectivity again.

#### What I Learned
- Devices on the same subnet communicate through a switch without needing a router.
- Each host on a subnet needs a unique IP address.
- ARP maps an IPv4 address to a MAC address.
- `arp -a` displays learned ARP entries on a PC.
- A Layer 2 switch learns source MAC addresses and associates them with switch ports.
- `show mac address-table` displays the switch MAC address table.
- Communication between different IP networks requires Layer 3 routing.

### Lab 2 - Routing Between Networks
- [ ] Add a router
- [ ] Create two IP networks
- [ ] Configure router interfaces
- [ ] Configure default gateways
- [ ] Verify communication between networks

### Lab 3 - VLANs
- [ ] Create IT, HR, Finance, and Sales VLANs
- [ ] Assign switch access ports
- [ ] Configure trunking
- [ ] Verify VLAN configuration

### Lab 4 - Inter-VLAN Routing
- [ ] Configure routing between VLANs
- [ ] Verify connectivity
- [ ] Troubleshoot an intentionally broken configuration

### Lab 5 - Network Services and Security
- [ ] Configure DHCP
- [ ] Configure SSH
- [ ] Configure an ACL
- [ ] Configure switch port security

## Troubleshooting Log
I will document networking problems here instead of only recording successful configurations.

### Lab 1 - Hosts on Different Subnets
Problem: PC1 could not reach PC2 after PC2's address was changed.

Expected behavior: The ping should fail because the hosts were on different /24 networks without a router.

Actual behavior: Four ping requests timed out, resulting in 100% packet loss.

Commands/tools used:
- `ping`
- `arp -a`
- `show mac address-table`

Root cause: PC1 was on `192.168.10.0/24` and PC2 was on `192.168.20.0/24`. No router or default gateway existed between the networks.

Solution: Restore PC2 to `192.168.10.11/24`.

What I learned: A switch provides Layer 2 connectivity inside the LAN, while communication between different IP networks requires routing.

## Useful Commands
| Command | Purpose |
| --- | --- |
| `ping <IP>` | Tests IP connectivity to another host |
| `arp -a` | Displays IP-to-MAC mappings in the ARP table |
| `enable` | Enters privileged EXEC mode on a Cisco device |
| `show mac address-table` | Displays MAC addresses learned by the switch and their associated ports |

## Screenshots and Network Diagrams
Packet Tracer files, screenshots, and diagrams will be added as the labs progress.

## Current Status
Lab 1 - Basic LAN completed. Next: Lab 2 - Routing Between Networks.
