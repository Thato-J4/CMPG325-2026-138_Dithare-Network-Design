# Testing and Verification

## CMPG325 Individual Semester Project

**Project ID:** CMPG325-2026-138
**Client:** Dithare Car Wash & Detailing Group
**Student:** SITHOLE, TJ
**Milestone:** Milestone 2 – Client Implementation Review
**Final Verification Date:** 1 October 2026

---

## 1. Purpose

Testing was performed to verify that the implemented network operates according to the approved design.

The testing covered:

* Internal connectivity
* Inter-VLAN traffic restrictions
* IoT/Operations isolation
* External connectivity
* NAT/PAT operation
* ACL operation
* Router and switch configuration status

The final verification was performed after correcting the relevant end-device addressing and confirming the final Packet Tracer implementation.

Supporting screenshots are stored in the `09_Evidence/` directory.

---

## 2. Internal Office Connectivity

### PC1 → PC2

**Source:** PC1 `192.168.59.2`
**Destination:** PC2 `192.168.59.3`

Result:

```text
Packets: Sent = 4, Received = 4, Lost = 0 (0% loss)
```

This confirms connectivity between two Office VLAN devices.

---

### PC1 → Printer

**Source:** PC1 `192.168.59.2`
**Destination:** Printer `192.168.59.4`

Result:

```text
Packets: Sent = 4, Received = 4, Lost = 0 (0% loss)
```

This confirms connectivity between the Office workstation and printer.

---

## 3. Office Gateway Connectivity

### PC1 → R1 VLAN 10 Gateway

**Source:** PC1 `192.168.59.2`
**Destination:** R1 `192.168.59.1`

Result:

```text
Packets: Sent = 4, Received = 4, Lost = 0 (0% loss)
```

This confirms that the Office host can reach its default gateway.

---

## 4. CCTV Connectivity

### NVR → CCTV Gateway

**Source:** NVR `192.168.59.36`
**Destination:** R1 `192.168.59.33`

Result:

```text
Packets: Sent = 4, Received = 4, Lost = 0 (0% loss)
```

This confirms that the NVR can communicate with its VLAN gateway.

---

### NVR → Camera1

**Source:** NVR `192.168.59.36`
**Destination:** Camera1 `192.168.59.34`

Result:

```text
Packets: Sent = 4, Received = 4, Lost = 0 (0% loss)
```

This confirms internal connectivity between the NVR and Camera1 within the CCTV VLAN.

---

### NVR → Camera2

**Source:** NVR `192.168.59.36`
**Destination:** Camera2 `192.168.59.35`

Result:

```text
Packets: Sent = 4, Received = 4, Lost = 0 (0% loss)
```

This confirms internal connectivity between the NVR and Camera2 within the CCTV VLAN.

---

## 5. IoT/Operations Gateway Connectivity

### R1 → IoT Controller

**Source:** R1
**Destination:** IoT Controller `192.168.59.68`

The first test produced an initial packet loss while the device was being resolved. The test was immediately repeated.

Final repeat result:

```text
Packets: Sent = 5, Received = 5, Lost = 0 (0% loss)
```

This confirms that the IoT Controller is reachable through the VLAN 30 gateway.

---

## 6. Inter-VLAN Traffic Restriction

The network design requires CCTV and IoT/Operations traffic to remain separated from the Office network.

### PC1 → Camera1

**Source:** PC1 `192.168.59.2`
**Destination:** Camera1 `192.168.59.34`

Result:

```text
Packets: Sent = 4, Received = 0, Lost = 4 (100% loss)
```

The packets were rejected by the Office VLAN traffic restrictions.

This confirms that Office traffic cannot directly access the CCTV network.

---

### PC1 → IoT Controller

**Source:** PC1 `192.168.59.2`
**Destination:** IoT Controller `192.168.59.68`

Result:

```text
Packets: Sent = 4, Received = 0, Lost = 4 (100% loss)
```

The packets were rejected by the Office VLAN traffic restrictions.

This confirms that Office traffic cannot directly access the IoT/Operations network.

---

## 7. CCTV Isolation Testing

The NVR was tested against devices outside the CCTV VLAN.

### NVR → PC1

**Source:** NVR `192.168.59.36`
**Destination:** PC1 `192.168.59.2`

Result:

```text
Packets: Sent = 4, Received = 0, Lost = 4 (100% loss)
```

This confirms that CCTV traffic is prevented from reaching the Office network.

---

### NVR → IoT Controller

