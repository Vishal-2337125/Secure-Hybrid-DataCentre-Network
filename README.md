# Secure Hybrid DataCentre Network Architecture

Cisco Virtual Internship 2026 (Cybersecurity) | Cisco Networking Academy x AICTE

A secure hybrid network design (on-premises datacenter + simulated cloud VPCs) built and tested in **Cisco Packet Tracer**. The project focuses on network segmentation, perimeter hardening with a **Cisco ASA firewall**, and access control to stop lateral movement after a breach.

## Objectives

- **Network segmentation:** separate users, servers, databases, admin and DMZ traffic using 802.1Q VLANs and distinct subnets.
- **Perimeter hardening:** stateful inspection, NAT/PAT and access control on the Cisco ASA.
- **Least privilege:** only the traffic each zone needs is allowed.
- **Containment:** block pivoting between cloud VPCs and between datacenter segments.

## Architecture

| Component | Details |
|---|---|
| Routers | 3 x Cisco ISR 2911 (ISP, Enterprise Edge, Cloud Router) |
| Firewall | 1 x Cisco ASA 5505 (Inside, Outside and DMZ/Cloud zones) |
| Core switch | 1 x Cisco 3560 (Layer 3, SVI routing, trunk hub) |
| Access switches | 7 x Cisco 2960 (5 on-premises, 2 cloud VPC tiers) |
| Endpoints | 7 servers, 3 PCs |

### VLANs and addressing

| VLAN | Name | Subnet |
|---|---|---|
| 10 | USERS | 192.168.10.0/24 |
| 20 | APP_SERVERS | 192.168.20.0/24 |
| 30 | DB_SERVERS | 192.168.30.0/24 |
| 40 | ADMIN | 192.168.40.0/24 |
| 50 | DMZ | 192.168.50.0/24 |
| 60 | MANAGEMENT | 192.168.60.0/24 |
| n/a | Cloud VPC 1 (App tier) | 172.16.10.0/24 |
| n/a | Cloud VPC 2 (DB tier) | 172.16.20.0/24 |

## Key Features

- Inter-VLAN routing on the Layer 3 core switch
- DHCP pools for the user and admin VLANs
- Static routing across the core, ASA, edge and cloud router
- ASA security zones with dynamic NAT/PAT
- Extended ACLs (101, 102, 103) for micro-segmentation:
  - Users cannot query database tiers directly
  - Only application servers can reach databases (TCP 3306)
  - SSH to the management network is allowed only from the Admin VLAN

## Test Results

| ID | Test | Expected | Result |
|---|---|---|---|
| TC-01 | User PC to default gateway (ping) | Success | Pass |
| TC-02 | Core switch to ASA inside (ping) | Success | Pass |
| TC-03 | ASA outside to edge router (ping) | Success | Pass |
| TC-04 | User PC to cloud app (HTTPS 443) | Success | Pass |
| TC-05 | Cloud App 1 to Cloud App 2 (lateral pivot) | Blocked | Pass |
| TC-06 | User PC to cloud DB (MySQL 3306) | Blocked | Pass |
| TC-07 | Cloud App 1 to cloud DB (MySQL 3306) | Success | Pass |
| TC-08 | User PC to management SSH | Blocked | Pass |
| TC-09 | Admin PC to management SSH | Success | Pass |

## Packet Tracer Limitations

Some production cloud features cannot be fully simulated in Packet Tracer, including cloud IAM policies, MFA push or hardware tokens, stateful cloud security groups, Kubernetes and container network policies, and cloud logging or threat detection. These are documented in the report as requirements for a real deployment.

## Files

| File | Description |
|---|---|
| `Secure_Hybrid_DataCentre.pka` | Cisco Packet Tracer project file |
| `Cisco_Cybersecurity_Project.pdf` | Full project documentation |

## How to Open

1. Install Cisco Packet Tracer.
2. Open the `.pka` file.
3. Use the commands in the report to verify the setup, for example `show ip route`, `show access-lists`, `show ip dhcp binding` and (on the ASA) `show nameif`, `show xlate`, `show conn`.

## Tools Used

Cisco Packet Tracer, Cisco ASA 5505, Cisco IOS, VLANs (802.1Q), ACLs, NAT/PAT, DHCP, static routing.

