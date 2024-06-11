# Case Study: Network Design and Implementation for Routing and Switching Essentials (31277)

**Author**: Tommy Huynh

**October 2023**

## Introduction

This case study is part of the "Routing and Switching Essentials (31277)" course at the University of Technology Sydney. The objective of this project is to design, implement, and troubleshoot a comprehensive network infrastructure that integrates various networking concepts and protocols. Our team has undertaken a systematic approach to address the different aspects of network configuration, including IP addressing, static and dynamic routing, VLAN configuration, Network Address Translation (NAT), Access Control Lists (ACLs), Dynamic Host Configuration Protocol (DHCP), and Wireless Local Area Network (WLAN) setup.

Throughout this project, we employed a combination of theoretical knowledge and practical skills to create a robust and efficient network. Each part of the project has been carefully documented to provide clear guidance on the steps taken, the challenges faced, and the solutions implemented. The following sections of this README file will walk you through each phase of the project, detailing the configurations and troubleshooting processes involved.

Our goal is to ensure that this case study not only meets the course requirements but also serves as a valuable learning resource for students and professionals interested in networking. By following the structured approach outlined in this document, readers will gain insights into effective network design and management practices.

## Contents

1. [Part 1: VLSM IP Addressing](#part-1-vlsm-ip-addressing)
2. [Part 2: Construct the Network](#part-2-construct-the-network)
3. [Part 3: Configure Static Routing](#part-3-configure-static-routing)
4. [Part 4: Configuring OSPF](#part-4-configuring-ospf)
5. [Part 5: Configuring VLAN and Inter-VLAN](#part-5-configuring-vlan-and-inter-vlan)
6. [Part 6: Configure NAT](#part-6-configure-nat)
7. [Part 7: Configure ACLs](#part-7-configure-acls)
8. [Part 8: Configure DHCP](#part-8-configure-dhcp)
9. [Part 9: Configure WLAN](#part-9-configure-wlan)

## Part 1: VLSM IP Addressing

Variable Length Subnet Mask (VLSM) allows a network administrator to use more than one subnet mask within the same network address space. VLSM optimizes available address space and is vital for large organizations to use IP address allocations efficiently. This report presents the VLSM IP addressing scheme designed for the Branch and HQ networks.

- **Branch IP Range**: 192.168.0.0/24
- **HQ IP Range**: 172.10.0.0/24

### Subnets and Addressing

| Subnet Name  | Network Address | Subnet Mask         | Usable Range                   | Broadcast Address   |
|--------------|-----------------|---------------------|--------------------------------|---------------------|
| HQ LAN-D     | 172.10.0.0/25   | 255.255.255.128     | 172.10.0.1 - 172.10.0.126      | 172.10.0.127        |
| Branch LAN-A VLAN 20 | 192.168.0.0/26 | 255.255.255.192 | 192.168.0.1 - 192.168.0.62 | 192.168.0.63        |
| Branch LAN-A VLAN 10 | 192.168.0.64/27 | 255.255.255.224 | 192.168.0.65 - 192.168.0.94 | 192.168.0.95        |
| HQ LAN-C     | 172.10.0.128/28 | 255.255.255.240     | 172.10.0.129 - 172.10.0.142    | 172.10.0.143        |
| Branch LAN-A VLAN 99 | 192.168.0.96/29 | 255.255.255.248 | 192.168.0.97 - 192.168.0.102 | 192.168.0.103       |
| Branch LAN-B | 192.168.0.104/29 | 255.255.255.248 | 192.168.0.105 - 192.168.0.110 | 192.168.0.111       |

### Addressing Table

| Device Name | Interface    | Default Gateway | Subnet Mask       |
|-------------|--------------|-----------------|-------------------|
| Internet    | G0/0         | 138.25.88.85    | 255.255.255.252   |
| Internet    | S0/1/1       | 209.165.200.1   | 255.255.0.0       |
| R1          | G0/0         | 10.0.0.1        | 255.255.255.248   |
| R1          | S0/1/0       | 10.0.0.9        | 255.255.255.252   |
| R1          | S0/1/1       | 209.165.200.2   | 255.255.0.0       |
| R2          | G0/0.10      | 192.168.0.65    | 255.255.255.224   |
| R2          | G0/0.20      | 192.168.0.1     | 255.255.255.192   |
| R2          | G0/0.99      | 192.168.0.97    | 255.255.255.248   |
| R2          | G0/1         | 10.0.0.2        | 255.255.255.248   |
| R3          | G0/0         | 192.168.0.105   | 255.255.255.248   |
| R3          | G0/1         | 10.0.0.3        | 255.255.255.248   |
| R4          | S0/1/0       | 10.0.0.10       | 255.255.255.252   |
| R4          | S0/1/1       | 10.0.0.13       | 255.255.255.252   |
| R4          | S0/0/0       | 10.0.0.22       | 255.255.255.252   |
| R5          | G0/0         | 172.10.0.129    | 255.255.255.240   |
| R5          | S0/1/0       | 10.0.0.17       | 255.255.255.252   |
| R5          | S0/1/1       | 10.0.0.14       | 255.255.255.252   |
| R6          | G0/0         | 172.10.0.1      | 255.255.255.128   |
| R6          | G0/1         | 192.168.10.1    | 255.255.255.0     |
| R6          | S0/1/0       | 10.0.0.18       | 255.255.255.252   |
| R6          | S0/1/1       | 10.0.0.21       | 255.255.255.252   |
| Sw1         | VLAN 99      | 192.168.0.98    | 255.255.255.248   |
| Host A1     | NIC VLAN 10  | DHCP            | DHCP Assigned     |
| Host A2     | NIC VLAN 20  | 192.168.0.2     | 255.255.255.192   |
| Host B      | NIC          | 192.168.0.106   | 255.255.255.248   |
| Host C      | NIC          | DHCP            | DHCP Assigned     |
| Host D      | NIC          | 172.10.0.2      | 255.255.255.128   |
| Mobile Host | Wireless NIC | DHCP            | DHCP Assigned     |
| External Host | NIC        | 138.25.88.86    | 255.255.255.252   |

## Part 2: Construct the Network

### Network Design and Implementation

- The network was designed with a combination of routers, switches, and end devices.
- Devices were named per the provided list.
- IP addresses were assigned following the IP addressing table after VLSM assignment.
- DNS lookup on routers was disabled to prevent delays when mistyping commands.

### Images

<img width="641" alt="image" src="https://github.com/tnptommy/Case-Study-Comprehensive-Network-Configuration-and-Management/assets/106922908/9606be1b-8fba-4000-9db8-a5bdd9ef9e77">

*Network Topology Diagram*

## Part 3: Configure Static Routing

### Design and Implementation

- The Internet router has static routes directing traffic destined for internal addresses to R1.
- R1 has a default route pointing to the Internet router.

### Troubleshooting

- Initially, some internal devices could not reach the Internet due to improperly configured static routes. Using the `show ip route` command helped identify missing routes, which were then added.

## Part 4: Configuring OSPF

### Design and Implementation

- OSPF was activated using process ID 10 on all routers.
- Router R1 was configured to always be the designated router for stability.

### Troubleshooting

- Router IDs were set according to design requirements. An OSPF process restart was required after setting the router IDs to ensure uniqueness and proper distinction.

## Part 5: Configuring VLAN and Inter-VLAN

### Design and Implementation

- VLANs 10, 20, and 99 were configured on Sw1 with appropriate mappings to interfaces.
- Inter-VLAN routing was configured on R2.

### Troubleshooting

- Misconfigurations on the trunk link initially disrupted inter-VLAN communication. Correctly specifying the allowed VLANs restored communication.
- Sub-interfaces on R2 sometimes did not activate immediately, which was resolved by toggling the primary interface (G0/0).

## Part 6: Configure NAT

### Design and Implementation

- Static NAT was configured for Host D on HQ LAN D.
- Dynamic PAT was set up for Branch LAN-A using a NAT pool.

### Troubleshooting

- NAT translations were initially ineffective due to missing routing table entries. Adjusting static routes resolved the issue.

## Part 7: Configure ACLs

### Design and Implementation

- ACLs were configured to restrict external traffic from accessing SSH lines on R3 and to prevent hosts from HQ LAN-D from accessing HQ LAN-C.

### Troubleshooting

- Initial ACL configurations caused unintended traffic blockage. Adjusting the specificity of the ACL rules resolved the issue.

## Part 8: Configure DHCP

### Design and Implementation

- R4 was set as the DHCP server for specific network segments.
- R2 and R5 were configured as DHCP relay agents.

### Troubleshooting

- DHCP requests were not received initially due to trunk link issues, which were resolved by allowing VLAN 10 traffic.

## Part 9: Configure WLAN

### Design and Implementation

- A wireless router was configured with appropriate IP settings and DHCP.
- WPA2 security was implemented for the wireless network.

### Troubleshooting

- Configurations were straightforward and primarily GUI-based.

### Images

<img width="597" alt="image" src="https://github.com/tnptommy/Case-Study-Comprehensive-Network-Configuration-and-Management/assets/106922908/b440a9a5-1aba-4f0a-9a61-d0b18fe47166">

*Wireless Network Configuration*

---
