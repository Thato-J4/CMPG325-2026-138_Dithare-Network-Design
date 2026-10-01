# Network Configuration

## CMPG325 Individual Semester Project

**Project ID:** CMPG325-2026-138
**Client:** Dithare Car Wash & Detailing Group
**Student:** SITHOLE, TJ
**Milestone:** Milestone 2 – Client Implementation Review

---

## 1. Purpose

This section documents the main configurations applied to the Cisco devices in the implemented Packet Tracer network.

The configuration follows the approved Milestone 1 network design and implements VLAN segmentation, inter-VLAN routing, NAT/PAT, traffic restrictions, and connectivity to the simulated external network.

---

## 2. R1 Router Configuration

R1 is the main internal router. It performs:

* Inter-VLAN routing using router-on-a-stick
* NAT/PAT for Office traffic
* Default routing toward the simulated ISP
* Traffic filtering using inbound ACLs

### 2.1 WAN Interface

R1 `GigabitEthernet0/0` connects to the simulated ISP.

```text
interface GigabitEthernet0/0
 ip address 203.0.113.2 255.255.255.252
 ip nat outside
 no shutdown
```

The interface is configured as the NAT outside interface.

---

## 3. Router-on-a-Stick Configuration

R1 `GigabitEthernet0/1` connects to SW1 using an 802.1Q trunk.

The physical interface does not have an IP address. VLAN gateways are configured using subinterfaces.

### VLAN 10 – Office Data

```text
interface GigabitEthernet0/1.10
 encapsulation dot1Q 10
 ip address 192.168.59.1 255.255.255.224
 ip nat inside
```

Gateway:

`192.168.59.1`

Network:

`192.168.59.0/27`

---

### VLAN 20 – CCTV

```text
interface GigabitEthernet0/1.20
 encapsulation dot1Q 20
 ip address 192.168.59.33 255.255.255.224
```

Gateway:

`192.168.59.33`

Network:

`192.168.59.32/27`

This VLAN is not configured as a NAT inside interface.

---

### VLAN 30 – IoT/Operations

```text
interface GigabitEthernet0/1.30
 encapsulation dot1Q 30
 ip address 192.168.59.65 255.255.255.224
```

Gateway:

`192.168.59.65`

Network:

`192.168.59.64/27`

This VLAN is not configured as a NAT inside interface.

---

## 4. Default Route

R1 uses a static default route toward the simulated ISP:

```text
ip route 0.0.0.0 0.0.0.0 203.0.113.1
```

This allows traffic destined for external networks to be forwarded to the ISP.

---

## 5. NAT/PAT Configuration

NAT/PAT is the assigned networking challenge for this project.

Only the Office VLAN is permitted to use NAT/PAT.

### NAT Source ACL

```text
access-list 10 permit 192.168.59.0 0.0.0.31
```

This matches the Office VLAN only.

The CCTV and IoT/Operations networks are therefore excluded from NAT.

### PAT Overload

```text
ip nat inside source list 10 interface GigabitEthernet0/0 overload
```

This translates multiple Office hosts using the single outside address of R1:

`203.0.113.2`

PAT overload allows multiple internal Office devices to share the same outside address when communicating with the simulated external network.

---

## 6. Switch VLAN Configuration

SW1 is a Cisco 2960 switch.

The following VLANs are configured:

| VLAN | Name           | Network            | Purpose                     |
| ---- | -------------- | ------------------ | --------------------------- |
| 10   | Office_Data    | `192.168.59.0/27`  | Office devices              |
| 20   | CCTV           | `192.168.59.32/27` | CCTV devices                |
| 30   | IoT_Operations | `192.168.59.64/27` | CR12 IoT/operations devices |

No Management VLAN is used in the final implementation.

---

## 7. SW1 Trunk Configuration

SW1 `GigabitEthernet0/1` connects to R1 `GigabitEthernet0/1`.

The link operates as an 802.1Q trunk and carries VLANs 10, 20, and 30.

The allowed VLANs are:

```text
10,20,30
```

This provides the connection required for router-on-a-stick inter-VLAN routing.

---

## 8. SW1 Access Port Allocation

### Office – VLAN 10

```text
Fa0/1 - Fa0/5
```

These ports are assigned to VLAN 10.

Current devices include:

* PC1
* PC2
* Printer

### CCTV – VLAN 20

```text
Fa0/6 - Fa0/10
```

These ports are assigned to VLAN 20.

Current devices include:

* Camera1
* Camera2
* NVR

### IoT/Operations – VLAN 30

```text
Fa0/11 - Fa0/15
```

These ports are assigned to VLAN 30.

The IoT Controller is connected to:

```text
Fa0/13
```

Sensor1 and Sensor2 connect wirelessly to the IoT Controller.

---

## 9. Traffic Restriction ACLs

Inbound ACLs are applied to the VLAN subinterfaces on R1.

### ACL 100 – Office

ACL 100 restricts Office traffic from reaching the CCTV and IoT/Operations networks.

```text
access-list 100 deny ip 192.168.59.0 0.0.0.31 192.168.59.32 0.0.0.31
access-list 100 deny ip 192.168.59.0 0.0.0.31 192.168.59.64 0.0.0.31
access-list 100 permit ip 192.168.59.0 0.0.0.31 any
```

Applied inbound to:

```text
GigabitEthernet0/1.10
```

---

### ACL 110 – CCTV

ACL 110 allows CCTV devices to communicate within their own VLAN while blocking access to the Office, IoT/Operations, and external networks.

Applied inbound to:

```text
GigabitEthernet0/1.20
```

---

### ACL 120 – IoT/Operations

ACL 120 allows IoT/Operations devices to communicate within their own VLAN while blocking access to the Office, CCTV, and external networks.

Applied inbound to:

```text
GigabitEthernet0/1.30
```

---

## 10. ACL and NAT Separation

The NAT matching ACL and traffic-filtering ACLs serve different purposes.

| ACL     | Purpose                                        |
| ------- | ---------------------------------------------- |
| ACL 10  | Identifies Office traffic eligible for NAT/PAT |
| ACL 100 | Restricts Office traffic                       |
| ACL 110 | Restricts CCTV traffic                         |
| ACL 120 | Restricts IoT/Operations traffic               |

ACL 10 is therefore not used as a traffic-security ACL.

---

## 11. Simulated External Network

The simulated external network uses:

| Device      | Interface | Address          |
| ----------- | --------- | ---------------- |
| R1          | G0/0      | `203.0.113.2/30` |
| ISP         | G0/0      | `203.0.113.1/30` |
| ISP         | G0/1      | `203.0.113.5/30` |
| External-PC | NIC       | `203.0.113.6/30` |

External-PC uses:

```text
Default Gateway: 203.0.113.5
```

The `203.0.113.4/30` network is used only as the simulated external test network and is separate from the client's internal `192.168.59.0/24` address block.

---

## 12. Configuration Verification

The configuration was verified using Cisco IOS show commands, including:

```text
show vlan brief
show interfaces trunk
show ip interface brief
show ip route
show access-lists
show ip nat translations
show ip nat statistics
```

The results confirmed that:

* VLANs 10, 20, and 30 are configured.
* The R1-SW1 link operates as a trunk.
* The VLAN subinterfaces are operational.
* R1 has the required connected routes and default route.
* NAT/PAT is configured for Office traffic.
* ACLs 100, 110, and 120 are applied for traffic segmentation.
* The simulated external network is reachable through the ISP.

Detailed connectivity and NAT/PAT test results are documented in the Testing section.

