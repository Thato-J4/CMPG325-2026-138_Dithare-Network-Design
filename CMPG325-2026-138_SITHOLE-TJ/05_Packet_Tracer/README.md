# Packet Tracer Implementation

## Project Information

**Project:** CMPG325 Individual Semester Project
**Project ID:** CMPG325-2026-138
**Client:** Dithare Car Wash & Detailing Group
**Student:** SITHOLE, TJ

---

## Purpose

This folder contains the final Cisco Packet Tracer implementation of the network designed for Dithare Car Wash & Detailing Group.

The Packet Tracer file demonstrates the implemented VLAN structure, router-on-a-stick configuration, inter-network segmentation, simulated WAN connectivity, and the assigned NAT/PAT challenge.

---

## Final Packet Tracer File

**File:** `CMPG325-2026-138_Dithare-Network-Final.pkt`

The final `.pkt` file contains the complete implemented network and can be opened in Cisco Packet Tracer for verification and demonstration.

---

## Network Devices

The final topology includes:

* Cisco 2911 router — R1
* Cisco 2960 switch — SW1
* Cisco 2911 router — ISP
* External-PC
* PC1
* PC2
* Printer
* Camera1
* Camera2
* NVR
* IoT-Controller
* Sensor1
* Sensor2

---

## VLAN and IP Structure

| VLAN    | Purpose          | Network            | Gateway         |
| ------- | ---------------- | ------------------ | --------------- |
| VLAN 10 | Office Data      | `192.168.59.0/27`  | `192.168.59.1`  |
| VLAN 20 | CCTV             | `192.168.59.32/27` | `192.168.59.33` |
| VLAN 30 | IoT / Operations | `192.168.59.64/27` | `192.168.59.65` |

The VLANs are implemented using router-on-a-stick through the trunk connection between R1 and SW1.

---

## Switch Port Allocation

| Switch Ports  | VLAN    | Purpose          |
| ------------- | ------- | ---------------- |
| Fa0/1–Fa0/5   | VLAN 10 | Office Data      |
| Fa0/6–Fa0/10  | VLAN 20 | CCTV             |
| Fa0/11–Fa0/15 | VLAN 30 | IoT / Operations |
| Gi0/1         | Trunk   | R1 connection    |

The trunk allows VLANs 10, 20 and 30.

---

## Router Interfaces

### R1

| Interface | Address            | Function                 |
| --------- | ------------------ | ------------------------ |
| G0/0      | `203.0.113.2/30`   | WAN / NAT outside        |
| G0/1.10   | `192.168.59.1/27`  | Office gateway           |
| G0/1.20   | `192.168.59.33/27` | CCTV gateway             |
| G0/1.30   | `192.168.59.65/27` | IoT / Operations gateway |

### Simulated ISP

| Interface | Address          |
| --------- | ---------------- |
| G0/0      | `203.0.113.1/30` |
| G0/1      | `203.0.113.5/30` |

The external test host uses `203.0.113.6/30` with gateway `203.0.113.5`.

---

## NAT/PAT Implementation

The assigned technical challenge is **NAT**.

PAT overload is configured so that devices in the Office VLAN can access the simulated external network using the R1 WAN interface address.

The NAT configuration uses:

* `G0/1.10` as the NAT inside interface
* `G0/0` as the NAT outside interface
* NAT access list 10 for the Office subnet
* PAT overload using the R1 G0/0 address
* A default route toward the simulated ISP

Only the Office VLAN is included in the NAT/PAT configuration.

CCTV and IoT/Operations traffic is not translated through PAT.

---

## Network Segmentation

The implementation maintains separate VLANs for:

* Office Data
* CCTV
* IoT / Operations

ACLs are used to restrict traffic between the segmented networks.

The implemented ACLs are:

* ACL 100 — Office traffic
* ACL 110 — CCTV traffic
* ACL 120 — IoT / Operations traffic

Testing confirms that the required cross-segment traffic is blocked while permitted local and required connectivity remains functional.

---

## Verification

The implementation was verified using:

* End-to-end ping tests
* VLAN verification
* Trunk verification
* Routing table verification
* NAT translation verification
* NAT statistics
* ACL behaviour testing

Supporting configuration and testing information is available in:

* `06_Configuration/README.md`
* `07_Testing/README.md`
* `09_Evidence/`

---

## Implementation Status

The final Packet Tracer implementation has been completed and tested.

The `.pkt` file in this folder represents the final network implementation used for the Milestone 2 testing and evidence.