**Source:** NVR `192.168.59.36`
**Destination:** IoT Controller `192.168.59.68`

Result:

```text
Packets: Sent = 4, Received = 0, Lost = 4 (100% loss)
```

This confirms that CCTV traffic is prevented from reaching the IoT/Operations network.

---

### NVR → External-PC

**Source:** NVR `192.168.59.36`
**Destination:** External-PC `203.0.113.6`

Result:

```text
Packets: Sent = 4, Received = 0, Lost = 4 (100% loss)
```

This confirms that CCTV traffic is restricted from reaching the external network.

These tests confirm that CCTV traffic is restricted from the Office, IoT/Operations, and external networks.

---

## 8. IoT/Operations Isolation Testing

A temporary test host was connected to SW1 Fa0/14 in VLAN 30 during testing.

The temporary host used:

```text
IP Address: 192.168.59.69
Subnet Mask: 255.255.255.224
Default Gateway: 192.168.59.65
```

The host was used only for verification and was removed after testing.

### IoT VLAN → Office

**Source:** Temporary VLAN 30 test host
**Destination:** PC1 `192.168.59.2`

Result:

```text
Packets: Sent = 4, Received = 0, Lost = 4 (100% loss)
```

### IoT VLAN → CCTV

**Source:** Temporary VLAN 30 test host
**Destination:** Camera1 `192.168.59.34`

Result:

```text
Packets: Sent = 4, Received = 0, Lost = 4 (100% loss)
```

### IoT VLAN → External Network

**Source:** Temporary VLAN 30 test host
**Destination:** External-PC `203.0.113.6`

Result:

```text
Packets: Sent = 4, Received = 0, Lost = 4 (100% loss)
```

These tests confirm that the IoT/Operations VLAN is isolated from the Office, CCTV, and external networks.

---

## 9. External Connectivity

### PC1 → External-PC

**Source:** PC1 `192.168.59.2`
**Destination:** External-PC `203.0.113.6`

An earlier test produced:

```text
Packets: Sent = 4, Received = 3, Lost = 1 (25% loss)
```

This was followed by an immediate repeat test.

The final clean verification produced:

```text
Packets: Sent = 4, Received = 4, Lost = 0 (0% loss)
Average = 0 ms
```

The successful final test confirms that Office traffic can reach the simulated external network.

---

## 10. NAT/PAT Verification

NAT/PAT was verified by generating external traffic from PC1 to External-PC.

### Test Path

```text
PC1 192.168.59.2
        |
        | ICMP
        v
R1 G0/1.10
        |
      PAT
        |
R1 G0/0 203.0.113.2
        |
        v
External-PC 203.0.113.6
```

The Office VLAN is configured as the NAT inside network, while R1 `GigabitEthernet0/0` is configured as the NAT outside interface.

After the successful ping, the following command was executed on R1:

```text
show ip nat translations
```

The final test produced four dynamic ICMP translations:

```text
icmp  203.0.113.2:29  192.168.59.2:29  203.0.113.6:29  203.0.113.6:29
icmp  203.0.113.2:30  192.168.59.2:30  203.0.113.6:30  203.0.113.6:30
icmp  203.0.113.2:31  192.168.59.2:31  203.0.113.6:31  203.0.113.6:31
icmp  203.0.113.2:32  192.168.59.2:32  203.0.113.6:32  203.0.113.6:32
```

This demonstrates that the internal Office address `192.168.59.2` was translated to R1's outside address `203.0.113.2`.

The translation table therefore provides direct evidence that PAT was operating on Office traffic.

---

## 11. NAT Statistics

The following command was used:

```text
show ip nat statistics
```

During the final verification, the NAT statistics showed:

```text
Total translations: 4 (0 static, 4 dynamic, 4 extended)
Outside Interfaces: GigabitEthernet0/0
Inside Interfaces: GigabitEthernet0/1.10
```

The statistics also recorded NAT hits during testing.

The presence of dynamic and extended translations confirms that PAT was operating.

NAT translations can expire after the traffic stops. Therefore, an empty translation table after some time does not indicate that the configuration has failed. A fresh traffic test recreates the dynamic entries.

---

## 12. ACL Verification

The ACL counters were also checked using:

```text
show access-lists 100
show access-lists 110
show access-lists 120
```

### ACL 110

After the CCTV isolation tests, ACL 110 showed matches on its deny statements:

```text
deny CCTV → Office       4 matches
deny CCTV → IoT          4 matches
deny CCTV → external     4 matches
```

