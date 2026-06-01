# Infrastructure

This directory contains build logs, configuration references, network
documentation, and installation screenshots for the Orthanc PACS lab
infrastructure. All work documented here reflects the physical lab
environment built in VMware Workstation Pro 26H1.

## Phase 1 — Core DICOM Infrastructure

### Objective

Deploy a functional multi-node Orthanc DICOM environment mirroring real
PACS architecture fundamentals. Interoperability only becomes meaningful
with multiple systems — single node configurations do not reflect
production PACS reality.

### Phase 1 Status

| Node | Hostname | Status |
|------|----------|--------|
| Node 1 | orthanc-primary | ✅ Complete |
| Node 2 | orthanc-secondary | ✅ Complete |

### Node 1 — orthanc-primary

| Component | Detail |
|-----------|--------|
| OS | Ubuntu 24.04.4 LTS minimized server |
| Orthanc version | 1.12.2 |
| PostgreSQL version | 16.13 |
| AE Title | ORTHANC-PRIMARY |
| DICOM port | 4242 |
| REST API port | 8042 |
| PACS segment IP | 192.168.100.10/24 (static) |
| Authentication | HTTP Basic — enforced |
| Database backend | PostgreSQL index — files on disk |
| Service status | Active, enabled at boot |
| Orthanc Explorer | Confirmed accessible |

### Node 2 — orthanc-secondary

| Component | Detail |
|-----------|--------|
| OS | Ubuntu 24.04.4 LTS minimized server |
| Orthanc version | 1.12.2 |
| PostgreSQL version | 16.14 |
| AE Title | ORTHANC-SCNDRY (display name: ORTHANC-SECONDARY) |
| DICOM port | 4242 |
| REST API port | 8042 |
| PACS segment IP | 192.168.100.20/24 (static) |
| Authentication | HTTP Basic — enforced |
| Database backend | PostgreSQL index — files on disk |
| Service status | Active, enabled at boot |
| Orthanc Explorer | Confirmed accessible |

### Peer Configuration

| Peer | Registered On | URL | Status |
|------|--------------|-----|--------|
| ORTHANC-SCNDRY | orthanc-primary | http://192.168.100.20:8042 | ✅ Confirmed reachable |
| ORTHANC-PRIMARY | orthanc-secondary | http://192.168.100.10:8042 | ✅ Confirmed reachable |

Peer traffic traverses the VMnet2 PACS segment exclusively — isolated
from management network traffic. Mirrors production PACS VLAN architecture
where inter-system replication uses a dedicated imaging network.

## Network Architecture

| Segment | VMnet | Subnet | Purpose |
|---------|-------|--------|---------|
| Management / Internet | VMnet8 NAT | 192.168.175.0/24 | Package management, SSH administration |
| PACS segment | VMnet2 Host-only | 192.168.100.0/24 | DICOM traffic — isolated, no DHCP |

All DICOM traffic between nodes transits the VMnet2 PACS segment
exclusively. Internet access and SSH administration use VMnet8 NAT.
This mirrors the dedicated clinical imaging VLAN segmentation used
in production PACS environments.

## Management Node

| Component | Detail |
|-----------|--------|
| Hostname | rhcontrol |
| OS | RHEL 10 |
| IP | 192.168.175.140 (DHCP via VMnet8) |
| Role | SSH administration, terminal management |
| Purpose | All lab administration performed from this node |

Direct console access to PACS nodes is reserved for emergency
break-glass scenarios only — mirroring production clinical environment
administrative access policy.

## Directory Structure
```
infrastructure/
├── README.md                        ← This file
├── PHASE1-PROGRESS.md               ← Node 1 build log — complete
├── PHASE1-NODE2-PROGRESS.md         ← Node 2 build log — complete
└── screenshots/
    ├── phase1-stage1/               ← VM creation — 4 screenshots
    ├── phase1-stage2/               ← OS install — 8 screenshots
    ├── phase1-stage3/               ← Network configuration — 5 screenshots
    ├── phase1-stage4/               ← PostgreSQL — 6 screenshots
    ├── phase1-stage5/               ← Orthanc install — 9 screenshots
    ├── phase1-stage6/               ← Validation — 3 screenshots
    ├── phase2-stage1/               ← VM creation — 4 screenshots
    ├── phase2-stage2/               ← OS install — 8 screenshots
    ├── phase2-stage3/               ← Network configuration — 7 screenshots
    ├── phase2-stage4/               ← PostgreSQL — 7 screenshots
    ├── phase2-stage5/               ← Orthanc install — 10 screenshots
    ├── phase2-stage6/               ← Validation — 4 screenshots
    └── phase2-stage7/               ← Peer configuration — 6 screenshots
```

## Key Documentation

| File | Purpose |
|------|---------|
| PHASE1-PROGRESS.md | Complete Node 1 build log with all stage decisions |
| PHASE1-NODE2-PROGRESS.md | Complete Node 2 build log with all stage decisions |

Supporting documentation for this infrastructure is maintained in
the [`docs/`](../docs/) directory:

| Document | Purpose |
|----------|---------|
| LAB-ENVIRONMENT.md | Full node inventory and network reference |
| ORTHANC-INSTALL-REFERENCE.md | Clean install CLI reference for future builds |
| PHASE1-CLI-REFERENCE.md | Every CLI command used in Phase 1 with descriptions |
| PHASE1-CIIP-NOTES.md | CIIP knowledge domain connections for Phase 1 |
