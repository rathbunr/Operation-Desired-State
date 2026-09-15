# Operation Desired State — Architecture Draft

This document captures the initial whole-lab recovery/control-plane architecture. It is intentionally high level and will be refined as repository ownership and dependencies are assessed.

## Catastrophic recovery control flow

```mermaid
flowchart TD
    A[New Windows workstation / laptop] --> B[Recover Edge profile and cloud access]
    B --> C[Access GitHub and critical cloud data]
    C --> D[Prepare external seed drive / recovery media]
    D --> E[Restore or replace network hardware]
    E --> F[Reconstitute pfSense]
    E --> G[Reconstitute Cisco switching]
    F --> H[Stable routed infrastructure network]
    G --> H
    H --> I[Provision first Hyper-V host]
    I --> J[Provision Red Hat IdM]
    J --> K[Provision Red Hat Satellite]
    K --> L[Provision Ansible Automation Platform]
    L --> M[Rebuild AD domain controllers]
    L --> N[Rebuild MECM / Windows management]
    M --> O[Rebuild remaining Windows infrastructure]
    J --> P[Rebuild Linux infrastructure]
    K --> P
    L --> P
    O --> Q[Rebuild remaining services / workloads]
    P --> Q
    Q --> R[Restore NAS configuration]
    R --> S[Restore classified NAS data]
    S --> T[Runtime + functional validation]
    T --> U[Verify idempotency / convergence]
    U --> V[Recovery complete]
```

## Backup/control-plane concept

```mermaid
flowchart LR
    GH[GitHub repositories] --> LB[Local repository mirror]
    GH --> CB[Cloud / offsite repository backup]
    LB --> VB[Backup validation]
    CB --> VB

    NAS[NAS data] --> LC[Local backup tier]
    NAS --> OC[Approved offsite / cloud tier]
    LC --> DV[Data recovery validation]
    OC --> DV

    CFG[Desired-state code + documentation] --> GH
    IMG[Periodic recovery images] --> RM[External recovery media]
```

## Architectural principles

- Git and documentation are authoritative for reproducible configuration.
- Recovery images accelerate bootstrap but do not replace desired-state sources.
- Hardware replacement may be like-for-like or better; capability intent is more important than exact model identity.
- Platform databases and PKI continuity are not mandatory if services can be cleanly reconstructed.
- Personal/irreplaceable data and source repositories require N-tier backup.
- The recovery interface is a tested runbook and dependency flow, not necessarily a literal one-click process.
- Each component must be independently restorable and validated before the whole-lab flow can be considered proven.

## Required future diagrams

- physical topology
- VLAN/routing topology
- service dependency graph
- identity/DNS/time/PKI relationships
- management/control-plane dependencies
- backup/data classification and flow
- recovery runbook sequence with checkpoints
