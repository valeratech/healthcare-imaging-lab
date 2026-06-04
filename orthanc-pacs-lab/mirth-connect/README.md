# Mirth Connect — HL7/DICOM Integration

## Overview

This directory covers Phase 7 of the Orthanc PACS Lab — integrating
Mirth Connect as an HL7/DICOM integration engine to simulate the
RIS/PACS interoperability layer found in production clinical imaging
environments.

## Objectives

- Deploy Mirth Connect as a dedicated integration engine node
- Simulate HL7 ADT and ORM message flows into DICOM Modality Worklist
- Route HL7 ORU result messages from PACS back to a simulated RIS
- Demonstrate the full order-to-image workflow in a lab environment
- Apply security controls to the integration layer consistent with Phase 4

## Planned Architecture

```
Simulated RIS
     │ HL7 ADT/ORM
     ▼
Mirth Connect (mirth-node)
     │ HL7 transform / worklist feed
     ▼
MWL data source
     ▲
     │ DICOM C-FIND MWL
Simulated modality
     │ DICOM C-STORE
     ▼
orthanc-primary (ORTHANC-PRIMARY)
     │ peer/archive routing
     ▼
orthanc-secondary (ORTHANC-SCNDRY)
```

## Planned Node

| Component | Detail |
|-----------|--------|
| Hostname | mirth-node |
| OS | Ubuntu 24.04.4 LTS |
| Software | Mirth Connect |
| Management IP | VMnet8 NAT (DHCP) |
| PACS segment IP | 192.168.100.30/24 (planned) |
| Role | HL7/DICOM integration engine |

## Planned Channels

| Channel | Direction | Protocol | Purpose |
|---------|-----------|----------|---------|
| ADT Inbound | Inbound | HL7 v2 | Receive patient registration messages |
| ORM Inbound | Inbound | HL7 v2 | Receive imaging order messages |
| MWL Integration | Interface | HL7 to DICOM MWL | Transform order data into worklist-ready records |
| ORU Outbound | Outbound | HL7 v2 | Route result messages back to simulated RIS |

## CIIP Knowledge Domain Coverage

| Domain | Coverage |
|--------|---------|
| Healthcare Interoperability | HL7 ADT/ORM/ORU message structure and routing |
| DICOM Standard | Modality Worklist (MWL) — C-FIND SCP/SCU |
| PACS Architecture | RIS/PACS integration layer, order-to-image workflow |
| Healthcare IT Operations | Integration engine administration, channel monitoring |

## Prerequisites

Phase 7 builds on the following completed phases:
- Phase 1 — Two-node Orthanc environment with peer configuration
- Phase 3 — Orthanc REST API automation
- Phase 4 — Security hardening
- Phase 5 — Monitoring and observability

## Status

🔲 Planned — implementation follows Phase 6 completion.
