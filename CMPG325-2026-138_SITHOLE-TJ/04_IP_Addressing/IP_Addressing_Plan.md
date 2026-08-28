# IP Addressing Plan

**Project ID:** CMPG325-2026-138
**Client ID:** CLI-138
**Student:** SITHOLE, TJ (51636654)
**Organisation:** Dithare Car Wash & Detailing Group (Potchefstroom)
**Industry:** Automotive
**Assigned addressing block:** `192.168.59.0/24`

This plan is the addressing source of truth for Milestone 1. Device IPs, VLANs and gateways here must match the logical topology and the later Packet Tracer build.

---

## 1. Addressing objectives

| Objective                       | How it is met                                                    |
| ------------------------------- | ---------------------------------------------------------------- |
| Stay inside the assigned block  | All internal networks are carved from `192.168.59.0/24`          |
| Segment Office from CCTV        | Separate `/27` subnets and VLANs                                 |
| Isolate IoT / operations (CR12) | Dedicated `/27` subnet and VLAN                                  |
| Support NAT inside/outside      | Private Office addresses translate on R1 toward `203.0.113.0/30` |
| Leave room for growth           | Unused `/27`s remain in the `/24`                                |
| Keep the scheme easy to verify  | Equal `/27`s, gateway = first usable address in each subnet      |

The WAN link is **not** taken from `192.168.59.0/24`. A documentation prefix (`203.0.113.0/30`, TEST-NET-3) is used so NAT inside (private) and NAT outside (public-style) addresses are visually distinct.

---

## 2. Master block — `192.168.59.0/24`

| Item                                | Value            |
| ----------------------------------- | ---------------- |
| Network address                     | `192.168.59.0`   |
| Subnet mask                         | `255.255.255.0`  |
| Prefix                              | `/24`            |
| Total addresses                     | 256              |
| Usable host addresses (unsubnetted) | 254              |
| First usable                        | `192.168.59.1`   |
| Last usable                         | `192.168.59.254` |
| Broadcast                           | `192.168.59.255` |

---

## 3. Subnetting method

The `/24` is divided into equal `/27` subnets.

| Calculation                    | Result        |
| ------------------------------ | ------------- |
| Host bits in a `/27`           | 5 (`32 − 27`) |
| Addresses per subnet           | 2^5 = 32      |
| Usable hosts per subnet        | 30            |
| Subnet increment               | 32            |
| Subnets available in the `/24` | 8             |

**Why `/27` rather than smaller or larger prefixes**

* A `/26` (62 hosts) wastes more of a 256-address block on three small groups of devices.
* A `/28` (14 hosts) is tight once extra cameras, sensors or PCs are added during testing.
* A `/27` (30 hosts) fits the Packet Tracer set, matches a small car-wash site, and still leaves five unused `/27`s for later growth.

VLSM (unequal masks) is not required. Office, CCTV and IoT are similar in size in this simulation, so equal `/27`s keep documentation and troubleshooting simpler.

---

## 4. Subnet allocation

| Subnet | Network          | Mask              | CIDR  | VLAN | Role             | Status      |
| ------ | ---------------- | ----------------- | ----- | ---- | ---------------- | ----------- |
| 0      | `192.168.59.0`   | `255.255.255.224` | `/27` | 10   | Office Data      | Allocated   |
| 1      | `192.168.59.32`  | `255.255.255.224` | `/27` | 20   | CCTV             | Allocated   |
| 2      | `192.168.59.64`  | `255.255.255.224` | `/27` | 30   | IoT / Operations | Allocated   |
| 3      | `192.168.59.96`  | `255.255.255.224` | `/27` | —    | Reserved         | Unallocated |
| 4      | `192.168.59.128` | `255.255.255.224` | `/27` | —    | Reserved         | Unallocated |
| 5      | `192.168.59.160` | `255.255.255.224` | `/27` | —    | Reserved         | Unallocated |
| 6      | `192.168.59.192` | `255.255.255.224` | `/27` | —    | Reserved         | Unallocated |
| 7      | `192.168.59.224` | `255.255.255.224` | `/27` | —    | Reserved         | Unallocated |

Reserved space is not assigned a VLAN in Milestone 1. It remains available if the site later adds a guest Wi-Fi, a second operations bay, or a management segment — without renumbering the three live subnets.

---

## 5. VLAN 10 — Office Data (`192.168.59.0/27`)

