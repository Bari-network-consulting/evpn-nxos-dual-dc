# AWX Integration Guide — evpn-nxos-dual-dc

## Step 1 — Create Project in AWX

1. AWX → Projects → Add
2. Name: evpn-nxos-dual-dc
3. Organization: Default
4. Source Control Type: Git
5. Source Control URL: https://github.com/Bari-network-consulting/evpn-nxos-dual-dc.git
6. Branch: main
7. Source Control Credential: GitHub-Bari
8. Options: check "Update Revision on Launch"
9. Save → Sync Project

---

## Step 2 — Create Credentials

### DC1 NX-OS Credential
Name: DC1-NX-OS
Type: Network
Username: admin
Password: admin123
SSH Key: (leave empty)

### DC2 NX-OS Credential
Name: DC2-NX-OS
Type: Network
Username: admin
Password: admin123

---

## Step 3 — Create Inventories

### DC1 Inventory
1. AWX → Inventories → Add Inventory
2. Name: DC1-Fabric
3. Save
4. Sources → Add
5. Name: DC1-Git-Inventory
6. Source: Sourced from a Project
7. Project: evpn-nxos-dual-dc
8. Inventory File: inventory/dc1/hosts
9. Save → Sync

### DC2 Inventory
Same steps with:
- Name: DC2-Fabric
- Inventory File: inventory/dc2/hosts

---

## Step 4 — Create Job Templates

### Module 00 — Wipe DC1
Name:        Module 00 - Wipe DC1
Playbook:    playbooks/module00_wipe_dc1.yml
Inventory:   DC1-Fabric
Credentials: DC1-NX-OS
Extra Vars:  (none)
WARNING:     This removes ALL config except management!

### Module 00 — Wipe DC2
Name:        Module 00 - Wipe DC2
Playbook:    playbooks/module00_wipe_dc2.yml
Inventory:   DC2-Fabric
Credentials: DC2-NX-OS

### Module 01 — OSPF Underlay DC1
Name:        Module 01 - OSPF Underlay DC1
Playbook:    playbooks/module01_ospf_underlay.yml
Inventory:   DC1-Fabric
Credentials: DC1-NX-OS

### Module 01 — OSPF Underlay DC2
Name:        Module 01 - OSPF Underlay DC2
Playbook:    playbooks/module01_ospf_underlay.yml
Inventory:   DC2-Fabric
Credentials: DC2-NX-OS

### (Repeat for modules 02-10 for both DC1 and DC2)

### Module 09 — Full DC1 Deployment
Name:        Module 09 - Full DC1 Deploy
Playbook:    playbooks/module09_deploy_all.yml
Inventory:   DC1-Fabric
Credentials: DC1-NX-OS

### Module 09 — Full DC2 Deployment
Name:        Module 09 - Full DC2 Deploy
Playbook:    playbooks/module09_deploy_all.yml
Inventory:   DC2-Fabric
Credentials: DC2-NX-OS

---

## Step 5 — Create Workflow Templates

### Workflow: DC1 Full Reset and Redeploy
1. Module 00 - Wipe DC1        (on success →)
2. Module 01 - OSPF DC1        (on success →)
3. Module 02 - BGP EVPN DC1    (on success →)
4. Module 03 - VXLAN DC1       (on success →)
5. Module 04 - IRB DC1         (on success →)
6. Module 05 - vPC Pair1 DC1   (on success →)
7. Module 06 - vPC Pair2 DC1   (on success →)
8. Module 07b - Fortinet DC1   (on success →)
9. Module 08 - Verify DC1

### Workflow: Dual DC Full Reset and Redeploy
1. Module 00 - Wipe DC1  +  Module 00 - Wipe DC2  (parallel)
2. Module 09 - Full DC1  +  Module 09 - Full DC2  (parallel)
3. Module 08 - Verify DC1 + Module 08 - Verify DC2 (parallel)
4. Module 10 - DCI

---

## Usage — Clean Start for Video Recording

Run this sequence before recording:

1. AWX → Workflow: DC1 Full Reset and Redeploy → Launch
2. Wait ~10 minutes for complete deployment
3. Verify: ping 10.20.20.11 from SRV1-TEN-A (100%)
4. Wipe again: Module 00 - Wipe DC1
5. Start recording — redeploy live on camera
