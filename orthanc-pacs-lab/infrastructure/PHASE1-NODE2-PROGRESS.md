# Phase 1 — Node 2 Build
## orthanc-secondary — Build Log and Progress Tracker

**Objective:** Deploy orthanc-secondary as the second node of the multi-node
Orthanc DICOM environment, then establish peer relationships between nodes to
complete the foundational PACS replication and routing architecture.

**Environment:**
- Hypervisor: VMware Workstation Pro 26H1
- OS: Ubuntu 24.04.4 LTS
- Backend: PostgreSQL
- Network: VMnet8 (NAT/internet) + VMnet2 (PACS segment, 192.168.100.0/24)

**Node target:**
- Hostname: orthanc-secondary
- PACS segment IP: 192.168.100.20/24
- AE Title: ORTHANC-SECONDARY
- DICOM port: 4242
- REST API port: 8042

## Stage Progress

| Stage | Description | Status |
|-------|-------------|--------|
| Stage 1 | VM Creation — VMware Workstation | ✅ Complete |
| Stage 2 | Ubuntu 24.04 OS Install | ✅ Complete |
| Stage 3 | Network Configuration | ⬜ Pending |
| Stage 4 | PostgreSQL Install and Configuration | ⬜ Pending |
| Stage 5 | Orthanc Install and Initial Configuration | ⬜ Pending |
| Stage 6 | Validation | ⬜ Pending |
| Stage 7 | Peer Configuration — Node 1 ↔ Node 2 | ⬜ Pending |

## Build Log

### 2026-05-29 — Node 2 build initiated

- Build reference: orthanc-primary (Node 1) — identical process with
  node-specific values substituted
- Hostname target: orthanc-secondary
- PACS segment IP target: 192.168.100.20/24
- AE Title target: ORTHANC-SECONDARY
- Username: valeratech (same as Node 1)
- PostgreSQL database and user: orthanc/orthanc — separate instance, no conflict
- Ports: 4242 (DICOM), 8042 (REST API) — isolated node, no conflict
- Build log file: infrastructure/PHASE1-NODE2-PROGRESS.md

### 2026-05-29 — Stage 1 complete: orthanc-secondary VM created

- VM name: orthanc-secondary
- Location: E:\Virtual Machines\ORTHANC_SECONDARY
- CPUs: 2 cores | RAM: 4096 MB | Disk: 40 GB single file SCSI
- NIC 1: VMnet8 (NAT) — internet access
- NIC 2: VMnet2 (Host-only, isolated) — PACS segment
- Ubuntu 24.04.4 LTS server ISO attached: ubuntu-24.04.4-live-server-amd64.iso
- Connect at power on: confirmed
- Stage 1 status: ✅ Complete

### 2026-05-29 — Documentation: Node 2 Stage 1 screenshots curated and committed

- 4 screenshots kept — execution order sequence
- phase2-stage1-01-vm-install-os-later.png — OS install later selected
- phase2-stage1-02-vm-name-and-location.png — VM name orthanc-secondary confirmed
- phase2-stage1-03-vm-creation-summary.png — creation summary reviewed
- phase2-stage1-04-vm-hardware-settings.png — hardware spec and dual NIC confirmed
- Committed to infrastructure/screenshots/phase2-stage1/

---

### 2026-05-29 — Stage 2 complete: Ubuntu 24.04.4 LTS installed on orthanc-secondary

- OS: Ubuntu 24.04.4 LTS — minimized server install
- Kernel: Linux 6.8.0-124-generic x86_64
  - Note: Minor kernel version difference from orthanc-primary (6.8.0-117-generic) —
    reflects kernel update available at time of Node 2 install. No operational impact.
