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
| Stage 3 | Network Configuration | ✅ Complete |
| Stage 4 | PostgreSQL Install and Configuration | ✅ Complete |
| Stage 5 | Orthanc Install and Initial Configuration | ✅ Complete |
| Stage 6 | Validation | ✅ Complete |

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

### 2026-05-16 — Documentation: Phase 1 Stage 1 screenshots curated and committed

- 4 screenshots kept — execution order sequence
- phase1-stage1-01-guest-os-ubuntu64.png — guest OS type confirmed
- phase1-stage1-02-install-os-later.png — OS install later selected
- phase1-stage1-03-vm-name-location.png — VM name orthanc-primary confirmed
- phase1-stage1-04-cpu-configuration.png — CPU and RAM spec confirmed
- Committed to infrastructure/screenshots/phase1-stage1/

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

### 2026-05-16 — Documentation: Phase 1 Stage 2 screenshots curated and committed

- 8 screenshots kept — execution order sequence
- phase1-stage2-01-installer-no-update.png — installer update skipped
- phase1-stage2-02-minimized-install-selected.png — Ubuntu Server minimized confirmed
- phase1-stage2-03-storage-no-lvm.png — simple partition layout, no LVM
- phase1-stage2-04-profile-configuration.png — hostname and username configured
- phase1-stage2-05-ssh-openssh-enabled.png — OpenSSH server enabled
- phase1-stage2-06-snaps-none-selected.png — no snaps installed
- phase1-stage2-07-installation-complete.png — installation complete
- phase1-stage2-08-post-install-validation.png — hostnamectl and ip link show confirmed
- Committed to infrastructure/screenshots/phase1-stage2/

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

### 2026-05-17 — Stage 3 complete: Network configuration applied on orthanc-primary

- Existing Netplan file: /etc/netplan/50-cloud-init.yaml — ens33 DHCP only
- 50-cloud-init.yaml not modified — managed by cloud-init, overwrite risk
- New Netplan file created: /etc/netplan/99-pacs-static.yaml
- ens34 configured: static IP 192.168.100.10/24, dhcp4 disabled
- Permissions set: 600 on 99-pacs-static.yaml
- netplan apply executed — no errors
- Post-apply validation:
  - ens34: UP
  - inet 192.168.100.10/24 — confirmed
  - valid_lft forever — static assignment confirmed
- Stage 3 status: ✅ Complete

### 2026-05-17 — Documentation: Phase 1 Stage 3 screenshots curated and committed

- 5 screenshots kept — execution order sequence
- phase1-stage3-01-pre-state-configuration-ens34.png — baseline ens34 DOWN state
- phase1-stage3-02-ssh-access-os-verified.png — SSH established, OS confirmed
- phase1-stage3-03-netplan-cloud-init-existing.png — existing Netplan inspected
- phase1-stage3-04-netplan-pacs-static-config.png — 99-pacs-static.yaml created
- phase1-stage3-05-ens34-static-ip-verified.png — 192.168.100.10/24 UP confirmed
- Committed to infrastructure/screenshots/phase1-stage3/

### 2026-05-17 — Stage 4 complete: PostgreSQL installed and configured on orthanc-primary

- PostgreSQL version: 16.13 (Ubuntu 16.13-0ubuntu0.24.04.1)
- Cluster: postgresql@16-main — active (running), enabled at boot
- Database created: orthanc
- Owner: orthanc
- Encoding: UTF8
- Dedicated user created: orthanc
- Privileges: CTc on orthanc database
- Connection test: psql -U orthanc -h localhost -d orthanc
  - current_database: orthanc — confirmed
  - current_user: orthanc — confirmed
  - Password authentication: successful
- Connection string validated — ready for Orthanc plugin configuration
- Stage 4 status: ✅ Complete

### 2026-05-17 — Documentation: Phase 1 Stage 4 screenshots curated and committed

- 6 screenshots kept — execution order sequence
- phase1-stage4-01-postgresql-apt-install.png — apt install postgresql command
- phase1-stage4-02-postgresql-install-complete.png — installation output complete
- phase1-stage4-03-postgresql-service-status.png — systemctl status postgresql
- phase1-stage4-04-postgresql-cluster-version.png — cluster status and version confirmed
- phase1-stage4-05-orthanc-db-user-provisioned.png — database, user, privileges created
- phase1-stage4-06-orthanc-db-connection-verified.png — connection test confirmed
- Committed to infrastructure/screenshots/phase1-stage4/

### 2026-05-21 — Stage 5 complete: Orthanc installed and configured on orthanc-primary

