# Client Requirements

**Project ID:** CMPG325-2026-138  
**Client ID:** CLI-138  
**Student:** SITHOLE, TJ (51636654)  
**Assigned Organisation:** Dithare Car Wash & Detailing Group (Potchefstroom)  
**Industry:** Automotive  

---

## 1. Client Background

Dithare Car Wash & Detailing Group operates a car wash and detailing site in Potchefstroom. The site requires a network to support its office operations, physical security system and newly introduced IoT-based operational equipment.

The network must support:

- Day-to-day office operations, including staff PCs, administration and bookings
- A physical security system using CCTV covering areas such as the wash bays, yard and reception
- An operations area using IoT sensors for purposes such as equipment monitoring, water usage and bay occupancy

The project requires a network design that provides appropriate segmentation between office data, CCTV traffic and IoT traffic. The design must use the assigned addressing block `192.168.59.0/24` and implement NAT as the assigned technical challenge.

---

## 2. Assigned Addressing Block

The assigned internal IPv4 address block is:

`192.168.59.0/24`

This address block will be subnetted to provide separate network segments for the required traffic types.

---

## 3. Functional Requirements

| ID | Requirement | Source |
|---|---|---|
| R1 | Office staff devices must have LAN connectivity and access to the simulated external network. | Brief §6–7 |
| R2 | CCTV traffic must be segmented from office data traffic. | Design Constraint, Brief §8 |
| R3 | IoT sensors in the operations area must be placed on their own isolated network segment. | CR12, Brief §10 |
| R4 | Internal private addresses must be translated to an outside/global address for traffic leaving the internal network. | Assigned Challenge — NAT, Brief §9 |
| R5 | The network must be built and demonstrated in Cisco Packet Tracer using the assigned `192.168.59.0/24` address block. | Brief §7 |
| R6 | The design must be testable end-to-end and appropriate evidence must be captured. | Brief §11 |

---

## 4. Design Constraints

| ID | Constraint | Source |
|---|---|---|
| C1 | CCTV traffic must be logically separated from office data traffic. | Design Constraint, Brief §8 |
| C2 | IoT sensors added to the operations area require a dedicated isolated network segment. | CR12, Brief §10 |
| C3 | Only the scope described in the brief should be implemented. Additional services or features should not be introduced unless they are necessary to satisfy the stated requirements. | Brief §6–10 |

### Design Approach

The proposed design will use VLAN segmentation as the primary mechanism for separating Office, CCTV and IoT traffic.

Each traffic type will be assigned to a dedicated VLAN and IP subnet. Inter-VLAN routing will only be implemented where required by the final network design. Where Layer 3 communication could compromise the required segmentation, appropriate access controls may be used.

NAT/PAT will be configured for the Office network to provide access to the simulated external network. CCTV and IoT networks will not be given external access unless required by the project brief.

The final configuration will be validated through testing in Cisco Packet Tracer.

---

## 5. Assigned Technical Challenge

### NAT — Network Address Translation

The assigned technical challenge is **NAT (inside/outside address translation)**.

The router must be configured to:

- Identify the appropriate internal interfaces as **NAT inside**
- Identify the external interface as **NAT outside**
- Translate internal private addresses to an outside/global address for traffic leaving the internal network
- Use PAT/NAT overload where appropriate to allow multiple internal devices to share an outside address
- Demonstrate and verify the NAT configuration using appropriate Cisco IOS commands

The following commands will be used as part of the verification evidence:

```text
show ip nat translations
show ip nat statistics
```

The NAT implementation will be demonstrated using a simulated external network in Cisco Packet Tracer.

---

## 6. Traffic Types Identified

| Segment | Description | Simulated External Access | Required Access to Other Segments |
|---|---|---|---|
| Office Data | Staff PCs, administration, reception and bookings | Yes | As required by the final design |
| CCTV | Security cameras covering areas such as wash bays, yard and reception | No | No |
| IoT / Operations | Sensors monitoring equipment and operational activities | No | No |

**Scope Note:** A separate Management VLAN is not included in the initial design because it is not explicitly required by the project brief. Router and switch management will therefore be performed through the existing network interfaces without creating an additional management subnet.

---

## 7. Design Decisions

### 7.1 VLAN Segmentation

Each major traffic type will be placed into its own VLAN and IP subnet:

- Office Data VLAN
- CCTV VLAN
- IoT / Operations VLAN

This provides logical Layer 2 separation and directly addresses the client's requirement for segmented traffic.

**Requirements addressed:** R2, R3, C1, C2

### 7.2 Router-on-a-Stick

Router-on-a-stick will be considered for inter-VLAN routing because it allows multiple VLANs to use a single physical router interface through 802.1Q trunking.

This approach is suitable for the scale of the simulated client network and avoids introducing unnecessary routing hardware.