- Hostname confirmed: orthanc-secondary
- Username: valeratech
- Virtualization detected: vmware
- Installation type: Ubuntu Server (minimized) — reduced runtime footprint
- Installer update: skipped — continued without updating
- Proxy: none configured
- Mirror: http://archive.ubuntu.com/ubuntu — connectivity confirmed during install
- Storage: /dev/sda 40 GB — simple partition layout
  - Partition 1: BIOS grub spacer 1MB
  - Partition 2: ext4 mounted at / — 39.997 GB
  - LVM: deliberately not configured — lab simplification
  - Note: Production PACS archive servers use LVM for online volume expansion.
    Deliberate tradeoff accepted for lab environment.
- OpenSSH server: installed and active
- Password authentication over SSH: enabled
- Featured snaps: none installed — explicit packages only
- Post-install validation:
  - hostnamectl: Static hostname orthanc-secondary confirmed
  - ip link show:
    - lo: UP — loopback
    - ens33: UP — VMnet8 NAT interface (internet)
    - ens34: DOWN — VMnet2 PACS segment (no IP assigned — pending Stage 3)
- Stage 2 status: ✅ Complete

### 2026-05-29 — Documentation: Node 2 Stage 2 screenshots curated and committed

- 8 screenshots kept — execution order sequence
- phase2-stage2-01-installer-no-update.png — installer update skipped
- phase2-stage2-02-minimized-install-selected.png — Ubuntu Server minimized confirmed
- phase2-stage2-03-storage-no-lvm.png — simple partition layout, no LVM
- phase2-stage2-04-profile-configuration.png — hostname and username configured
- phase2-stage2-05-ssh-openssh-enabled.png — OpenSSH server enabled
- phase2-stage2-06-snaps-none-selected.png — no snaps installed
- phase2-stage2-07-installation-complete.png — installation complete
- phase2-stage2-08-post-install-validation.png — hostnamectl and ip link show confirmed
- Committed to infrastructure/screenshots/phase2-stage2/

---

### YYYY-MM-DD — Management node SSH access established to orthanc-secondary

- SSH from rhcontrol to orthanc-secondary: <!-- 192.168.175.xxx --> confirmed
- SSH fingerprint accepted and persisted to known_hosts
- OS release confirmed via SSH: Ubuntu 24.04.4 LTS (Noble Numbat)
- All subsequent Node 2 administration performed from rhcontrol via SSH

---

### YYYY-MM-DD — Stage 3 complete: Network configuration applied on orthanc-secondary

- Existing Netplan file: /etc/netplan/50-cloud-init.yaml — ens33 DHCP only
- 50-cloud-init.yaml not modified — managed by cloud-init, overwrite risk
- New Netplan file created: /etc/netplan/99-pacs-static.yaml
- ens34 configured: static IP 192.168.100.20/24, dhcp4 disabled
- Permissions set: 600 on 99-pacs-static.yaml
- netplan apply executed — no errors
- Post-apply validation:
  - ens34: UP
  - inet 192.168.100.20/24 — confirmed
  - valid_lft forever — static assignment confirmed
- PACS segment connectivity: ping 192.168.100.10 from orthanc-secondary — confirmed
- Stage 3 status: ✅ Complete

### YYYY-MM-DD — Documentation: Node 2 Stage 3 screenshots curated and committed

- <!-- N --> screenshots kept — execution order sequence
- <!-- stage3-01-description.png -->
- Committed to infrastructure/screenshots/phase2-stage3/

---

### YYYY-MM-DD — Stage 4 complete: PostgreSQL installed and configured on orthanc-secondary

- PostgreSQL version: <!-- 16.x -->
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

### YYYY-MM-DD — Documentation: Node 2 Stage 4 screenshots curated and committed

- <!-- N --> screenshots kept — execution order sequence
- <!-- stage4-01-description.png -->
- Committed to infrastructure/screenshots/phase2-stage4/

---

### YYYY-MM-DD — Stage 5 complete: Orthanc installed and configured on orthanc-secondary

