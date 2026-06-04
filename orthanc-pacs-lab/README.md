# Orthanc PACS Lab

A multi-phase hands-on laboratory building a functional DICOM/PACS 
environment using Orthanc as the primary platform. The lab mirrors 
real-world PACS architecture decisions, operational workflows, and 
security considerations encountered in production clinical imaging 
environments.

## Objectives

- Deploy and operate a multi-node Orthanc DICOM environment
- Analyze DICOM protocol traffic at the association level
- Build administrative and security automation against the Orthanc REST API
- Apply security hardening within clinical operational constraints
- Instrument the environment with production-realistic monitoring
- Diagnose and resolve synthetic PACS incident scenarios
- Simulate HL7/DICOM integration workflows via Mirth Connect

## Architecture Overview

Two-node Orthanc environment simulating a primary PACS archive 
and a secondary remote archive node. All DICOM traffic transits 
a dedicated isolated network segment simulating clinical VLAN 
segmentation.

| Node | Hostname | IP Address | Role |
|------|----------|------------|------|
| Node 1 | orthanc-primary | 192.168.100.10 | Primary PACS archive |
| Node 2 | orthanc-secondary | 192.168.100.20 | Secondary/remote archive |

**AE Title assignments, port configuration, and full network 
diagram documented in** [`infrastructure/`](./infrastructure/)

## Environment

| Component | Detail |
|-----------|--------|
| Hypervisor | VMware Workstation Pro 26H1 |
| Operating System | Ubuntu 24.04.4 LTS |
| Orthanc Version | 1.12.2 |
| Database Backend | PostgreSQL |
| Integration Engine | Mirth Connect — planned Phase 7 |
| PACS Segment | 192.168.100.0/24 — VMnet2, isolated, no DHCP |
| Internet Access | VMnet8 NAT — package management only |
| Datasets | TCIA public anonymized datasets |
| DICOM Tooling | DCMTK — storescu, findscu, movescu, echoscu |
| Packet Analysis | Wireshark with DICOM dissectors |
| Automation | Python, Bash |
| Monitoring | Prometheus + Grafana |

## Phases

| Phase | Focus | Directory | Status |
|-------|-------|-----------|--------|
| Phase 1 | Core DICOM Infrastructure | [infrastructure/](./infrastructure/) | ✅ Complete |
| Phase 2 | Protocol Analysis and Troubleshooting | [protocol-analysis/](./protocol-analysis/) | 🔲 Planned |
| Phase 3 | REST API Automation | [automation/](./automation/) | 🔲 Planned |
| Phase 4 | Security Layer | [security/](./security/) | 🔲 Planned |
| Phase 5 | Monitoring and Observability | [monitoring/](./monitoring/) | 🔲 Planned |
| Phase 6 | Synthetic Incident Scenarios | [protocol-analysis/](./protocol-analysis/) | 🔲 Planned |
| Phase 7 | Mirth Connect — HL7/DICOM Integration | [mirth-connect/](./mirth-connect/) | 🔲 Planned |

## CIIP Knowledge Domain Coverage

This lab is structured to provide hands-on expression of SIIM CIIP 
knowledge domains across all phases.

| Domain | Primary Lab Expression |
|--------|----------------------|
| DICOM Standard | Phases 1, 2 — infrastructure and protocol analysis |
| PACS Architecture and Administration | Phases 1, 3, 5 |
| Healthcare Interoperability | Phase 2 — C-Move, Phase 7 — HL7/DICOM via Mirth Connect |
| Healthcare IT Operations | Phase 5 — monitoring, Phase 6 — incidents |
| Informatics and Data Management | Phase 3 — scripts 1, 3, 5 |
| Security and Compliance | Phase 4 — full security layer |

## Repository Structure

```
orthanc-pacs-lab/
├── README.md                  ← This file
├── infrastructure/            ← Phase 1: configs, diagrams, build logs
├── protocol-analysis/         ← Phase 2 and 6: captures, case studies
├── automation/                ← Phase 3: Python scripts
├── security/                  ← Phase 4: hardening, threat model
├── monitoring/                ← Phase 5: Grafana, Prometheus
├── mirth-connect/             ← Phase 7: HL7/DICOM integration
└── docs/                      ← Supporting docs, glossary, CIIP mapping
```

## Important Note

All work performed in an isolated lab environment using publicly 
available anonymized datasets from The Cancer Imaging Archive (TCIA). 
No real patient data is used at any point. PHI audit and security 
exercises operate exclusively against synthetic or publicly 
anonymized data.
