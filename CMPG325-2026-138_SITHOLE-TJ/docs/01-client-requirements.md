# Client Requirements

**Project ID:** CMPG325-2026-138
**Client ID:** CLI-138
**Student:** SITHOLE, TJ (51636654)
**Assigned Organisation:** Dithare Car Wash & Detailing Group (Potchefstroom)
**Industry:** Automotive

## 1. Client Background

Dithare Car Wash & Detailing Group operates a car wash and detailing site in Potchefstroom. As
an automotive service business, the site needs to support day-to-day office operations (staff
PCs, admin, bookings), a physical security system (CCTV covering the wash bays and yard), and
an operations area that is beginning to use IoT sensors (e.g. for monitoring equipment, water
usage, or bay occupancy). The client has no formal network segmentation currently and needs a
design that keeps these traffic types separated while still giving office staff controlled access to
the internet.

## 2. Assigned Addressing Block

`192.168.59.0/24`

## 3. Functional Requirements

| # | Requirement | Source |
|---|---|---|
| R1 | Office staff devices must have LAN connectivity and internet (outbound) access | Brief §6–7 |
| R2 | CCTV traffic must be **segmented** from office data traffic | Design Constraint, Brief §8 |
| R3 | IoT sensors in the operations area must sit on their own **isolated segment** | CR12, Brief §10 |
| R4 | Internal (private) addresses must be translated to a public/outside address for internet-bound traffic | Assigned Challenge — NAT, Brief §9 |
| R5 | The network must be built and demonstrated in Cisco Packet Tracer, addressed from `192.168.59.0/24` | Brief §7 |
| R6 | Design must be testable end-to-end and evidence must be captured | Brief §11 |

## 4. Non-Functional / Constraints

- **Design Constraint:** CCTV traffic must not be able to reach the office data VLAN, and vice versa.
- **Change Request CR12:** IoT sensors added to the operations area need their own isolated
  segment — separate from both Office and CCTV.
- Only the scope described in this brief is to be implemented — no additional services beyond
  what is needed to satisfy the requirements, constraint, and change request.

## 5. Assigned Technical Challenge

**NAT (inside/outside address translation)** — Difficulty: Intermediate.

The router must be configured to translate internal private addresses (inside local) to a
public/outside address (inside global) for outbound traffic, and this must be verified with
`show ip nat translations` / `show ip nat statistics` evidence.

## 6. Departments / Traffic Types Identified

| Segment | Description | Needs Internet? | Needs to reach other segments? |
|---|---|---|---|
| Office Data | Staff PCs, admin/reception, bookings | Yes | No |
| CCTV | Security cameras covering bays/yard | No | No |
| IoT / Operations | Sensors monitoring equipment/operations area | No | No |
| Management | Switch/router management access | Yes (limited) | No |

## 7. Design Decisions Arising from Requirements

- **VLAN segmentation** is used to satisfy R2 and R3 — each traffic type (Office, CCTV, IoT) is
  placed in its own VLAN and subnet, so they are isolated at Layer 2 by default.
- **Inter-VLAN routing via router-on-a-stick** is used so a single router interface (trunked) can
  route between VLANs where required, while an **access control list (ACL)** explicitly denies
  CCTV↔Office and IoT↔Office traffic to enforce the segmentation constraint at Layer 3 as well.
- **NAT (PAT/overload)** is applied only to the Office and Management VLANs, since CCTV and IoT
  devices do not require internet access — this also reduces their exposure, reinforcing the
  isolation requirement.
- The `192.168.59.0/24` block is subnetted into four `/27` subnets (30 usable hosts each) to give
  each segment room to grow while keeping the addressing plan simple to document and defend.
