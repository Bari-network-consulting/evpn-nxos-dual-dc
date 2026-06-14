# BGP EVPN/VXLAN Dual Data Center - Cisco NX-OS

Author: Abdelkrim Bari | CCIE 20869 | Bari Network Consulting
Status: DC1 Complete | DC2 In Progress | DCI Planned

## Key Design - One Playbook Set for Both DCs

DC1 and DC2 use identical playbooks. Only variables differ.

Deploy DC1:
  ansible-playbook -i inventory/dc1/hosts playbooks/module09_deploy_all.yml

Deploy DC2:
  ansible-playbook -i inventory/dc2/hosts playbooks/module09_deploy_all.yml

## Architecture

DC1 AS 65000 - N-SPINE-01/02, N-LEAF-01 to 05, Fortinet-FW1
DC2 AS 65001 - A-SPINE-01/02, A-LEAF-01 to 05, PaloAlto-FW2
DCI: N-LEAF-05 to A-LEAF-05 via eBGP EVPN on Port-Channel10 (E1/5+E1/6)
     10.255.1.1/30 (DC1) to 10.255.1.2/30 (DC2)

## Modules

01 - OSPF Underlay                          DC1 Done
02 - BGP EVPN Route Reflectors              DC1 Done
03 - VXLAN NVE L2VNI 10010/10020           DC1 Done
04 - Tenant Design VRF L3VNI Anycast GW    DC1 Done
05 - vPC Pair1 N-LEAF-01/02 domain 10      DC1 Done
06 - vPC Pair2 N-LEAF-03/04 domain 20      DC1 Done
07 - Border Leaf N-LEAF-05                  DC1 Done
07b - Fortinet FW1 NX-OS integration DC1   DC1 Done
07c - PaloAlto FW2 NX-OS integration DC2   Planned
08 - Verification                           DC1 Done
09 - Full DC Deployment                     DC1 Done
10 - DCI eBGP EVPN N-LEAF-05 to A-LEAF-05 Planned

## IP Addressing DC1 AS 65000

N-SPINE-01   192.168.1.11  Lo0 10.0.0.1
N-SPINE-02   192.168.1.12  Lo0 10.0.0.2
N-LEAF-01    192.168.1.21  Lo0 10.0.0.11  VTEP 10.0.0.111
N-LEAF-02    192.168.1.22  Lo0 10.0.0.12  VTEP 10.0.0.112
N-LEAF-03    192.168.1.23  Lo0 10.0.0.13  VTEP 10.0.0.113
N-LEAF-04    192.168.1.24  Lo0 10.0.0.14  VTEP 10.0.0.114
N-LEAF-05    192.168.1.25  Lo0 10.0.0.15  VTEP 10.0.0.115
Fortinet-FW1 192.168.1.58

## IP Addressing DC2 AS 65001

A-SPINE-01    192.168.1.31  Lo0 10.1.0.1
A-SPINE-02    192.168.1.32  Lo0 10.1.0.2
A-LEAF-01     192.168.1.41  Lo0 10.1.0.11  VTEP 10.1.0.111
A-LEAF-02     192.168.1.42  Lo0 10.1.0.12  VTEP 10.1.0.112
A-LEAF-03     192.168.1.43  Lo0 10.1.0.13  VTEP 10.1.0.113
A-LEAF-04     192.168.1.44  Lo0 10.1.0.14  VTEP 10.1.0.114
A-LEAF-05     192.168.1.45  Lo0 10.1.0.15  VTEP 10.1.0.115
PaloAlto-FW2  192.168.1.62

## Tenant Design

TENANT-A VLAN 10 VNI 10010 L3VNI 50001 DC1 GW 10.10.10.1/24 DC2 GW 10.30.10.1/24
TENANT-B VLAN 20 VNI 10020 L3VNI 50002 DC1 GW 10.20.20.1/24 DC2 GW 10.40.20.1/24

## DC1 Verified Results

OSPF: All 5 Leafs FULL adjacency to both Spines
BGP EVPN: All Leafs Established as RR clients
VXLAN: NVE1 Up VNI 10010/10020 active
TENANT-A: 100 percent intra-tenant connectivity
TENANT-B: 100 percent intra-tenant connectivity
Inter-Tenant: TENANT-A to TENANT-B via Fortinet FW1 100 percent
Firewall Demo: Stop FW traffic fails, Start FW auto-recovery verified
vPC: Po11 Po12 active on both pairs

## Automation Stack

Ansible 2.15.12 / AWX 24.6.1 on Kubernetes k3s
cisco.nxos 5.2.1 / ansible.netcommon 6.1.0

CCIE 20869 | Bari Network Consulting
