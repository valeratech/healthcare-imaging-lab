# Phase 1 — Core DICOM Infrastructure
## Build Log and Progress Tracker

**Objective:** Deploy a functional multi-node Orthanc DICOM environment
mirroring real PACS architecture fundamentals.

**Environment:**
- Hypervisor: VMware Workstation
- OS: Ubuntu 24.04 LTS
- Backend: PostgreSQL
- Network: VMnet8 (NAT/internet) + VMnet2 (PACS segment, 192.168.100.0/24)

## Stage Progress

| Stage | Description | Status |
|-------|-------------|--------|
| Stage 1 | VM Creation — VMware Workstation | ✅ Complete |
| Stage 2 | Ubuntu 24.04 OS Install | 🔲 Not Started |
| Stage 3 | Network Configuration | 🔲 Not Started |
| Stage 4 | PostgreSQL Install and Configuration | 🔲 Not Started |
| Stage 5 | Orthanc Install and Initial Configuration | 🔲 Not Started |
| Stage 6 | Validation | 🔲 Not Started |

## Build Log

### 2026-05-16 — Repository initialized, Phase 1 scope defined

- Hypervisor: VMware Workstation
- VMnet2 created: Host-only, no host adapter, no DHCP, 192.168.100.0/24
- VMnet8: NAT — internet access for package management
- Node 1 target hostname: orthanc-primary | IP: 192.168.100.10
- Node 2 target hostname: orthanc-secondary | IP: 192.168.100.20
- OS selection: Ubuntu 24.04 LTS minimal server install
- Database backend: PostgreSQL — mandatory before meaningful lab work begins
- GitHub repository initialized: healthcare-imaging-lab
- Directory structure established: orthanc-pacs-lab/

### 2026-05-16 — Stage 1 complete: orthanc-primary VM created

- VM name: orthanc-primary
- CPUs: 2 cores | RAM: 4096 MB | Disk: 40GB single file
- NIC 1: VMnet8 (NAT) — internet access
- NIC 2: VMnet2 (Host-only, isolated) — PACS segment
- Ubuntu 24.04 LTS server ISO attached: ubuntu-24.04.4-live-server-amd64.iso
- Connect at power on: confirmed
- Stage 1 status: ✅ Complete