| Item                   | Value                            |
| ---------------------- | -------------------------------- |
| Network address        | `192.168.59.0`                   |
| Subnet mask            | `255.255.255.224`                |
| Wildcard (for NAT ACL) | `0.0.0.31`                       |
| Default gateway        | `192.168.59.1` (R1 `G0/1.10`)    |
| Usable host range      | `192.168.59.2` – `192.168.59.30` |
| Broadcast              | `192.168.59.31`                  |
| NAT                    | Inside source for PAT            |
| Simulated Internet     | Allowed                          |

| Device  | Interface / port | IPv4 address      | Gateway        |
| ------- | ---------------- | ----------------- | -------------- |
| R1      | `G0/1.10`        | `192.168.59.1/27` | —              |
| PC 1    | SW1 `Fa0/1`      | `192.168.59.2/27` | `192.168.59.1` |
| PC 2    | SW1 `Fa0/2`      | `192.168.59.3/27` | `192.168.59.1` |
| Printer | SW1 `Fa0/3`      | `192.168.59.4/27` | `192.168.59.1` |

SW1 `Fa0/4`–`Fa0/5` are reserved as VLAN 10 access ports with no host assigned yet.

---

## 6. VLAN 20 — CCTV (`192.168.59.32/27`)

| Item               | Value                             |
| ------------------ | --------------------------------- |
| Network address    | `192.168.59.32`                   |
| Subnet mask        | `255.255.255.224`                 |
| Default gateway    | `192.168.59.33` (R1 `G0/1.20`)    |
| Usable host range  | `192.168.59.34` – `192.168.59.62` |
| Broadcast          | `192.168.59.63`                   |
| NAT                | Not used                          |
| Simulated Internet | Denied                            |

| Device   | Interface / port | IPv4 address       | Gateway         |
| -------- | ---------------- | ------------------ | --------------- |
| R1       | `G0/1.20`        | `192.168.59.33/27` | —               |
| Camera 1 | SW1 `Fa0/6`      | `192.168.59.34/27` | `192.168.59.33` |
| Camera 2 | SW1 `Fa0/7`      | `192.168.59.35/27` | `192.168.59.33` |
| NVR      | SW1 `Fa0/8`      | `192.168.59.36/27` | `192.168.59.33` |

SW1 `Fa0/9`–`Fa0/10` are reserved as VLAN 20 access ports. Cameras and the NVR can talk on the same VLAN; they must not reach Office or IoT at Layer 3.

---

## 7. VLAN 30 — IoT / Operations (`192.168.59.64/27`)

| Item               | Value                             |
| ------------------ | --------------------------------- |
| Network address    | `192.168.59.64`                   |
| Subnet mask        | `255.255.255.224`                 |
| Default gateway    | `192.168.59.65` (R1 `G0/1.30`)    |
| Usable host range  | `192.168.59.66` – `192.168.59.94` |
| Broadcast          | `192.168.59.95`                   |
| NAT                | Not used                          |
| Simulated Internet | Denied                            |

| Device         | Interface / port | IPv4 address       | Gateway         |
| -------------- | ---------------- | ------------------ | --------------- |
| R1             | `G0/1.30`        | `192.168.59.65/27` | —               |
| Sensor 1       | SW1 `Fa0/11`     | `192.168.59.66/27` | `192.168.59.65` |
| Sensor 2       | SW1 `Fa0/12`     | `192.168.59.67/27` | `192.168.59.65` |
| IoT Controller | SW1 `Fa0/13`     | `192.168.59.68/27` | `192.168.59.65` |

SW1 `Fa0/14`–`Fa0/15` are reserved as VLAN 30 access ports. This subnet is the CR12 isolated operations segment.

---

## 8. WAN / NAT outside — `203.0.113.0/30`

This prefix is **outside** the assigned client block. It exists only so Packet Tracer can show inside/outside translation.

| Item             | Value             |
| ---------------- | ----------------- |
| Network          | `203.0.113.0/30`  |
| Mask             | `255.255.255.252` |
| Usable addresses | 2                 |
| Broadcast        | `203.0.113.3`     |

| Device     | Interface | Address          | Role                       |
| ---------- | --------- | ---------------- | -------------------------- |
| ISP router | `G0/0`    | `203.0.113.1/30` | Simulated upstream gateway |
| R1         | `G0/0`    | `203.0.113.2/30` | NAT **outside**            |

R1 default route:

```text
ip route 0.0.0.0 0.0.0.0 203.0.113.1
```

An external test host will be placed on a separate simulated ISP LAN during Packet Tracer implementation (exact host IP recorded when the `.pkt` file is built). Public resolver `8.8.8.8` is **not** assumed unless that host is actually configured in Packet Tracer.

### NAT matching (Office only)

