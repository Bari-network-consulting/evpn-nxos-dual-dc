# BGP EVPN/VXLAN Dual Data Center — Cisco NX-OS

**Author:** Abdelkrim Bari | CCIE #20869 | Bari Network Consulting  
**Status:** DC1 Complete ✅ | DC2 In Progress 🔄 | DCI Planned 📋

## Key Design — One Playbook Set for Both DCs

Deploy DC1:
ansible-playbook -i inventory/dc1/hosts playbooks/module09_deploy_all.yml

Deploy DC2 (same playbooks, different inventory):
ansible-playbook -i inventory/dc2/hosts playbooks/module09_deploy_all.yml
