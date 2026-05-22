# Phase 1 — CIIP Knowledge Domain Notes

## Overview

This document captures the CIIP (Certified Imaging Informatics Professional)
knowledge domain connections identified throughout Phase 1. Each entry maps
a hands-on lab decision or observation to its corresponding CIIP knowledge
domain, explaining what a clinical imaging informatics professional would
understand about that decision in a production environment.

## Network Architecture and Segmentation

**Lab action:** Created VMnet2 as an isolated host-only network segment with
no DHCP, no host adapter, and a dedicated `192.168.100.0/24` subnet. Assigned
static IPs to all DICOM nodes on this segment.

**CIIP domain:** PACS Architecture — Network Design for Clinical Environments

**Clinical context:**
In production clinical environments, DICOM traffic is always isolated on a
dedicated network segment separate from general enterprise IT traffic. This
is not primarily a security decision — it is an operational reliability
decision. DICOM associations are sensitive to network congestion and latency.
A radiologist waiting for a CT study to load for an urgent read cannot compete
with general office traffic on a shared network. Clinical imaging networks are
segmented by design, and PACS administrators are expected to understand and
enforce this segmentation. The VMnet2 isolation in this lab directly simulates
this clinical network architecture decision.

**CIIP knowledge connection:** Network segmentation for clinical imaging,
modality VLAN design, PACS network topology.

## AE Title Configuration

**Lab action:** Set `DicomAet` to `ORTHANC-PRIMARY` in `orthanc.json`. This
is the Application Entity Title assigned to this Orthanc instance.

**CIIP domain:** DICOM Standard — Application Entity Addressing Model

**Clinical context:**
The AE Title is the fundamental addressing mechanism in DICOM networking.
Every device that participates in DICOM communication — modalities, PACS
archives, workstations, printers — has an AE Title. It functions as the DICOM
equivalent of a hostname. When a CT scanner sends a study to PACS, it addresses
the destination by AE Title. When a workstation queries for studies, it
identifies itself by AE Title. AE Titles must be unique within a DICOM
environment and cannot exceed 16 characters. In production environments, AE
Title assignment is a formal administrative process — new modalities must be
registered with their AE Title in the PACS configuration before they can
communicate. Misconfigured or duplicate AE Titles are one of the most common
causes of DICOM connectivity failures in clinical environments. Phase 2
Scenario 5 deliberately induces a duplicate AE Title conflict to demonstrate
this failure mode.

**CIIP knowledge connection:** AE Title management, DICOM addressing model,
modality registration workflow, association negotiation.

## DICOM Port Configuration

**Lab action:** Confirmed `DicomPort : 4242` in `orthanc.json`. Validated
port was listening via `nc -zv 192.168.175.128 4242`.

**CIIP domain:** DICOM Standard — Network Communication Model

**Clinical context:**
DICOM does not have a single standardized port. Port 104 is the IANA-assigned
well-known port for DICOM and is used by most production PACS systems. Port
11112 is commonly used for DICOM TLS. Orthanc defaults to 4242 for lab and
development environments to avoid requiring root privileges for ports below
1024. In production environments, the DICOM port assignment must be consistent
across all registered modalities and must be explicitly configured in firewall
rules. The C-Move service class adds particular complexity to firewall
configuration because it involves three-party traffic — the requesting SCU,
the responding SCP, and the storage destination which may be a third system.
Understanding DICOM port behavior at the firewall level is a CIIP-relevant
operational skill covered extensively in Phase 2.

**CIIP knowledge connection:** DICOM networking model, port assignments,
firewall configuration for DICOM, C-Move three-party traffic implications.

## PostgreSQL Backend — Index vs Storage Decision

**Lab action:** Configured `EnableIndex: true` and `EnableStorage: false` in
`postgresql.json`. DICOM files stored on disk, metadata index stored in
PostgreSQL.

**CIIP domain:** PACS Architecture — Storage Architecture and Data Management

**Clinical context:**
This is one of the most important architectural decisions in PACS deployment.
The DICOM index — patient demographics, study metadata, series information,
instance UIDs — is small, frequently queried, and benefits enormously from a
relational database with proper indexing. Storing this in PostgreSQL enables
fast C-FIND queries, efficient wildcard searches, and transactional integrity.
The actual DICOM files — pixel data, waveforms, structured reports — are large
binary objects that relational databases handle poorly. Storing DICOM files in
PostgreSQL as binary blobs creates significant performance problems at scale
and is generally avoided in production environments. The correct architecture
separates concerns: relational database for the index, filesystem or object
storage for the files. This is the production-realistic choice and it is what
we implemented. In large enterprise PACS environments this distinction maps to
separate storage tiers — fast SSD for the index database, high-capacity NAS
or SAN for DICOM file storage.

