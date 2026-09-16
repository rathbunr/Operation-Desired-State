# Operation Desired State — Architecture Draft

This document captures the initial whole-lab recovery architecture and the relationship between the ODS charter, validated reference designs, and implementation repositories. It is intentionally high level and will be refined as activity ownership, contracts, and dependencies are assessed.

## Architecture layers

```mermaid
flowchart TD
    C[ODS Architecture Charter<br/>Vision, activities, outcomes, contracts,<br/>controls, validation, evidence] --> R[Validated Reference Designs<br/>Technology realization + traceability + proof]
    R --> I1[Implementation Activity / Repository A]
    R --> I2[Implementation Activity / Repository B]
    R --> I3[Implementation Activity / Repository N]

    I1 --> E[Retained Validation Evidence]
    I2 --> E
    I3 --> E
    E --> R

    T1[Technology Choice 1] --> I1
    T2[Technology Choice 2] --> I2
    T3[Technology Choice N] --> I3
```

The charter defines architectural intent. Reference designs prove concrete implementation patterns. Implementation repositories perform bounded activities and exchange explicit contracts.

Technology may be replaced beneath a reference design or by creating a new reference design without changing the charter unless the architectural intent itself changes.

## Activity and contract principle

ODS decomposes end-to-end outcomes into activities with explicit producer/consumer boundaries.

For example:

```text
Artifact Production Activity
        |
        | validated artifact + provenance contract
        v
Infrastructure Deployment Activity
        |
        | instantiated compute + runtime identity
        v
Configuration / Service Establishment Activity
        |
        | validated functional state
        v
Continuous Convergence / Compliance Activity
```

A product does not own an architectural activity merely because it participates in the current implementation. The reference design maps each activity to the selected technology and implementation repository.

## Reference-design validation loop

```mermaid
flowchart LR
    CR[Charter Requirement] --> DD[Design Decision]
    DD --> IR[Implementation Repository]
    IR --> VM[Validation Method]
    VM --> EV[Retained Evidence]
    EV --> VS[Validation State]
    VS --> DD
```

A successful implementation run does not, by itself, establish architectural conformance. Validation evidence must prove the applicable requirement.

## Catastrophic recovery control flow

The following is the current RITCSUSA reference-design realization of the higher-level recovery activity model.

```mermaid
flowchart TD
    A[New Windows workstation / laptop] --> B[Recover Edge profile and cloud access]
    B --> C[Access GitHub and critical cloud data]
    C --> D[Prepare external seed drive / recovery media]
    D --> E[Restore or replace network hardware]
    E --> F[Reconstitute firewall/routing capability]
    E --> G[Reconstitute switching capability]
    F --> H[Stable routed infrastructure network]
    G --> H
    H --> I[Provision first virtualization host]
    I --> J[Provision Linux identity capability]
    J --> K[Provision Linux lifecycle/content capability]
    K --> L[Provision automation/orchestration capability]
    L --> M[Rebuild AD domain controllers]
    L --> N[Rebuild Windows management capability]
    M --> O[Rebuild remaining Windows infrastructure]
    J --> P[Rebuild Linux infrastructure]
    K --> P
    L --> P
    O --> Q[Rebuild remaining services / workloads]
    P --> Q
    Q --> R[Restore NAS configuration]
    R --> S[Restore protected NAS data]
    S --> T[Runtime + functional validation]
    T --> U[Verify idempotency / convergence]
    U --> V[Recovery complete]
```

The associated reference design maps these capabilities to the currently selected RITCSUSA technologies. Those product choices are implementation decisions, not charter-level architectural mandates.

## Backup/control-plane concept

The working backup model uses four tiers. The exact cadence and retention may vary by data classification, but Tier 4 has an explicit requirement: **offsite/cloud replication must be automated and frequent** rather than dependent on manual copy operations.

```mermaid
flowchart LR
    SRC[Authoritative source / live data] --> T1[Tier 1: Primary]
    T1 --> T2[Tier 2: Local independent backup]
    T2 --> T3[Tier 3: Offline / removable copy]
    T1 --> T4[Tier 4: Automated frequent offsite / cloud copy]
    T2 --> T4
    T3 --> RV[Recovery validation]
    T4 --> RV

    GH[GitHub repositories] --> MIRROR[Automated local mirror]
    MIRROR --> OFFSITE[Automated frequent offsite backup]

    NAS[NAS protected data] --> LOCAL[Local backup]
    NAS --> CLOUD[Automated frequent offsite / cloud backup]

    CFG[Desired-state code + documentation] --> GH
    IMG[Periodic recovery images] --> RM[External recovery media]
```

### Tier definitions

- **Tier 1 — Primary:** active authoritative copy used by the workload or user.
- **Tier 2 — Local independent backup:** separate local copy that is not merely another view of the same storage failure domain.
- **Tier 3 — Offline/removable:** disconnected or removable copy intended to survive compromise, corruption, or broader local failure.
- **Tier 4 — Offsite/cloud:** geographically/failure-domain independent copy, **automatically pushed and refreshed frequently** according to the data class.

The target is not simply to possess four copies. Each tier must address a distinct failure mode, and restoration must be tested where practical.

For Git repositories, Operation Desired State should include or reference an Ansible-driven workflow that:

1. enumerates authoritative repositories,
2. creates or refreshes independent local mirrors,
3. pushes/mirrors them to an approved offsite target,
4. verifies the backup result,
5. reports failures clearly,
6. can itself be reconstructed from Git/documentation.

The same automation-first expectation applies to approved NAS data classes that require offsite retention.

## Architectural principles

- Technology implements the architecture; technology does not define the architecture.
- The charter defines intent, capabilities, activities, contracts, controls, validation, and evidence requirements.
- Validated reference designs map those requirements to technology-specific implementations.
- Implementation repositories remain authoritative for implementation details.
- Cross-activity integration should occur through explicit contracts rather than unnecessary repository coupling.
- Git and documentation are authoritative for reproducible configuration.
- Recovery images accelerate bootstrap but do not replace desired-state sources.
- Hardware replacement may be like-for-like or better; capability intent is more important than exact model identity.
- Platform databases and PKI continuity are not mandatory if services can be cleanly reconstructed.
- Personal/irreplaceable data and source repositories require N-tier backup.
- Tier 4 is an automated, frequent offsite/cloud process, not a manual archival activity.
- Backup success is not assumed from job completion alone; recovery/verification evidence is required.
- The recovery interface is a tested runbook and dependency flow, not necessarily a literal one-click process.
- Each component must be independently restorable and validated before the whole-lab flow can be considered proven.

## Required future diagrams

- charter-to-reference-design traceability
- activity and contract model
- reference-design implementation mappings
- physical topology
- VLAN/routing topology
- service dependency graph
- identity/DNS/time/PKI relationships
- management/control-plane dependencies
- backup/data classification and flow
- recovery runbook sequence with checkpoints
