# Operation Desired State

**Working subtitle:** Deterministic Infrastructure Reconstruction and Continuous Convergence

Operation Desired State is the architecture and evidence repository for defining how infrastructure is reconstructed, validated, and continuously converged toward an explicitly declared desired state.

The architecture is intentionally technology-agnostic at the charter level. Technology is selected beneath the charter through validated reference designs and bounded implementation repositories.

This repository is **not** intended to become a monorepo for implementation code or the mandatory orchestration engine. Existing infrastructure, security, operations, and platform repositories remain authoritative for their respective implementation activities.

## Architectural model

ODS uses three layers:

1. **Architecture Charter** — defines vision, activity boundaries, required outcomes, control objectives, contracts, validation expectations, evidence requirements, and convergence principles.
2. **Validated Reference Designs** — map charter requirements to concrete technology implementations and retain evidence proving whether the design satisfies those requirements.
3. **Implementation Repositories** — contain the product- or platform-specific automation that performs individual activities and consumes/produces explicit contracts.

The governing principle is:

> **Technology implements the architecture; technology does not define the architecture.**

Reference designs may evolve as technology changes. The charter should change only when architectural intent changes.

## Working objective

Establish a version-controlled, evidence-based architecture such that infrastructure can be reconstructed, validated, and continuously converged toward an explicitly defined desired state with minimal undocumented manual intervention.

The RITCSUSA lab is the initial proving ground and reference implementation for the architecture.

## What this repository owns

- Architecture charter and scope
- Activity and capability model
- Validated reference designs
- Charter-to-design-to-implementation traceability
- Repository and platform inventory
- Dependency and contract mapping
- Maturity model
- Current-state assessments
- Coverage and gap analysis
- Cross-repository architecture decisions
- Recovery/reconstruction model
- Validation and evidence standards
- Program roadmap and deferred work

## What this repository does not own

- Platform implementation code already maintained in domain repositories
- Secrets or credentials
- Copies of other repositories
- Generated runtime configuration as a substitute for authoritative desired state
- A requirement that one specific orchestration or infrastructure technology implement every activity

## Reference-design validation

A reference design is not considered validated because automation completed successfully.

The expected traceability chain is:

```text
Charter Requirement
        |
        v
Reference Design Decision
        |
        v
Implementation Activity / Repository
        |
        v
Validation Method
        |
        v
Retained Evidence
        |
        v
Validation State
```

See `docs/REFERENCE-DESIGN-STANDARD.md` for the required reference-design structure and conformance model.

## Initial maturity concept

The working maturity progression is:

1. Discovered
2. Documented
3. Desired State Defined
4. Automated
5. Dry-Run Validated
6. Applied
7. Runtime Validated
8. Functionally Validated
9. Idempotent / Convergent
10. Recoverable
11. Greenfield Reconstructable
12. Fully Orchestrated

This model is provisional and will be refined as scope and reference designs mature.

## Key architecture documents

```text
Operation-Desired-State/
├── README.md
├── docs/
│   ├── CHARTER-DRAFT.md
│   ├── REFERENCE-DESIGN-STANDARD.md
│   ├── ARCHITECTURE-DRAFT.md
│   └── SCOPE-QUESTIONS.md
├── inventory/
│   └── repositories.yml
└── assessments/
    └── CURRENT-STATE.md
```

## Current phase

The current phase is establishing the architecture baseline, decomposing activities and ownership boundaries, validating bootstrap and reconstruction patterns, and converting proven lab implementations into traceable reference designs beneath the charter.
