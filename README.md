# Healthcare Imaging Lab

A hands-on technical laboratory for DICOM/PACS infrastructure,
healthcare interoperability, and imaging system security — built
to develop operationally credible expertise in healthcare imaging
systems engineering.

## Background

I am an infrastructure and security engineer with 20+ years of enterprise
IT experience executing a deliberate transition into healthcare imaging
systems. This lab compounds existing depth in Linux administration,
enterprise networking, security engineering, and automation into
imaging-specific infrastructure capability.

No prior clinical IT experience — that is the point. This repository
documents what structured, hands-on technical work against real DICOM
tooling and public datasets can produce. The work is designed to be
evaluated on its own merits.

## Lab Environment

| Component | Detail |
|-----------|--------|
| Hypervisor | VMware Workstation Pro 26H1 |
| Operating System | Ubuntu 24.04.4 LTS |
| Primary Platform | Orthanc DICOM Server 1.12.2 |
| Database Backend | PostgreSQL 16.13 |
| Network Simulation | Dedicated isolated PACS segment + NAT management segment |
| Management Node | RHEL 10 control node — SSH administration |
| Datasets | TCIA public anonymized datasets — no real patient data |
| Packet Analysis | Wireshark with DICOM dissectors |
| Automation | Python, Bash, Ansible |
| Monitoring | Prometheus + Grafana |

## Projects

### [Orthanc PACS Lab](./orthanc-pacs-lab/)

Multi-phase lab building a functional DICOM/PACS environment using
Orthanc as the primary platform. Covers core infrastructure, protocol
analysis, REST API automation, security hardening, and operational
monitoring.

| Phase | Focus | Status |
|-------|-------|--------|
| Phase 1 | Core DICOM Infrastructure — Node 1 | ✅ Complete |
| Phase 1 | Core DICOM Infrastructure — Node 2 | 🔲 In Progress |
| Phase 2 | Protocol Analysis and Troubleshooting | 🔲 Planned |
| Phase 3 | REST API Automation | 🔲 Planned |
| Phase 4 | Security Layer | 🔲 Planned |
| Phase 5 | Monitoring and Observability | 🔲 Planned |
| Phase 6 | Synthetic Incident Scenarios | 🔲 Planned |

## Phase 1 — What Was Built

A production-realistic single-node Orthanc DICOM server environment
running on Ubuntu 24.04.4 LTS with PostgreSQL as the index backend.
The node is fully operational with authenticated REST API access,
DICOM AE Title configured, and network segmentation simulating a
clinical PACS environment.

| Component | Detail |
|-----------|--------|
| Node 1 hostname | orthanc-primary |
| AE Title | ORTHANC-PRIMARY |
| DICOM port | 4242 |
| REST API port | 8042 |
| Database backend | PostgreSQL 16.13 — index only, files on disk |
| PACS segment IP | 192.168.100.10/24 |
| Authentication | HTTP Basic — enforced |
| Orthanc Explorer | Accessible and confirmed |

## Standards and Frameworks

- DICOM (Digital Imaging and Communications in Medicine)
- HIPAA Security Rule — Technical Safeguards
- SIIM CIIP (Certified Imaging Informatics Professional) knowledge domains
- HL7 — where applicable to imaging workflow integration

## Important Note

All work performed in an isolated lab environment using publicly
available anonymized datasets from The Cancer Imaging Archive (TCIA).
No real patient data is used at any point. PHI audit and security
exercises operate exclusively against synthetic or publicly
anonymized data.

## Contact

www.linkedin.com/in/ryan-valera-897796210
