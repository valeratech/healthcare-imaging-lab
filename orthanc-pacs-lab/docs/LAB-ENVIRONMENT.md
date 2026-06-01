# Lab Environment Reference

## Node Inventory

| Hostname | OS | Role | Management IP | PACS Segment IP |
|----------|----|------|---------------|-----------------|
| orthanc-primary | Ubuntu 24.04.4 LTS | Primary PACS archive | 192.168.175.128 (DHCP) | 192.168.100.10/24 (static) |
| orthanc-secondary | Ubuntu 24.04.4 LTS | Secondary archive | 192.168.175.130 (DHCP) | 192.168.100.20/24 (static) |
| rhcontrol | RHEL 10 | Management/control node | 192.168.175.140 (DHCP) | N/A |

## Network Segments

| Segment | VMnet | Subnet | Purpose |
|---------|-------|--------|---------|
| Management/Internet | VMnet8 NAT | 192.168.175.0/24 | Package management, SSH administration |
| PACS segment | VMnet2 Host-only | 192.168.100.0/24 | DICOM traffic, isolated |

## Orthanc Node Details

| Component | orthanc-primary | orthanc-secondary |
|-----------|----------------|-------------------|
| AE Title | ORTHANC-PRIMARY | ORTHANC-SCNDRY |
| Display name | ORTHANC-PRIMARY | ORTHANC-SECONDARY |
| DICOM port | 4242 | 4242 |
| REST API port | 8042 | 8042 |
| PostgreSQL version | 16.13 | 16.14 |
| Orthanc version | 1.12.2 | 1.12.2 |
| Peer registered | ORTHANC-SCNDRY | ORTHANC-PRIMARY |

## Administration

All lab administration performed via SSH from rhcontrol.
No direct console access used after initial OS installation.

## Notes

- VMnet8 NAT IPs are DHCP assigned and may change on VM restart
- PACS segment IPs are statically assigned via Netplan — stable
- Peer communication between nodes traverses VMnet2 PACS segment exclusively
- rhcontrol used for Ansible automation in later phases