Inter-VLAN routing will only provide communication where it is required by the final design.

**Requirements addressed:** R1, R5

### 7.3 Restricted Inter-VLAN Traffic

Although VLANs provide Layer 2 separation, routing between VLANs can permit Layer 3 communication if not controlled.

Therefore, the final configuration will ensure that traffic flows comply with the required segmentation. If necessary, Layer 3 access controls such as ACLs will be used to enforce the required restrictions.

ACLs are considered a supporting mechanism, not the assigned technical challenge.

**Requirements addressed:** C1, C2

### 7.4 PAT / NAT Overload

PAT will be configured on the edge router so that multiple Office devices can access the simulated external network through an outside address.

CCTV and IoT devices will not be configured for external access because the requirements do not identify such access as necessary.

**Requirements addressed:** R1, R4

### 7.5 /27 Subnetting

The assigned 192.168.59.0/24 network will initially be divided into /27 subnets.

Each /27 subnet provides:

- 32 total addresses
- 30 usable host addresses
- 1 network address
- 1 broadcast address

Using /27 subnets provides sufficient addresses for the representative devices required in the Packet Tracer implementation while keeping the addressing scheme simple and easy to document.

**Requirements addressed:** R5, R6

---

## 8. IP Addressing Plan

### 8.1 Subnet Breakdown

| Segment | Network Address | Subnet Mask | CIDR | Usable Hosts | Default Gateway |
|---|---|---|---|---|---|
| Office Data | 192.168.59.0 | 255.255.255.224 | /27 | 30 | 192.168.59.1 |
| CCTV | 192.168.59.32 | 255.255.255.224 | /27 | 30 | 192.168.59.33 |
| IoT / Operations | 192.168.59.64 | 255.255.255.224 | /27 | 30 | 192.168.59.65 |

### 8.2 Remaining Address Space

| Address Range | Status |
|---|---|
| 192.168.59.96 – 192.168.59.127 | Unallocated / available for future expansion |
| 192.168.59.128 – 192.168.59.255 | Available for future expansion |

This leaves a significant portion of the assigned /24 block available for future requirements without changing the existing addressing scheme.

### 8.3 Router Interface Addressing

| Device | Interface | IP Address | Subnet Mask | Connected To |
|---|---|---|---|---|
| Edge Router (R1) | GigabitEthernet0/0 — NAT outside | 203.0.113.2 | 255.255.255.252 | Simulated ISP (`203.0.113.1`) |
| Edge Router (R1) | GigabitEthernet0/1.10 — Office | 192.168.59.1 | 255.255.255.224 | Office VLAN 10 |
| Edge Router (R1) | GigabitEthernet0/1.20 — CCTV | 192.168.59.33 | 255.255.255.224 | CCTV VLAN 20 |
| Edge Router (R1) | GigabitEthernet0/1.30 — IoT | 192.168.59.65 | 255.255.255.224 | IoT VLAN 30 |

The WAN uses documentation prefix `203.0.113.0/30` (TEST-NET-3). It is not taken from the assigned `192.168.59.0/24` block. Full host tables are in `04_IP_Addressing/IP_Addressing_Plan.md`.

### 8.4 Example Device Addressing

**Office**

| Device | IP Address | Subnet Mask | Default Gateway |
|---|---|---|---|
| PC 1 | 192.168.59.2 | 255.255.255.224 | 192.168.59.1 |
| PC 2 | 192.168.59.3 | 255.255.255.224 | 192.168.59.1 |
| Printer | 192.168.59.4 | 255.255.255.224 | 192.168.59.1 |

**CCTV**

| Device | IP Address | Subnet Mask | Default Gateway |
|---|---|---|---|
| Camera 1 | 192.168.59.34 | 255.255.255.224 | 192.168.59.33 |
| Camera 2 | 192.168.59.35 | 255.255.255.224 | 192.168.59.33 |
| NVR | 192.168.59.36 | 255.255.255.224 | 192.168.59.33 |

**IoT / Operations**

| Device | IP Address | Subnet Mask | Default Gateway |
|---|---|---|---|
| Sensor 1 | 192.168.59.66 | 255.255.255.224 | 192.168.59.65 |
| Sensor 2 | 192.168.59.67 | 255.255.255.224 | 192.168.59.65 |
| IoT Controller | 192.168.59.68 | 255.255.255.224 | 192.168.59.65 |

The device quantities above are representative and may be adjusted during Packet Tracer implementation while remaining within the assigned subnets.

---

## 9. Implementation Requirements

The solution must:

