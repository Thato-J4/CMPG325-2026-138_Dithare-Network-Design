# IP Addressing Plan

**Assigned block:** `192.168.59.0/24`
**Subnetting scheme:** Split into four `/27` subnets (30 usable hosts each), one per VLAN/segment.

## 1. Subnet Table

| VLAN ID | Segment | Subnet | Mask | Usable Range | Broadcast | Gateway (Router subinterface) |
|---|---|---|---|---|---|---|
| 10 | Office Data | 192.168.59.0/27 | 255.255.255.224 | .1 – .30 | .31 | 192.168.59.1 |
| 20 | CCTV | 192.168.59.32/27 | 255.255.255.224 | .33 – .62 | .63 | 192.168.59.33 |
| 30 | IoT / Operations (CR12) | 192.168.59.64/27 | 255.255.255.224 | .65 – .94 | .95 | 192.168.59.65 |
| 99 | Management | 192.168.59.96/27 | 255.255.255.224 | .97 – .126 | .127 | 192.168.59.97 |

Remaining ranges (`192.168.59.128/27` through `192.168.59.224/27`) are reserved for future growth
and are not allocated in this phase.

## 2. Outside / WAN Addressing

The router's outside interface connects to a simulated ISP link, outside the client's assigned
`192.168.59.0/24` block (as required for NAT to have a distinct inside/outside boundary):

| Link | Subnet | Router (outside) | ISP-side |
|---|---|---|---|
| Edge Router ↔ ISP Router | 203.0.113.0/30 | 203.0.113.1 | 203.0.113.2 |

## 3. Device Addressing Table (planned — to be confirmed against final Packet Tracer build)

| Device | Segment | Interface | IP Address | Subnet Mask | Default Gateway |
|---|---|---|---|---|---|
| Edge Router | Office (sub-int) | G0/1.10 | 192.168.59.1 | /27 | — |
| Edge Router | CCTV (sub-int) | G0/1.20 | 192.168.59.33 | /27 | — |
| Edge Router | IoT (sub-int) | G0/1.30 | 192.168.59.65 | /27 | — |
| Edge Router | Management (sub-int) | G0/1.99 | 192.168.59.97 | /27 | — |
| Edge Router | Outside | G0/0 | 203.0.113.1 | /30 | — |
| ISP Router | Outside link | G0/0 | 203.0.113.2 | /30 | — |
| Office PC-1 | Office | NIC | 192.168.59.2 (DHCP or static) | /27 | 192.168.59.1 |
| Office PC-2 | Office | NIC | 192.168.59.3 (DHCP or static) | /27 | 192.168.59.1 |
| CCTV Camera-1 | CCTV | NIC | 192.168.59.34 | /27 | 192.168.59.33 |
| CCTV Camera-2 | CCTV | NIC | 192.168.59.35 | /27 | 192.168.59.33 |
| IoT Sensor-1 | IoT | NIC | 192.168.59.66 | /27 | 192.168.59.65 |
| IoT Sensor-2 | IoT | NIC | 192.168.59.67 | /27 | 192.168.59.65 |
| L2 Switch (mgmt) | Management | VLAN 99 SVI | 192.168.59.98 | /27 | 192.168.59.97 |

## 4. NAT Allocation

- **Inside (translated):** VLAN 10 (Office) and VLAN 99 (Management) — permitted by ACL 1.
- **Outside (not translated / no internet access):** VLAN 20 (CCTV) and VLAN 30 (IoT) — this is a
  deliberate design decision that reinforces the segmentation constraint, since these devices have
  no functional need to reach the internet.
- **NAT type:** PAT (Port Address Translation / "overload") using the router's outside interface
  address (203.0.113.1) as the single global address — appropriate for this scale of deployment
  and avoids the need for a public address pool.

## 5. Addressing Plan Justification

- `/27` subnets were chosen because each segment (office staff, cameras, sensors) is small in
  scale for a single-site car wash business, and 30 usable addresses per segment comfortably
  covers current and near-future device counts without wasting address space.
- Keeping all four segments within one `/24` (`192.168.59.0/24`) satisfies the brief's requirement
  to use the assigned block as the basis of the addressing plan.
- A separate, clearly distinct WAN range (`203.0.113.0/30`) is used for the outside link so the
  inside/outside NAT boundary is unambiguous during demonstration and testing.
