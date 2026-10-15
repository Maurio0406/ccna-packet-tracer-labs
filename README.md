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

### Lab 2 - Routing Between Networks ✅
- [x] Add a Cisco router between two switched LANs
- [x] Create two IPv4 networks
- [x] Configure and enable router interfaces through Cisco IOS
- [x] Configure default gateways on both PCs
- [x] Verify router interfaces with `show ip interface brief`
- [x] Inspect connected and local routes with `show ip route`
- [x] Verify end-to-end communication between networks
- [x] Save the router configuration to startup-config

#### Topology
```text
PC1                 Switch1          Router          Switch2                 PC2
192.168.10.10/24  -----------  G0/0        G0/1  -----------  192.168.20.10/24
                               .10.1        .20.1
```

#### Addressing
| Device | Interface | IPv4 Address | Default Gateway |
| --- | --- | --- | --- |
| PC1 | FastEthernet0 | 192.168.10.10/24 | 192.168.10.1 |
| Router | G0/0 | 192.168.10.1/24 | N/A |
| Router | G0/1 | 192.168.20.1/24 | N/A |
| PC2 | FastEthernet0 | 192.168.20.10/24 | 192.168.20.1 |

#### Router Configuration
```text
enable
configure terminal
interface gigabitEthernet 0/0
ip address 192.168.10.1 255.255.255.0
no shutdown
exit
interface gigabitEthernet 0/1
ip address 192.168.20.1 255.255.255.0
no shutdown
end
```

#### Verification
Both router interfaces reached an `up/up` state. PC1 successfully pinged its default gateway and PC2 on the remote network with 0% packet loss after ARP resolution.

The routing table contained directly connected routes for `192.168.10.0/24` through G0/0 and `192.168.20.0/24` through G0/1.

#### Troubleshooting
During verification, G0/1 was initially configured as `192.68.20.1` instead of `192.168.20.1`. The physical interface still showed `up/up`, demonstrating that link status alone does not prove the Layer 3 configuration is correct. I found the error with `show ip interface brief` and corrected the address.

#### What I Learned
- Routers forward traffic between different IP networks.
- Hosts send remote-network traffic to their default gateway.
- Router interfaces need an IP address and `no shutdown`.
- `show ip interface brief` quickly verifies interface addressing and status.
- `show ip route` displays the router's routing table.
- `C` identifies connected network routes and `L` identifies the router's local interface addresses.
- Directly connected networks are added to the routing table automatically when their interfaces are operational.
- A successful ping across the router confirms Layer 3 connectivity.
- `copy running-config startup-config` saves the active configuration.

### Lab 3 - VLANs ✅
- [x] Create IT, HR, Finance, and Sales VLANs on a Cisco 2960
- [x] Assign four access ports to their respective VLANs
- [x] Configure static IP addresses for four PCs
- [x] Verify VLAN assignments with `show vlan brief`
- [x] Test VLAN isolation using ping
- [x] Temporarily move HR's PC into the IT VLAN to test same-VLAN connectivity
- [x] Restore HR's original VLAN and IP address
- [x] Save the switch running configuration to startup-config

#### Topology and Addressing
| PC | Department | VLAN | Switch Port | IP Address |
| --- | --- | --- | --- | --- |
| PC1 | IT | 10 | Fa0/1 | 192.168.10.10/24 |
| PC2 | HR | 20 | Fa0/2 | 192.168.20.10/24 |
| PC3 | Finance | 30 | Fa0/3 | 192.168.30.10/24 |
| PC4 | Sales | 40 | Fa0/4 | 192.168.40.10/24 |

All four PCs connect to one Cisco 2960 switch. No router or default gateways are configured.

#### Switch Configuration Example
```text
enable
configure terminal
vlan 10
name IT
exit
interface fastEthernet 0/1
switchport mode access
switchport access vlan 10
end
show vlan brief
copy running-config startup-config
```
The same VLAN creation and access-port assignment process was repeated for HR (20), Finance (30), and Sales (40).

#### Verification and Experiment
`show vlan brief` showed Fa0/1 in VLAN 10, Fa0/2 in VLAN 20, Fa0/3 in VLAN 30, and Fa0/4 in VLAN 40. A ping from IT's PC1 to HR's PC2 timed out as expected because the VLANs are isolated and no inter-VLAN routing is configured.

For an experiment, PC2 was temporarily changed to `192.168.10.20/24` and its Fa0/2 switch port was moved into VLAN 10. The user confirmed the same-VLAN test succeeded. PC2's IP and switch port were then restored to the HR network and VLAN 20.

#### What I Learned
- VLANs create separate Layer 2 broadcast domains on a shared physical switch.
- Cisco switch ports belong to VLAN 1 by default.
- Creating a VLAN does not automatically move ports into it.
- An access port belongs to one VLAN, while a trunk carries multiple VLANs.
- Devices in different VLANs require Layer 3 routing to communicate.
- `show vlan brief` verifies VLAN names and access-port membership.

### Lab 4 - Trunking
- [ ] Connect two switches
- [ ] Configure and verify an 802.1Q trunk
- [ ] Test same-VLAN connectivity across switches

### Lab 5 - Inter-VLAN Routing
- [ ] Configure routing between VLANs
- [ ] Verify connectivity
- [ ] Troubleshoot an intentionally broken configuration

### Lab 6 - Network Services and Security
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
| `configure terminal` | Enters global configuration mode |
| `interface gigabitEthernet 0/0` | Enters configuration mode for a router interface |
| `ip address <IP> <mask>` | Assigns an IPv4 address and subnet mask to an interface |
| `no shutdown` | Administratively enables an interface |
| `show ip interface brief` | Summarizes interface IP addresses and up/down status |
| `show ip route` | Displays the router's IPv4 routing table |
| `vlan 10` | Creates or enters VLAN 10 configuration mode |
| `name IT` | Names the selected VLAN |
| `switchport mode access` | Configures a switch port as an access port |
| `switchport access vlan 10` | Assigns an access port to VLAN 10 |
| `show vlan brief` | Lists VLANs and their assigned access ports |
| `copy running-config startup-config` | Saves the active configuration for the next reboot |

## Screenshots and Network Diagrams
Packet Tracer files, screenshots, and diagrams will be added as the labs progress.

## Current Status
Labs 1, 2, and 3 completed. Next: Lab 4 - Trunking.
