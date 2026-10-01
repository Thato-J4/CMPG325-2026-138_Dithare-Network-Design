# CMPG325-2026-138 — Dithare Car Wash & Detailing Group

**Module:** CMPG 325 Computer Networks
**Project ID:** CMPG325-2026-138
**Client ID:** CLI-138
**Student:** SITHOLE, TJ (51636654)
**Assigned organisation:** Dithare Car Wash & Detailing Group (Potchefstroom)
**Industry:** Automotive

This repository is the individual GitHub portfolio of evidence for the CMPG 325 semester project. It records client analysis, network design, implementation, testing and reflection as the project progresses.

---

## Project in brief

Dithare Car Wash & Detailing Group needs a working site network that supports office operations, a CCTV security system and IoT sensors in the operations area.

| Item                         | Assigned value                                                         |
| ---------------------------- | ---------------------------------------------------------------------- |
| Internal addressing block    | `192.168.59.0/24`                                                      |
| Design constraint            | CCTV traffic must be segmented from office data traffic                |
| Change request               | **CR12** — IoT sensors in the operations area need an isolated segment |
| Assigned technical challenge | **NAT (inside/outside address translation)**                           |
| Simulation platform          | Cisco Packet Tracer                                                    |

The design uses three VLANs on a single access switch, router-on-a-stick on an edge router, ACL-enforced isolation between segments, and PAT/NAT overload for Office traffic only.

---

## Milestone 1 — Client Design Review (28 August 2026)

| Deliverable                  | Location                                                                                                                                                                                                |
| ---------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 1. Client requirements       | [`01_Client_Requirements/01-client-requirements.md`](01_Client_Requirements/01-client-requirements.md)                                                                                                  |
| 2. Physical topology         | [`02_Physical_Topology/physical_topology.md`](02_Physical_Topology/physical_topology.md) · [diagram](02_Physical_Topology/physical_topology.png) · [source](02_Physical_Topology/physical_topology.svg) |
| 3. Logical topology          | [`03_Logical_Topology/logical_topology.md`](03_Logical_Topology/logical_topology.md) · [diagram](03_Logical_Topology/logical_topology.png) · [source](03_Logical_Topology/logical_topology.svg)         |
| 4. IP addressing plan        | [`04_IP_Addressing/IP_Addressing_Plan.md`](04_IP_Addressing/IP_Addressing_Plan.md)                                                                                                                      |
| 5. Initial GitHub repository | This repository                                                                                                                                                                                         |

The original Milestone 1 documentation is retained as part of the project record.

---

## Design at a glance

```text
Simulated ISP  203.0.113.0/30
        |
   R1 G0/0  NAT outside  (203.0.113.2)
        |
       R1   router-on-a-stick
        |
   R1 G0/1 — SW1 Gi0/1   802.1Q trunk
        |
   +----+----+----+
   |         |         |
VLAN 10   VLAN 20    VLAN 30
Office    CCTV       IoT / Operations
192.168.59.0/27   .32/27    .64/27
NAT/PAT yes       no        no
Internet yes      no        no
```

**Why this design**

* **VLAN 10 Office** — staff PCs and printer need LAN connectivity and simulated Internet access (NAT/PAT).
* **VLAN 20 CCTV** — cameras and NVR are kept off the office data VLAN (design constraint).
* **VLAN 30 IoT / Operations** — sensors and controller sit on a dedicated isolated segment (CR12).
* **NAT inside/outside** — only VLAN 10 is a NAT inside source; G0/0 is NAT outside.

---

## Milestone 2 — Client Implementation Review (2 October 2026)

Milestone 2 focused on implementing the approved network design in Cisco Packet Tracer, configuring the assigned NAT/PAT challenge, and verifying the resulting network behaviour.

| Deliverable                         | Location                                                                                                                     |
| ----------------------------------- | ---------------------------------------------------------------------------------------------------------------------------- |
| Final Packet Tracer implementation  | [`05_Packet_Tracer/CMPG325-2026-138_Dithare-Network-Final.pkt`](05_Packet_Tracer/CMPG325-2026-138_Dithare-Network-Final.pkt) |
| Packet Tracer documentation         | [`05_Packet_Tracer/README.md`](05_Packet_Tracer/README.md)                                                                   |
| Configuration documentation         | [`06_Configuration/README.md`](06_Configuration/README.md)                                                                   |
| Testing documentation               | [`07_Testing/README.md`](07_Testing/README.md)                                                                               |
| Testing and implementation evidence | [`09_Evidence/`](09_Evidence/)                                                                                               |
| Troubleshooting                     | [`08_Troubleshooting/`](08_Troubleshooting/)                                                                                 |

The implemented network was tested for internal connectivity, VLAN segmentation, required cross-segment restrictions, simulated external connectivity and NAT/PAT operation.

NAT/PAT was verified using:

* `show ip nat translations`
* `show ip nat statistics`
* End-to-end connectivity testing

The final Packet Tracer file contains the working implementation used during Milestone 2 verification.

---

## Repository structure

```text
CMPG325-2026-138_SITHOLE-TJ/
├── README.md
├── 01_Client_Requirements/
├── 02_Physical_Topology/
├── 03_Logical_Topology/
├── 04_IP_Addressing/
├── 05_Packet_Tracer/          # Milestone 2 — .pkt file
├── 06_Configuration/          # Milestone 2 — device configs
├── 07_Testing/                # Milestone 2 — test results
├── 08_Troubleshooting/        # Troubleshooting records
├── 09_Evidence/               # Milestone 2 — screenshots/evidence
├── 10_Technical_Report/       # Final submission
└── 11_Video/                  # Final submission
```

The `10_Technical_Report/` and `11_Video/` folders are reserved for the final submission material.

---

## Project timeline

| Milestone            | Date               | Focus                                                     |
| -------------------- | ------------------ | --------------------------------------------------------- |
| Project commencement | 14 August 2026     | Client brief and analysis                                 |
| **Milestone 1**      | **28 August 2026** | Client design review                                      |
| **Milestone 2**      | **2 October 2026** | Packet Tracer build, NAT, testing                         |
| Final submission     | 16 October 2026    | `.pkt` file, GitHub portfolio, report, 15–20 minute video |

Official CMPG 325 dates are used. No alternative deadlines are applied.

---

## Academic integrity

This is individual work for student number **51636654**. Configurations, diagrams and documentation in this repository were produced for CLI-138 only.