- Orthanc version: 1.12.2+dfsg-1build4 (Ubuntu universe repository)
- Note: Ubuntu universe package used — upstream repository at
  package.orthanc-server.com experienced transient DNS resolution
  failure during initial build. 1.12.2 is fully functional for all
  lab phases.
- orthanc-postgresql plugin: 5.0+dfsg-2build3
- Configuration files modified:
  - /etc/orthanc/orthanc.json:
    - Name: ORTHANC-PRIMARY
    - DicomAet: ORTHANC-PRIMARY
    - RemoteAccessAllowed: true
    - AuthenticationEnabled: true
  - /etc/orthanc/credentials.json:
    - Admin user configured — password secured
  - /etc/orthanc/postgresql.json:
    - EnableIndex: true
    - EnableStorage: false — DICOM files stored on disk
    - Database: orthanc
    - Username: orthanc
    - Password: secured
- PostgreSQL index plugin confirmed active on startup:
  - Trigram index created on first startup
  - Custom database backend confirmed in logs
- REST API validation from rhcontrol:
  - curl -u admin http://192.168.175.128:8042/system
  - DatabaseBackendPlugin: libOrthancPostgreSQLIndex.so.5.0 confirmed
  - DicomAet: ORTHANC-PRIMARY confirmed
  - IsHttpServerSecure: true confirmed
  - PluginsEnabled: true confirmed
- Stage 5 status: ✅ Complete

### 2026-05-21 — Documentation: Phase 1 Stage 5 screenshots curated and committed

- 9 screenshots kept — execution order sequence
- phase1-stage5-01-orthanc-apt-install.png — apt install orthanc command
- phase1-stage5-02-orthanc-install-complete.png — installation output complete
- phase1-stage5-03-orthanc-post-install-validation.png — dpkg, service, binary verified
- phase1-stage5-04-orthanc-credentials-config.png — credentials.json configured
- phase1-stage5-05-orthanc-postgresql-config.png — postgresql.json configured
- phase1-stage5-06-orthanc-json-key-settings-grep.png — orthanc.json key settings confirmed
- phase1-stage5-07-orthanc-startup-log.png — clean startup log verified
- phase1-stage5-08-orthanc-service-journal.png — systemctl status confirmed
- phase1-stage5-09-orthanc-rest-api-system-verified.png — REST API /system response confirmed
- Committed to infrastructure/screenshots/phase1-stage5/

### 2026-05-21 — Stage 6 complete: Full stack validation on orthanc-primary

- Orthanc Explorer: accessible at http://192.168.175.128:8042
  - Browser authentication prompt confirmed
  - Credentials accepted: admin user
  - Instance name confirmed: ORTHANC-PRIMARY
  - Page confirmed: "ORTHANC-PRIMARY » Lookup studies"
- DICOM port validation: nc -zv 192.168.175.128 4242
  - TCP connection confirmed — port 4242 listening
- REST API /patients: [] — empty database confirmed
- REST API /studies: [] — empty database confirmed
- REST API /statistics:
  - CountPatients: 0
  - CountStudies: 0
  - CountSeries: 0
  - CountInstances: 0
  - TotalDiskSize: 0
  - PostgreSQL backend active — all counts accurate
- Full stack confirmed operational:
  - Ubuntu 24.04.4 LTS
  - PostgreSQL 16.13 index backend
  - Orthanc 1.12.2 with PostgreSQL plugin
  - DICOM AE: ORTHANC-PRIMARY port 4242
  - REST API port 8042 with authentication
- Stage 6 status: ✅ Complete

### 2026-05-21 — Documentation: Phase 1 Stage 6 screenshots curated and committed

- 3 screenshots kept — execution order sequence
- phase1-stage6-01-orthanc-explorer-browser-auth.png — browser authentication prompt
- phase1-stage6-02-orthanc-explorer-ui-accessible.png — Orthanc Explorer UI loaded
- phase1-stage6-03-dicom-port-4242-reachable.png — nc TCP connection + REST API
  validation outputs captured
- Committed to infrastructure/screenshots/phase1-stage6/

### 2026-05-21 — Phase 1 complete

- All 6 stages completed and validated
- orthanc-primary node fully operational
- PostgreSQL backend confirmed active
- DICOM server listening on port 4242 — AE Title ORTHANC-PRIMARY
- REST API listening on port 8042 — authentication enforced
- Orthanc Explorer accessible and confirmed
- Repository documentation current through Phase 1 closure
- Next: Phase 1 Node 2 — orthanc-secondary build
