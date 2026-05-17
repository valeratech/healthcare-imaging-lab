# Phase 1 — Core DICOM Infrastructure
## Build Log and Progress Tracker

**Objective:** Deploy a functional multi-node Orthanc DICOM environment
mirroring real PACS architecture fundamentals.

**Environment:**
- Hypervisor: VMware Workstation Pro 26H1
- OS: Ubuntu 24.04.4 LTS
- Backend: PostgreSQL
- Network: VMnet8 (NAT/internet) + VMnet2 (PACS segment, 192.168.100.0/24)

## Stage Progress

| Stage | Description | Status |
|-------|-------------|--------|
| Stage 1 | VM Creation — VMware Workstation | ✅ Complete |
| Stage 2 | Ubuntu 24.04 OS Install | ✅ Complete |
| Stage 3 | Network Configuration | 🔲 In Progress |
| Stage 4 | PostgreSQL Install and Configuration | 🔲 Not Started |
| Stage 5 | Orthanc Install and Initial Configuration | 🔲 Not Started |
| Stage 6 | Validation | 🔲 Not Started |

## Build Log

### 2026-05-16 — Repository initialized, Phase 1 scope defined

- Hypervisor: VMware Workstation Pro 26H1
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
- CPUs: 2 cores | RAM: 4096 MB | Disk: 40GB single file SCSI
- NIC 1: VMnet8 (NAT) — internet access
- NIC 2: VMnet2 (Host-only, isolated) — PACS segment
- Ubuntu 24.04.4 LTS server ISO attached: ubuntu-24.04.4-live-server-amd64.iso
- Connect at power on: confirmed
- Stage 1 status: ✅ Complete

### 2026-05-16 — Stage 2 complete: Ubuntu 24.04.4 LTS installed on orthanc-primary

- OS: Ubuntu 24.04.4 LTS — minimized server install
- Kernel: Linux 6.8.0-117-generic x86_64
- Hostname confirmed: orthanc-primary
- Username: valeratech
- Virtualization detected: vmware
- Installation type: Ubuntu Server (minimized) — reduced runtime footprint
- Installer update: skipped — continued without updating to 24.04.4.1
- Proxy: none configured
- Mirror: http://archive.ubuntu.com/ubuntu — connectivity confirmed during install
- Storage: /dev/sda 40GB — simple partition layout
  - Partition 1: BIOS grub spacer 1MB
  - Partition 2: ext4 mounted at / — 39.997GB
  - LVM: deliberately not configured — lab simplification
  - Note: Production PACS archive servers use LVM for online volume expansion.
    Deliberate tradeoff accepted for lab environment.
- OpenSSH server: installed and active
- Password authentication over SSH: enabled
- Featured snaps: none installed — explicit packages only
- Post-install validation:
  - hostnamectl: Static hostname orthanc-primary confirmed
  - ip link show:
    - lo: UP — loopback
    - ens33: UP — VMnet8 NAT interface (internet)
    - ens34: DOWN — VMnet2 PACS segment (no IP assigned — pending Stage 3)
- Stage 2 status: ✅ Complete

### 2026-05-16 — Documentation: Phase 1 Stage 1-2 screenshots curated and committed

- Stage 1 screenshots: 4 kept, committed to infrastructure/screenshots/phase1-stage1/
- Stage 2 screenshots: 8 kept, committed to infrastructure/screenshots/phase1-stage2/
- Routine installer steps excluded — only configuration decisions and
  validation outputs retained

### 2026-05-17 — Management node established for lab administration

- RHEL 10 control node configured on same VMnet8 NAT segment
- Hostname: rhcontrol | IP: 192.168.175.140 (DHCP)
- Purpose: SSH administration, copy/paste capable terminal sessions,
  future Ansible automation target
- Connectivity verified: ping and SSH to orthanc-primary (192.168.175.128)
  confirmed successful from control node
- SSH fingerprint accepted and persisted to known_hosts
- All subsequent lab administration performed from rhcontrol via SSH
- orthanc-primary management IP confirmed: 192.168.175.128 (DHCP via VMnet8)
- OS release confirmed via SSH: Ubuntu 24.04.4 LTS (Noble Numbat)