- Orthanc version: <!-- 1.12.2+dfsg-1build4 --> (Ubuntu universe repository)
- orthanc-postgresql plugin: <!-- 5.0+dfsg-2build3 -->
- Configuration files modified:
  - /etc/orthanc/orthanc.json:
    - Name: ORTHANC-SECONDARY
    - DicomAet: ORTHANC-SECONDARY
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
  - curl -u admin http://<!-- 192.168.175.xxx -->:8042/system
  - DatabaseBackendPlugin: libOrthancPostgreSQLIndex.so.<!-- x.x --> confirmed
  - DicomAet: ORTHANC-SECONDARY confirmed
  - IsHttpServerSecure: true confirmed
  - PluginsEnabled: true confirmed
- Stage 5 status: ✅ Complete

### YYYY-MM-DD — Documentation: Node 2 Stage 5 screenshots curated and committed

- <!-- N --> screenshots kept — execution order sequence
- <!-- stage5-01-description.png -->
- Committed to infrastructure/screenshots/phase2-stage5/

---

### YYYY-MM-DD — Stage 6 complete: Full stack validation on orthanc-secondary

- Orthanc Explorer: accessible at http://<!-- 192.168.175.xxx -->:8042
  - Browser authentication prompt confirmed
  - Credentials accepted: admin user
  - Instance name confirmed: ORTHANC-SECONDARY
  - Page confirmed: "ORTHANC-SECONDARY » Lookup studies"
- DICOM port validation: nc -zv <!-- 192.168.175.xxx --> 4242
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
  - PostgreSQL <!-- 16.x --> index backend
  - Orthanc <!-- 1.12.2 --> with PostgreSQL plugin
  - DICOM AE: ORTHANC-SECONDARY port 4242
  - REST API port 8042 with authentication
- Stage 6 status: ✅ Complete

### YYYY-MM-DD — Documentation: Node 2 Stage 6 screenshots curated and committed

- <!-- N --> screenshots kept — execution order sequence
- <!-- stage6-01-description.png -->
- Committed to infrastructure/screenshots/phase2-stage6/

---

### YYYY-MM-DD — Stage 7 complete: Peer configuration — ORTHANC-PRIMARY ↔ ORTHANC-SECONDARY

- Peer registration on orthanc-primary (/etc/orthanc/orthanc.json):
  - Peers entry: ORTHANC-SECONDARY
  - URL: http://192.168.100.20:8042
  - Username: admin
  - Password: secured
- Peer registration on orthanc-secondary (/etc/orthanc/orthanc.json):
  - Peers entry: ORTHANC-PRIMARY
  - URL: http://192.168.100.10:8042
  - Username: admin
  - Password: secured
- Both nodes restarted: systemctl restart orthanc
- Peer connectivity validation:
  - REST API /peers from orthanc-primary: ORTHANC-SECONDARY listed
  - REST API /peers from orthanc-secondary: ORTHANC-PRIMARY listed
  - /peers/ORTHANC-SECONDARY/system from Node 1: ORTHANC-SECONDARY confirmed reachable
  - /peers/ORTHANC-PRIMARY/system from Node 2: ORTHANC-PRIMARY confirmed reachable
- Note: Peer communication traverses VMnet2 PACS segment (192.168.100.0/24) —
  isolated from management network. Mirrors production PACS VLAN architecture
  where inter-system replication uses dedicated imaging network, not general LAN.
- Stage 7 status: ✅ Complete

### YYYY-MM-DD — Documentation: Stage 7 screenshots curated and committed

- <!-- N --> screenshots kept — execution order sequence
- <!-- stage7-01-description.png -->
- Committed to infrastructure/screenshots/phase2-stage7/

---

### YYYY-MM-DD — Node 2 build complete

- All 7 stages completed and validated
- orthanc-secondary node fully operational
- PostgreSQL backend confirmed active
- DICOM server listening on port 4242 — AE Title ORTHANC-SECONDARY
- REST API listening on port 8042 — authentication enforced
- Orthanc Explorer accessible and confirmed
- Peer relationship established: ORTHANC-PRIMARY ↔ ORTHANC-SECONDARY
- Inter-node communication over VMnet2 PACS segment confirmed
- Repository documentation current through Node 2 closure
- Next: Phase 2 — <!-- TBD -->
