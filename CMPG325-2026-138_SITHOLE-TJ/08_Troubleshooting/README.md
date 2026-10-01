# Troubleshooting and Issue Resolution

## CMPG325 Individual Semester Project

**Project ID:** CMPG325-2026-138
**Client:** Dithare Car Wash & Detailing Group
**Student:** SITHOLE, TJ
**Milestone:** Milestone 2 – Client Implementation Review

---

## 1. Purpose

This section records the significant troubleshooting performed during the implementation and verification of the Cisco Packet Tracer network.

The purpose is to document the observed problem, investigation, corrective action, and final verification result.

Only issues encountered during the actual implementation and testing process are recorded.

---

## 2. Camera2 Addressing Conflict

### Problem

Camera2 initially experienced an addressing/interface conflict because an address was being associated with the incorrect interface configuration.

This prevented the device from operating correctly with the intended CCTV network configuration.

### Investigation

The device interfaces were checked to determine which interface was being used for the wired CCTV connection.

The intended CCTV addressing was:

```text
Network: 192.168.59.32/27
Gateway: 192.168.59.33
Camera1: 192.168.59.34
Camera2: 192.168.59.35
NVR:     192.168.59.36
```

### Resolution

The conflicting wireless configuration on Camera2 was cleared and the intended address was assigned to its wired `GigabitEthernet3` interface:

```text
IP Address: 192.168.59.35
Subnet Mask: 255.255.255.224
Default Gateway: 192.168.59.33
```

### Verification

Camera2 was subsequently reachable from the NVR:

```text
NVR → Camera2
Packets: Sent = 4, Received = 4, Lost = 0 (0% loss)
```

The CCTV segment was therefore operating correctly.

---

## 3. IoT Controller Connectivity Verification

### Problem

The IoT Controller and its wireless sensors required verification after being placed in VLAN 30.

The controller was connected to SW1 using a wired connection while Sensor1 and Sensor2 communicated wirelessly with the controller.

### Investigation

The device addressing and switch port assignment were checked.

The intended configuration was:

```text
IoT Controller: 192.168.59.68/27
Gateway:        192.168.59.65
Switch Port:    Fa0/13
VLAN:           30
```

Sensor1 and Sensor2 were assigned:

```text
Sensor1: 192.168.59.66/27
Sensor2: 192.168.59.67/27
Gateway: 192.168.59.65
```

### Initial Test

The first R1 → IoT Controller test produced an initial packet loss while connectivity was being resolved.

The test was immediately repeated.

### Resolution and Verification

The repeated test produced:

```text
Packets: Sent = 5, Received = 5, Lost = 0 (0% loss)
```

The IoT Controller was therefore confirmed to be reachable through the VLAN 30 gateway.

No change to the VLAN design was required.

---

## 4. Temporary VLAN 30 Test Host for ACL Verification

### Purpose

The IoT/Operations ACL needed to be tested from an actual VLAN 30 host.

The IoT Controller itself was not used as the sole source for all isolation tests, so a temporary test host was connected to SW1 `Fa0/14`.

### Temporary Configuration

```text
IP Address:      192.168.59.69
Subnet Mask:     255.255.255.224
Default Gateway: 192.168.59.65
VLAN:            30
Switch Port:     Fa0/14
```

### Tests Performed

The temporary host was used to test that VLAN 30 traffic was blocked from:

* Office — PC1 `192.168.59.2`
* CCTV — Camera1 `192.168.59.34`
* External network — External-PC `203.0.113.6`

Each test produced:

```text
Packets: Sent = 4, Received = 0, Lost = 4 (100% loss)
```

These results confirmed that ACL 120 was enforcing the intended IoT/Operations isolation.

### Cleanup

After the ACL tests were completed, the temporary test host was removed.

Fa0/14 was therefore left unused in the final topology.

The temporary host was not part of the final network design.

---

## 5. Initial External Connectivity Test

### Problem

During the first PC1 → External-PC test, the result was:

```text
Packets: Sent = 4, Received = 3, Lost = 1 (25% loss)
```

This occurred during the initial external connectivity verification.

### Investigation

The NAT configuration and network path were checked.

The relevant path was:

```text
PC1
192.168.59.2
   |
   v
R1 G0/1.10
   |
  PAT
   |
R1 G0/0
203.0.113.2
   |
   v
ISP
   |
   v
External-PC
203.0.113.6
```