```text
access-list 10 permit 192.168.59.0 0.0.0.31
ip nat inside source list 10 interface GigabitEthernet0/0 overload
```

> **ACL numbering clarification:** ACL 10 is reserved for NAT source matching. It is separate from the segmentation ACLs 100, 110 and 120 used to enforce VLAN traffic restrictions. This avoids ambiguity between NAT matching and traffic-segmentation policy.

* `0.0.0.31` matches the 32-address Office `/27` only.
* CCTV (`192.168.59.32/27`) and IoT (`192.168.59.64/27`) are excluded from NAT.
* `G0/1.10` is NAT **inside**; `G0/0` is NAT **outside**.

---

## 9. Master addressing table (submission table)

| Network | Device         | Interface    | IPv4 address    | Mask  | Gateway         | Purpose                     |
| ------- | -------------- | ------------ | --------------- | ----- | --------------- | --------------------------- |
| WAN     | ISP router     | `G0/0`       | `203.0.113.1`   | `/30` | —               | Simulated ISP               |
| WAN     | R1             | `G0/0`       | `203.0.113.2`   | `/30` | `203.0.113.1`   | NAT outside                 |
| VLAN 10 | R1             | `G0/1.10`    | `192.168.59.1`  | `/27` | —               | Office gateway / NAT inside |
| VLAN 10 | PC 1           | `SW1 Fa0/1`  | `192.168.59.2`  | `/27` | `192.168.59.1`  | Office workstation          |
| VLAN 10 | PC 2           | `SW1 Fa0/2`  | `192.168.59.3`  | `/27` | `192.168.59.1`  | Office workstation          |
| VLAN 10 | Printer        | `SW1 Fa0/3`  | `192.168.59.4`  | `/27` | `192.168.59.1`  | Office printer              |
| VLAN 20 | R1             | `G0/1.20`    | `192.168.59.33` | `/27` | —               | CCTV gateway                |
| VLAN 20 | Camera 1       | `SW1 Fa0/6`  | `192.168.59.34` | `/27` | `192.168.59.33` | CCTV                        |
| VLAN 20 | Camera 2       | `SW1 Fa0/7`  | `192.168.59.35` | `/27` | `192.168.59.33` | CCTV                        |
| VLAN 20 | NVR            | `SW1 Fa0/8`  | `192.168.59.36` | `/27` | `192.168.59.33` | Recording                   |
| VLAN 30 | R1             | `G0/1.30`    | `192.168.59.65` | `/27` | —               | IoT gateway                 |
| VLAN 30 | Sensor 1       | `SW1 Fa0/11` | `192.168.59.66` | `/27` | `192.168.59.65` | Operations sensor           |
| VLAN 30 | Sensor 2       | `SW1 Fa0/12` | `192.168.59.67` | `/27` | `192.168.59.65` | Operations sensor           |
| VLAN 30 | IoT Controller | `SW1 Fa0/13` | `192.168.59.68` | `/27` | `192.168.59.65` | Operations controller       |

End devices use static IPv4 in Milestone 1 so addresses stay identical between this document, the diagrams and later tests. DHCP can be added later only if it is needed for demonstration; it is not required by the brief.

---

## 10. Addressing rules used on this project

1. Gateway = first usable address in each subnet (`.1`, `.33`, `.65`).
2. End hosts start at the second usable address.
3. Switch ports are VLAN membership; empty reserved ports do not receive IP addresses.
4. Internal addresses stay inside `192.168.59.0/24`.
5. NAT overload uses R1's outside interface address (`203.0.113.2`).
6. **ACL 10 is reserved for NAT source matching and is separate from segmentation ACLs 100, 110 and 120.**

---

## 11. Related documents

* [`../01_Client_Requirements/01-client-requirements.md`](../01_Client_Requirements/01-client-requirements.md)
* [`../02_Physical_Topology/physical_topology.md`](../02_Physical_Topology/physical_topology.md)
* [`../03_Logical_Topology/logical_topology.md`](../03_Logical_Topology/logical_topology.md)

---

## 12. Document control

| Version | Date       | Author      | Changes                                                                                         |
| ------- | ---------- | ----------- | ----------------------------------------------------------------------------------------------- |
| 1.0     | 2026-08-26 | SITHOLE, TJ | Initial addressing tables                                                                       |
| 1.1     | 2026-08-27 | SITHOLE, TJ | Full Milestone 1 plan: subnet math, reserved `/27`s, NAT wildcard, WAN TEST-NET-3, master table |
| 2.0     | 2026-08-28 | SITHOLE, TJ | Final M1 Lock — consistent with Physical v1.7 and Logical v1.5                                  |
