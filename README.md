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
| Hypervisor | VMware Workstation |
| Operating System | Ubuntu 24.04 LTS |
| Primary Platform | Orthanc DICOM Server |
| Database Backend | PostgreSQL |
| Network Simulation | Dedicated isolated PACS segment |
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
| Phase 1 | Core DICOM Infrastructure | 🔲 In Progress |
| Phase 2 | Protocol Analysis and Troubleshooting | 🔲 Planned |
| Phase 3 | REST API Automation | 🔲 Planned |
| Phase 4 | Security Layer | 🔲 Planned |
| Phase 5 | Monitoring and Observability | 🔲 Planned |
| Phase 6 | Synthetic Incident Scenarios | 🔲 Planned |

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