**CIIP knowledge connection:** PACS storage architecture, index vs storage
separation, database backend selection, storage tier design.

## Trigram Index Creation

**Lab action:** Orthanc log on first startup showed:
`Trying to enable trigram matching on the PostgreSQL database to speed up
wildcard searches. Trigram index has been created.`

**CIIP domain:** DICOM Standard — C-FIND Query Performance

**Clinical context:**
DICOM C-FIND queries frequently use wildcard matching — searching for `SMITH*`
in PatientName, or `2026*` in StudyDate. Without a trigram index, these
wildcard searches require a full table scan across every record in the
database. In a PACS with millions of studies this is unacceptably slow. The
PostgreSQL trigram extension creates an index structure that enables efficient
substring and wildcard matching. Orthanc automatically creates this index on
first startup. In production environments with large existing datasets, this
index creation can take significant time and should be planned as a maintenance
window activity. The fact that it completed in under 50ms in our lab confirms
the database is empty — in a production migration with years of historical data
this same operation could take hours. Understanding query performance
optimization is directly relevant to PACS administration roles where
radiologists and technologists complain about slow worklist or query
performance.

**CIIP knowledge connection:** DICOM C-FIND query model, query performance
optimization, database indexing for PACS, operational impact of index
maintenance.

## RemoteAccessAllowed Configuration

**Lab action:** Changed `RemoteAccessAllowed` from `false` to `true` in
`orthanc.json`.

**CIIP domain:** Healthcare Security — PACS Access Control

**Clinical context:**
Orthanc defaults to localhost-only access for the REST API — a secure default
that prevents any external access to the web interface or REST API without
explicit configuration. In a production environment this default would be
appropriate for an Orthanc instance that is only accessed locally by other
services on the same host. In our lab and in most production deployments,
remote access is required for radiologist workstations, PACS administrators,
and modality integrations. Enabling remote access without simultaneously
enabling authentication would expose the entire DICOM archive — all patient
studies, all metadata — to anyone on the network without credentials. This is
a real vulnerability in improperly configured PACS systems. The combination of
`RemoteAccessAllowed: true` and `AuthenticationEnabled: true` is the minimum
required configuration for any production-facing Orthanc deployment. Phase 4
security exercises build on this foundation by adding TLS, reverse proxy
authentication hardening, and role-based access controls.

**CIIP knowledge connection:** HIPAA Technical Safeguards — Access Control,
PACS security configuration, authentication requirements for clinical systems.

## Authentication Configuration

**Lab action:** Enabled `AuthenticationEnabled: true` and configured admin
credentials in `credentials.json`. REST API confirmed `IsHttpServerSecure:
true` in `/system` response.

**CIIP domain:** Healthcare Security — HIPAA Technical Safeguards

**Clinical context:**
HIPAA requires covered entities to implement technical safeguards that control
access to electronic protected health information (ePHI). An unauthenticated
PACS REST API exposes every patient study, every demographic record, and every
DICOM metadata tag — all of which constitute ePHI — to anyone with network
access. This is not a theoretical risk. Unauthenticated PACS systems have been
discovered exposed on the public internet containing millions of patient
records. Orthanc's HTTP Basic Authentication is a starting point but has
significant limitations — credentials are transmitted in base64 encoding which
is trivially reversible without TLS. The `IsHttpServerSecure: true` response
from the API reflects Orthanc's assessment that authentication is enforced,
not that transport encryption is active. Real security requires both
authentication and TLS. This distinction is addressed explicitly in Phase 4.
The clinical operational constraint here is that authentication cannot break
modality connectivity — CT scanners and MRI machines that send studies via
DICOM C-Store are not affected by REST API authentication, but vendor service
accounts and administrative tools must be updated when credentials change.

**CIIP knowledge connection:** HIPAA Security Rule Technical Safeguards, ePHI
protection, authentication vs encryption distinction, access control in
clinical environments.

## Storage Architecture — LVM Decision

**Lab action:** Deliberately chose simple partition layout over LVM during
Ubuntu install. Documented as a conscious tradeoff.

**CIIP domain:** PACS Administration — Storage Management and Capacity Planning

**Clinical context:**
Production PACS archive servers universally use LVM or equivalent volume
management. DICOM archive storage growth is difficult to predict — a single
trauma activation can generate hundreds of CT slices, and a busy radiology
department may ingest thousands of studies per day. LVM enables online volume
expansion without taking the archive offline — critical in a clinical
environment where PACS downtime directly impacts patient care. A PACS
administrator who cannot expand storage without a maintenance window creates
operational risk. The simple partition layout chosen for this lab is
appropriate for a development environment but would be operationally
inappropriate for production. Documenting this deliberate tradeoff demonstrates
awareness of production requirements while maintaining lab simplicity. This is
the kind of contextual reasoning that differentiates a candidate who understands
clinical operational constraints from one who only knows the technology.