The NAT configuration was present and the external network addressing was correct.

### Resolution

The same test was immediately repeated after the initial connectivity had been established.

The repeat test produced:

```text
Packets: Sent = 4, Received = 4, Lost = 0 (0% loss)
Average = 0 ms
```

The final clean result confirmed that the Office host could reach the simulated external network.

---

## 6. NAT Translation Entries Expiring

### Observation

During NAT testing, dynamic NAT translations were present immediately after generating traffic.

However, after a period without traffic, the translation table could become empty.

### Investigation

NAT/PAT translations are dynamically created when matching traffic is generated and can expire after the traffic stops.

Therefore, the following result after waiting did not by itself indicate a configuration failure:

```text
Total translations: 0
```

### Resolution

A fresh external connectivity test was generated from PC1 before collecting NAT evidence.

The command:

```text
show ip nat translations
```

then displayed dynamic ICMP translations.

The final captured evidence showed four dynamic translations using R1's outside address:

```text
203.0.113.2
```

for traffic originating from:

```text
192.168.59.2
```

### Verification

The NAT statistics also showed:

```text
Total translations: 4
0 static
4 dynamic
4 extended
```

with:

```text
Outside Interface: GigabitEthernet0/0
Inside Interface:  GigabitEthernet0/1.10
```

This confirmed that PAT was operating correctly.

---

## 7. ACL Verification During Troubleshooting

The segmentation ACLs were verified after the connectivity tests.

The implemented ACLs were:

```text
ACL 100 — Office
ACL 110 — CCTV
ACL 120 — IoT/Operations
```

The ACL counters were checked using:

```text
show access-lists 100
show access-lists 110
show access-lists 120
```

The deny counters increased after the corresponding isolation tests.

This provided additional confirmation that the blocked connectivity was caused by the intended ACL rules rather than an unrelated physical or addressing problem.

---

## 8. Final Troubleshooting Verification

After the identified issues were resolved, the following areas were rechecked:

### Switch

```text
show vlan brief
show interfaces trunk
show mac address-table dynamic
```

Confirmed:

* VLAN 10, 20 and 30 configured
* Gi0/1 operating as an 802.1Q trunk
* Required devices appearing on their expected access ports

### Router

```text
show ip interface brief
show ip route
show access-lists
show ip nat translations
show ip nat statistics
```

Confirmed:

* R1 interfaces operational
* VLAN subinterfaces operational
* Required connected routes present
* Default route toward the ISP present
* ACLs applied
* NAT/PAT operating on Office traffic

### End-to-End Testing

Final testing confirmed:

* Office internal connectivity — successful
* CCTV internal connectivity — successful
* IoT Controller connectivity — successful
* Office → CCTV — blocked
* Office → IoT — blocked
* CCTV → Office — blocked
* CCTV → IoT — blocked
* CCTV → external network — blocked
* IoT → Office — blocked
* IoT → CCTV — blocked
* IoT → external network — blocked
* Office → simulated external network through PAT — successful

---

## 9. Troubleshooting Summary

| Issue                                            | Action Taken                                                                        | Final Result                         |
| ------------------------------------------------ | ----------------------------------------------------------------------------------- | ------------------------------------ |
| Camera2 interface/address conflict               | Corrected wired interface addressing and cleared conflicting wireless configuration | CCTV connectivity successful         |
| Initial IoT Controller connectivity test         | Repeated test after initial resolution                                              | 5/5 successful                       |
| Need to verify ACL 120 from VLAN 30              | Added temporary test host on Fa0/14                                                 | All required isolation tests blocked |
| Temporary VLAN 30 test host                      | Removed after testing                                                               | Not part of final topology           |
| Initial PC1 → External-PC test                   | Repeated test after initial connectivity                                            | 4/4 successful                       |
| NAT translations no longer visible after waiting | Generated fresh traffic before verification                                         | Dynamic PAT translations observed    |
| ACL behaviour verification                       | Checked ACL counters after isolation tests                                          | Deny counters confirmed              |

---

## 10. Conclusion

The troubleshooting process identified and resolved configuration and verification issues without changing the approved network design.

The final implementation was re-tested after the corrections and confirmed to provide the required VLAN connectivity, network segmentation, IoT/Operations isolation, simulated external connectivity, and NAT/PAT functionality.

The troubleshooting process also provided supporting evidence that the final results were obtained from the configured network behaviour rather than from assumptions about the design.
