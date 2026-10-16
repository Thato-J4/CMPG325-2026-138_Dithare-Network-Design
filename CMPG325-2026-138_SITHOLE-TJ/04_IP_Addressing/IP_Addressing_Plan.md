# IP Addressing Plan — Dithare Car Wash & Detailing Group
**Project ID:** CMPG325-2026-138 | **Client ID:** CLI-138
**Student:** SITHOLE, TJ (51636654)
**Organisation:** Dithare Car Wash & Detailing Group | **Location:** Potchefstroom
**Addressing Block:** 192.168.59.0/24
**Logical Topology Reference:** DITHARE_FINAL_diagramsnet_style.svg (Final Approved)

## Step 1 — Master Addressing Block
- **Addressing Block:** 192.168.59.0/24
- Network: 192.168.59.0 | Mask: 255.255.255.0 | Total: 256 | Usable: 254
- Range: 192.168.59.1 – 192.168.59.254 | Broadcast: 192.168.59.255
- Subnetting: Divide /24 into /27s (32 addresses, 30 usable each) for VLAN 10,20,30

## Step 2 — VLAN 10: Office Data — 192.168.59.0/27
| Item | Value |
|------|-------|
| Network address | 192.168.59.0 |
| Subnet mask | 255.255.255.224 (/27) |
| Default gateway | 192.168.59.1 (R1 G0/1.10) |
| Usable host range | 192.168.59.2 – 192.168.59.30 |
| Broadcast address | 192.168.59.31 |
| VLAN | 10 |
| Purpose | Office Data |

**Devices:**
| Device | IP Address | Switch Port |
|--------|------------|-------------|
| R1 G0/1.10 | 192.168.59.1 | — Gateway |
| PC1 | 192.168.59.2 | Fa0/1 |
| PC2 | 192.168.59.3 | Fa0/2 |
| Printer | 192.168.59.4 | Fa0/3 |

Note: .1 belongs to router, first end device starts at .2. Ports Fa0/4–5 allocated to VLAN 10 but no IP assigned (allocation ≠ active device).

## Step 3 — VLAN 20: CCTV — 192.168.59.32/27
| Item | Value |
|------|-------|
| Network address | 192.168.59.32 |
| Subnet mask | 255.255.255.224 (/27) |
| Default gateway | 192.168.59.33 (R1 G0/1.20) |
| Usable host range | 192.168.59.34 – 192.168.59.62 |
| Broadcast address | 192.168.59.63 |
| VLAN | 20 |
| Purpose | CCTV |

**Devices:**
| Device | IP Address | Switch Port |
|--------|------------|-------------|
| R1 G0/1.20 | 192.168.59.33 | — Gateway |
| Camera 1 | 192.168.59.34 | Fa0/6 |
| Camera 2 | 192.168.59.35 | Fa0/7 |
| NVR | 192.168.59.36 | Fa0/8 |

Note: .33 is gateway, end devices begin at .34. Ports Fa0/9–10 allocated to VLAN 20 but no IP assigned.

## Step 4 — VLAN 30: IoT / Operations — 192.168.59.64/27
| Item | Value |
|------|-------|
| Network address | 192.168.59.64 |
| Subnet mask | 255.255.255.224 (/27) |
| Default gateway | 192.168.59.65 (R1 G0/1.30) |
| Usable host range | 192.168.59.66 – 192.168.59.94 |
| Broadcast address | 192.168.59.95 |
| VLAN | 30 |
| Purpose | IoT / Operations |

**Devices:**
| Device | IP Address | Switch Port |
|--------|------------|-------------|
| R1 G0/1.30 | 192.168.59.65 | — Gateway |
| Sensor 1 | 192.168.59.66 | Fa0/11 |
| Sensor 2 | 192.168.59.67 | Fa0/12 |
| IoT Controller | 192.168.59.68 | Fa0/13 |

Note: .65 is gateway, end devices begin at .66. Ports Fa0/14–15 allocated to VLAN 30 but no IP assigned.

## Step 5 — WAN / External Network — 203.0.113.0/30
Separate from internal 192.168.59.0/24 block.

| Device/Interface | IP Address | Role |
|------------------|------------|------|
| Network | 203.0.113.0/30 | WAN |
| ISP Router | 203.0.113.1/30 | External gateway |
| R1 G0/0 | 203.0.113.2/30 | NAT Outside |
| Broadcast | 203.0.113.3 | Broadcast |

/30 provides exactly 2 usable addresses — ideal for point-to-point WAN. R1 default route: ip route 0.0.0.0 0.0.0.0 203.0.113.1
NAT: ip nat inside source list 10 interface G0/0 overload, access-list 10 permit 192.168.59.0 0.0.0.31 (only VLAN 10 Office)

## Step 6 — Master IP Addressing Table (Submission Table)
| VLAN / Network | Device | Interface | IP Address | Mask | Gateway | Purpose |
|----------------|--------|-----------|------------|------|---------|---------|
| WAN | ISP Router | G0/0 | 203.0.113.1 | /30 | — | External network |
| WAN | R1 | G0/0 | 203.0.113.2 | /30 | 203.0.113.1 | NAT Outside |
| VLAN 10 | R1 | G0/1.10 | 192.168.59.1 | /27 | — | Office gateway |
| VLAN 10 | PC1 | Fa0/1 | 192.168.59.2 | /27 | .1 | Office workstation |
| VLAN 10 | PC2 | Fa0/2 | 192.168.59.3 | /27 | .1 | Office workstation |
| VLAN 10 | Printer | Fa0/3 | 192.168.59.4 | /27 | .1 | Office printer |
| VLAN 20 | R1 | G0/1.20 | 192.168.59.33 | /27 | — | CCTV gateway |
| VLAN 20 | Camera 1 | Fa0/6 | 192.168.59.34 | /27 | .33 | CCTV |
| VLAN 20 | Camera 2 | Fa0/7 | 192.168.59.35 | /27 | .33 | CCTV |
| VLAN 20 | NVR | Fa0/8 | 192.168.59.36 | /27 | .33 | CCTV recording |
| VLAN 30 | R1 | G0/1.30 | 192.168.59.65 | /27 | — | IoT gateway |
| VLAN 30 | Sensor 1 | Fa0/11 | 192.168.59.66 | /27 | .65 | IoT sensor |
| VLAN 30 | Sensor 2 | Fa0/12 | 192.168.59.67 | /27 | .65 | IoT sensor |
| VLAN 30 | IoT Controller | Fa0/13 | 192.168.59.68 | /27 | .65 | Operations |

**Preserved Principle:** Port allocation (Fa0/1–5 → VLAN10 etc.) defines VLAN membership. IP Addressing Plan assigns IPs only to actual network devices, not to empty allocated ports.

## Step 7 — Addressing Summary (Matches Logical Topology Final SVG)
| Network | VLAN | Purpose | Gateway | Usable Hosts | Broadcast |
|---------|------|---------|---------|--------------|-----------|
| 192.168.59.0/27 | 10 | Office Data | .1 | .2–.30 | .31 |
| 192.168.59.32/27 | 20 | CCTV | .33 | .34–.62 | .63 |
| 192.168.59.64/27 | 30 | IoT / Operations | .65 | .66–.94 | .95 |
| 203.0.113.0/30 | WAN | External | .1 ISP / .2 R1 | .1–.2 | .3 |

This matches Logical Topology DITHARE_FINAL_diagramsnet_style.svg 100% — same gateways, same device IPs, same Fa ports, same NAT scope (Office only).