**CIIP knowledge connection:** PACS storage management, capacity planning,
archive lifecycle management, operational availability requirements.

## DICOM Information Model — Statistics Endpoint

**Lab action:** Executed `curl .../statistics` and received `CountPatients`,
`CountStudies`, `CountSeries`, `CountInstances` all returning zero.

**CIIP domain:** DICOM Standard — Information Model Hierarchy

**Clinical context:**
The four-level hierarchy returned by the statistics endpoint — Patient, Study,
Series, Instance — is the foundational DICOM information model. Every DICOM
operation maps to this hierarchy. A C-FIND query can target any level — find
all studies for a patient, find all series in a study, find a specific
instance. A C-Move retrieve operates at the study or series level. Storage
commitment is tracked at the instance level. Understanding this hierarchy is
prerequisite knowledge for every DICOM operation in Phase 2. In production
PACS environments, this hierarchy also maps to clinical workflow — a patient
has multiple visits (studies), each visit may involve multiple imaging
sequences (series), and each sequence produces multiple images (instances).
PACS administrators use these counts for capacity planning, storage
forecasting, and archive lifecycle management. A PACS with 500,000 studies
and 50 million instances has very different performance and storage
characteristics than one with 50,000 studies and 5 million instances.

**CIIP knowledge connection:** DICOM information model, Patient-Study-Series-
Instance hierarchy, C-FIND query levels, PACS capacity planning.

## Orthanc Explorer — Clinical Workflow Context

**Lab action:** Validated Orthanc Explorer accessible at port 8042. Page
confirmed as `"ORTHANC-PRIMARY » Lookup studies"`.

**CIIP domain:** PACS Architecture — Archive and Viewer Components

**Clinical context:**
Orthanc Explorer is a lightweight web-based interface for PACS administration
and basic study lookup. In production environments it is not used for
diagnostic reading — that requires a dedicated diagnostic viewer with
calibrated displays, hanging protocols, and measurement tools. Orthanc Explorer
maps to the archive/broker component of PACS architecture rather than the
diagnostic workstation component. Understanding the distinction between archive
management interfaces and diagnostic reading workstations is fundamental to
PACS architecture knowledge. In Phase 1 our architecture has the archive
component only. Phase 1 Node 2 adds a second archive node. A diagnostic viewer
(Weasis or RadiAnt) is added later to represent the workstation component and
complete the basic three-component PACS architecture — archive, broker, viewer.

**CIIP knowledge connection:** PACS architecture components, archive vs viewer
distinction, diagnostic workstation requirements, clinical workflow dependencies
on PACS.

## Minimized Server Install

**Lab action:** Selected Ubuntu Server (minimized) during installation.
Documented that `ping`, `nslookup` and other utilities were absent and
required explicit installation.

**CIIP domain:** Healthcare IT Operations — System Hardening and Attack
Surface Reduction

**Clinical context:**
Clinical imaging servers in production environments follow a minimal
installation principle — only software explicitly required for the clinical
function is installed. This reduces the attack surface, simplifies patching,
and reduces the risk of software conflicts affecting clinical availability.
Healthcare IT environments are frequent ransomware targets precisely because
PACS systems often run on older, poorly maintained operating systems with
unnecessary software installed. The Ubuntu minimized install reflects this
production principle. The operational implication is that administrators must
explicitly install tools they need rather than assuming availability — exactly
the behavior we encountered when `ping` and `nslookup` were absent. In a
clinical environment, even installing diagnostic utilities on a production
PACS server may require a change management approval process.

**CIIP knowledge connection:** Healthcare cybersecurity, system hardening,
attack surface reduction, change management in clinical environments.

## Management Node — rhcontrol

**Lab action:** Deployed a dedicated RHEL 10 management node for SSH
administration rather than using the VMware console directly.

**CIIP domain:** Healthcare IT Operations — Vendor and Administrative Access
Management

**Clinical context:**
In production clinical environments, direct console access to PACS servers is
reserved for emergency break-glass scenarios. All routine administration is
performed via SSH from dedicated management workstations or jump hosts. This
architecture provides an audit trail of administrative activity, enforces
credential management, and separates the management network from the clinical
imaging network. Vendor remote access — a particularly sensitive area in
healthcare IT — is handled through dedicated jump servers with session
recording, time-limited access, and explicit approval workflows. The rhcontrol
node in our lab simulates this production pattern. In Phase 4 the vendor remote
access threat model is addressed explicitly, and the management node
architecture becomes the foundation for understanding compensating controls for
PACS administrative access.

**CIIP knowledge connection:** PACS administration workflow, vendor access
management, jump host architecture, HIPAA audit controls, administrative
access separation.