| ID | Requirement |
|---|---|
| I1 | Be implemented using Cisco Packet Tracer. |
| I2 | Use appropriate Cisco networking devices such as a router and switches. |
| I3 | Use the assigned 192.168.59.0/24 address block for the internal network. |
| I4 | Include appropriate VLAN configuration and trunking. |
| I5 | Implement the required network segmentation between Office, CCTV and IoT traffic. |
| I6 | Implement NAT/PAT on the edge router. |
| I7 | Provide the connectivity required by the project and be fully testable. |
| I8 | Be saved as a working Cisco Packet Tracer .pkt file. |

---

## 10. Project Success Criteria

The project will be considered successful when:

| ID | Success Criterion |
|---|---|
| S1 | The designed network addresses all identified client requirements (R1–R6). |
| S2 | Office devices have LAN connectivity and simulated external network access. |
| S3 | CCTV traffic is logically separated from Office traffic. |
| S4 | IoT devices are placed in a dedicated isolated network segment. |
| S5 | The assigned 192.168.59.0/24 address block is correctly subnetted and documented. |
| S6 | Required routing and traffic restrictions operate correctly. |
| S7 | NAT/PAT is correctly configured on the edge router. |
| S8 | NAT translations can be verified using `show ip nat translations`. |
| S9 | NAT statistics can be verified using `show ip nat statistics`. |
| S10 | Required connectivity tests are successful. |
| S11 | The Packet Tracer implementation reproduces the documented design. |
| S12 | Project evidence is organised in the GitHub portfolio with meaningful commits. |
| S13 | The final solution can be clearly explained and defended during the video demonstration. |

---

## 11. Requirements Traceability

| Requirement | Design Response | Evidence |
|---|---|---|
| R1: Office LAN + external access | Office VLAN with gateway and NAT/PAT on the edge router | .pkt file, ping tests and external connectivity test |
| R2: CCTV segmentation | Dedicated CCTV VLAN and subnet | VLAN configuration and topology diagram |
| R3: IoT isolation | Dedicated IoT VLAN and subnet | VLAN configuration and topology diagram |
| R4: NAT implementation | PAT configured on the edge router | `show ip nat translations` and `show ip nat statistics` |
| R5: Packet Tracer implementation | Network built using the assigned address block | .pkt file and GitHub repository |
| R6: Testing and evidence | Connectivity testing and NAT verification | Screenshots, test results and GitHub evidence |
| C1: CCTV separation | Dedicated CCTV VLAN with appropriate traffic restrictions | VLAN configuration and connectivity tests |
| C2: IoT isolation | Dedicated IoT VLAN with appropriate traffic restrictions | VLAN configuration and connectivity tests |
| C3: Scope control | Design limited to required Office, CCTV, IoT and NAT functionality | Design documentation and device list |

---

## 12. Design Assumptions

The following assumptions are made only where the project brief does not provide specific implementation details.

| ID | Assumption |
|---|---|
| A1 | The project brief does not specify the exact number of office users, CCTV cameras or IoT sensors. Representative quantities will therefore be used for the Packet Tracer demonstration. |
| A2 | The external network required for NAT testing will be simulated within Cisco Packet Tracer using `203.0.113.0/30`. |
| A3 | Devices will initially use static IPv4 addressing unless the project brief specifically requires DHCP. |
| A4 | An external test host IP on the ISP side will be recorded when the Packet Tracer file is built. |
| A5 | ACLs are required to enforce Layer 3 isolation once router-on-a-stick is in use; they support the constraint and CR12 and are not the assigned technical challenge. |

---

## 13. Summary

The proposed network design for Dithare Car Wash & Detailing Group will:

- Use VLAN segmentation to separate Office, CCTV and IoT traffic.
- Use dedicated IP subnets for each traffic type.
- Use router-on-a-stick where inter-VLAN routing is required.
- Restrict traffic between segments where necessary to maintain the required isolation.
- Configure NAT/PAT on the edge router for Office access to the simulated external network.
- Use the assigned 192.168.59.0/24 address block.
- Use /27 subnets for the initial network segments.
- Be implemented and tested in Cisco Packet Tracer.
- Produce evidence for the GitHub portfolio, Packet Tracer project and final demonstration.

The implementation and testing stages will verify whether the proposed design satisfies all requirements and constraints.

---

## 14. Document Control

| Version | Date | Author | Changes |
|---|---|---|---|
| 1.0 | 2026-08-25 | SITHOLE, TJ | Initial client requirements document for Milestone 1 |
| 1.1 | 2026-08-25 | SITHOLE, TJ | Revised requirements, design constraints and addressing plan |
| 1.2 | 2026-08-25 | SITHOLE, TJ | Added requirements traceability and clarified project scope |
| 1.3 | 2026-08-25 | SITHOLE, TJ | Final Milestone 1 version; clarified segmentation, NAT/PAT, routing and assumptions |
| 1.4 | 2026-08-28 | SITHOLE, TJ | Aligned WAN/NAT outside addressing with the IP plan (`203.0.113.0/30`) |
