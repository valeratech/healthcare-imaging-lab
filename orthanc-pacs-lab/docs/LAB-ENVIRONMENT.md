# Lab Environment Reference

## Node Inventory

| Hostname | OS | Role | Management IP | PACS Segment IP |
|----------|----|------|---------------|-----------------|
| orthanc-primary | Ubuntu 24.04.4 LTS | Primary PACS archive | 192.168.175.128 (DHCP) | 192.168.100.10 (pending) |
| orthanc-secondary | Ubuntu 24.04.4 LTS | Secondary archive | TBD | 192.168.100.20 (pending) |
| rhcontrol | RHEL 10 | Management/control node | 192.168.175.140 (DHCP) | N/A |

## Network Segments

| Segment | VMnet | Subnet | Purpose |
|---------|-------|--------|---------|
| Management/Internet | VMnet8 NAT | 192.168.175.0/24 | Package management, SSH administration |
| PACS segment | VMnet2 Host-only | 192.168.100.0/24 | DICOM traffic, isolated |

## Administration

All lab administration performed via SSH from rhcontrol.
No direct console access used after initial OS installation.

## Notes

- VMnet8 NAT IPs are DHCP assigned and may change on VM restart
- PACS segment IPs are statically assigned via Netplan — stable
- rhcontrol used for Ansible automation in later phases