This provides evidence that the CCTV restrictions were actively processing the test traffic.

### ACL 120

After the IoT/Operations isolation tests, ACL 120 showed matches on its deny statements:

```text
deny IoT → Office       4 matches
deny IoT → CCTV         4 matches
deny IoT → external     4 matches
```

This confirms that the IoT/Operations isolation rules were actively processing the test traffic.

ACL 100 was also used to enforce the Office restrictions tested from PC1 to the CCTV and IoT/Operations networks.

---

## 13. Router and Switch Verification

The following commands were used during implementation and testing:

```text
show ip interface brief
show ip route
show vlan brief
show interfaces trunk
show access-lists
show ip nat translations
show ip nat statistics
```

The results confirmed that:

* R1 interfaces and VLAN subinterfaces were operational.
* VLAN 10, VLAN 20, and VLAN 30 were configured.
* The R1-SW1 link was operating as an 802.1Q trunk.
* The three internal networks appeared as directly connected routes.
* The default route pointed toward the simulated ISP.
* NAT/PAT was operating on Office traffic.
* ACLs were processing restricted traffic.

---

## 14. Test Summary

| Test                      | Expected Result     | Actual Result                 |
| ------------------------- | ------------------- | ----------------------------- |
| PC1 → PC2                 | Allowed             | Pass — 4/4, 0% loss           |
| PC1 → Printer             | Allowed             | Pass — 4/4, 0% loss           |
| PC1 → Office gateway      | Allowed             | Pass — 4/4, 0% loss           |
| NVR → CCTV gateway        | Allowed             | Pass — 4/4, 0% loss           |
| NVR → Camera1             | Allowed             | Pass — 4/4, 0% loss           |
| NVR → Camera2             | Allowed             | Pass — 4/4, 0% loss           |
| R1 → IoT Controller       | Allowed             | Pass — 5/5, 0% loss on repeat |
| PC1 → Camera1             | Blocked             | Pass                          |
| PC1 → IoT Controller      | Blocked             | Pass                          |
| NVR → PC1                 | Blocked             | Pass                          |
| NVR → IoT Controller      | Blocked             | Pass                          |
| NVR → External-PC         | Blocked             | Pass                          |
| IoT → Office              | Blocked             | Pass                          |
| IoT → CCTV                | Blocked             | Pass                          |
| IoT → External-PC         | Blocked             | Pass                          |
| PC1 → External-PC         | Allowed through PAT | Pass — 4/4, 0% loss           |
| NAT translation generated | Required            | Pass                          |
| Dynamic NAT translations  | Required            | Pass — 4 observed             |
| NAT inside interface      | G0/1.10             | Verified                      |
| NAT outside interface     | G0/0                | Verified                      |
| ACL 110 deny counters     | Required            | Pass                          |
| ACL 120 deny counters     | Required            | Pass                          |

---

## 15. Evidence References

Supporting screenshots for the final verification are stored in the `09_Evidence/` directory:

| Evidence              | File                            |
| --------------------- | ------------------------------- |
| Final topology        | `01_final_topology.png`         |
| VLAN configuration    | `02_vlan_configuration.png`     |
| Trunk configuration   | `03_trunk_configuration.png`    |
| Office connectivity   | `04_office_connectivity.png`    |
| CCTV connectivity     | `05_cctv_connectivity.png`      |
| Office → CCTV blocked | `06_office_to_cctv_blocked.png` |
| Office → IoT blocked  | `07_office_to_iot_blocked.png`  |
| CCTV isolation        | `08_cctv_isolation.png`         |
| NAT translations      | `09_nat_translations.png`       |
| NAT statistics        | `10_nat_statistics.png`         |

These screenshots provide supporting evidence for the configuration and testing results documented in this README.

---

## 16. Conclusion

The final testing confirms that the implemented network performs the required routing, segmentation, external connectivity, and NAT/PAT functions.

The Office VLAN can communicate internally and access the simulated external network through PAT.

The CCTV VLAN can communicate with its own devices while traffic to the Office, IoT/Operations, and external networks is restricted according to the network design.

The IoT/Operations VLAN is isolated from the Office, CCTV, and external networks.

The successful NAT translation table and NAT statistics provide direct evidence that the assigned NAT/PAT challenge was implemented and verified.

The ACL match counters provide additional evidence that the configured traffic restrictions were actively applied during testing.

The final Packet Tracer implementation and supporting evidence are stored in the project GitHub repository.
