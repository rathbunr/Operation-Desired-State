# Operation Desired State — Project Decomposition Draft

## Purpose

Operation Desired State is the program-level control plane. It should not absorb every implementation concern into one project.

Once the desired architecture state is enumerated, implementation work should be decomposed into discrete projects with focused scope, explicit dependencies, authoritative repositories, validation criteria, and reusable project prompts.

Backup/data protection is one such supporting project and should remain bounded rather than dominating the core architecture effort.

## Program approach

The preferred sequence is:

1. Enumerate the desired architecture state.
2. Establish component and service dependencies.
3. Identify authoritative repositories and current implementation coverage.
4. Identify gaps between current state and desired state.
5. Group gaps into coherent implementation projects.
6. Create a project prompt for each project.
7. Execute each project independently using the standard lifecycle.
8. Feed results, evidence, exceptions, and updated dependencies back into Operation Desired State.
9. Validate the end-to-end reconstruction runbook.

## Standard project package

Each project should eventually contain or reference:

- project name
- objective
- in-scope systems/capabilities
- out-of-scope items
- current-state evidence
- desired-state definition
- dependencies and prerequisites
- authoritative repositories
- expected deliverables
- rollback/recovery expectations where relevant
- dry-run/check/diff method where available
- runtime validation
- functional validation
- idempotency/convergence validation
- documentation updates
- acceptance criteria
- known gaps/deferred items
- reusable ChatGPT/AI project prompt

## Candidate project domains

These are placeholders until the desired architecture is fully enumerated.

### Foundation / bootstrap
- management workstation desired state
- seed-node/bootstrap process
- recovery media lifecycle
- physical hardware inventory and BIOS/UEFI baseline

### Network
- pfSense desired state and rebuild
- Cisco switching desired state and rebuild
- VLAN/routing architecture
- DNS forwarding/resolution architecture
- DHCP architecture
- VPN/remote access architecture

### Virtualization
- Hyper-V host build and lifecycle
- virtual switching/network attachment
- VM provisioning conventions

### Identity / trust
- Active Directory deployment and configuration
- Red Hat IdM deployment and configuration
- AD/IdM trust
- DNS integration
- Windows/Unix time hierarchy
- PKI reconstruction model

### Management platforms
- Satellite deployment/configuration
- AAP deployment/configuration
- MECM deployment/configuration
- Windows Admin Center
- Zabbix

### Endpoint / workload desired state
- Windows workstation/laptop reimage and configuration
- RHEL base OS desired state
- security baselines
- patching
- application/platform deployments

### Security / compliance
- SCAP/SCC/Nessus workflows
- Heimdall/compliance pipeline
- EDA/remediation flows
- endpoint security tooling

### Data protection (supporting project)
- Must Back Up versus Nice to Back Up classification
- NAS data classification
- Git repository protection
- automated offsite/cloud replication

This domain is intentionally separate from infrastructure reconstruction. System/VM backup is not a primary recovery strategy for the lab.

### Documentation / reference architecture
- physical topology
- logical network/VLAN topology
- dependency diagrams
- identity/DNS/time/PKI relationships
- recovery flow
- enterprise-facing reference-architecture artifacts

## Project lifecycle

Unless a project requires a justified exception, use:

`assess → capture state if needed → rollback/recovery if needed → dry run → review delta → apply → runtime validate → functional validate → verify idempotency → closeout evidence`

## Current priority

The immediate priority is **not** to launch the candidate projects above.

The immediate priority is to enumerate the desired architecture state sufficiently to determine the correct project boundaries, dependency order, and authoritative repository ownership.
