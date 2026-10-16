# Milestone 3 Final Verification Addendum

**Project:** CMPG325-2026-138  
**Client:** Dithare Car Wash & Detailing Group  
**Student:** SITHOLE, TJ (51636654)  
**Verification date:** 16 October 2026  
**Platform:** Cisco Packet Tracer

This addendum records the final live checks completed for the final submission. It supplements the Milestone 2 test record in [`README.md`](README.md). Testing was carried out in a Save As copy of the Packet Tracer project; the original project was left untouched. The temporary test PC was removed after the VLAN 30 checks.

## Switch and router state

| Check | Observed result |
| --- | --- |
| `SW1#show vlan brief` | VLAN 10 `Office_Data` active on Fa0/1–Fa0/5; VLAN 20 `CCTV` active on Fa0/6–Fa0/10; VLAN 30 `IoT_Operations` active on Fa0/11–Fa0/15. |
| `SW1#show interfaces trunk` | Gi0/1 is trunking with 802.1Q. VLANs 10, 20, and 30 are allowed, active, and forwarding. |
| `R1#show ip interface brief` | G0/0 `203.0.113.2` up/up; G0/1 up/up; G0/1.10 `192.168.59.1`, G0/1.20 `.33`, and G0/1.30 `.65` all up/up. G0/2 is administratively down. |
| `R1#show ip route` | Connected routes for the three `/27` VLANs and WAN `203.0.113.0/30`; default route via `203.0.113.1`. |

## Live reachability and policy checks

| Source and destination | Result | What it demonstrates |
| --- | --- | --- |
| Office PC1 `192.168.59.2` → External-PC `203.0.113.6` | Initial run: 2/4 replies. Immediate repeat: 4/4 replies, 0% loss. | Office external connectivity works after the initial simulated packet-resolution loss. |
| R1 `show ip nat translations` after PC1 external ping | Four ICMP entries showed inside-local `192.168.59.2`, inside-global `203.0.113.2`, and outside host `203.0.113.6`. | Office source traffic was translated through R1's outside address. |
| Office PC1 → Camera1 `192.168.59.34` and Sensor1 `192.168.59.66` | Both blocked; unreachable responses came from Office gateway `192.168.59.1`. | ACL 100 blocks Office-to-CCTV and Office-to-IoT traffic. |
| CCTV NVR `192.168.59.36` → Camera1 `192.168.59.34` | 4/4 replies. | CCTV devices communicate within their VLAN. |
| CCTV NVR → Office PC1 `192.168.59.2` | Blocked; unreachable responses came from CCTV gateway `192.168.59.33`. | ACL 110 blocks CCTV-to-Office traffic. |
| Temporary VLAN 30 PC `192.168.59.70/27`, gateway `192.168.59.65` → gateway | 4/4 replies. | The temporary test host was connected to VLAN 30 and could reach its gateway. |
| Temporary VLAN 30 PC → Office PC1, Camera1, and External-PC | All three blocked; replies came from IoT gateway `192.168.59.65`. | ACL 120 blocks the tested IoT-to-Office, IoT-to-CCTV, and IoT-to-external traffic. |

The ACL counters corroborated the tests: ACL 100 recorded four matches on each Office deny statement; ACL 110 recorded four CCTV-to-Office denials; ACL 120 recorded four matches on its permit-to-own-subnet statement during the gateway test and four matches on each tested deny statement. These counters support the specific tests above; they do not prove every possible protocol or application flow.

## Scope and evidence limits

The final checks verified VLAN membership, trunk state, router interfaces and routes, Office PAT, CCTV internal reachability, and the listed ACL outcomes. The temporary VLAN 30 PC was a test endpoint and was removed after testing. The checks did not validate live wireless sensor registration or application-level sensor telemetry to the IoT controller. The earlier Milestone 2 test notes remain in the main testing README; this addendum gives the final session's results and address (`192.168.59.70`) so it is not confused with the older temporary-host test.
