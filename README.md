# Operation Desired State

**Working subtitle:** Deterministic Infrastructure Reconstruction and Continuous Convergence

Operation Desired State is the program-level control repository for defining, measuring, and coordinating the RITCSUSA lab's progression toward reproducible desired state.

This repository is **not** intended to become a monorepo for the implementation code. Existing infrastructure, security, operations, and platform repositories remain authoritative for their respective implementation domains.

## Working objective

Establish a version-controlled, evidence-based model of the RITCSUSA environment such that infrastructure can be reconstructed, validated, and continuously converged toward an explicitly defined desired state with minimal undocumented manual intervention.

The exact scope and completion criteria are intentionally still under definition.

## What this repository owns

- Program charter and scope
- Repository and platform inventory
- Dependency mapping
- Maturity model
- Current-state assessments
- Coverage and gap analysis
- Cross-repository architecture decisions
- Recovery/reconstruction model
- Validation standards
- Program roadmap and deferred work

## What this repository does not own

- Platform implementation code already maintained in domain repositories
- Secrets or credentials
- Copies of other repositories
- Generated runtime configuration as a substitute for authoritative desired state

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

This model is provisional and will be refined as scope is defined.

## Initial repository layout

```text
Operation-Desired-State/
├── README.md
├── docs/
│   ├── CHARTER-DRAFT.md
│   └── SCOPE-QUESTIONS.md
├── inventory/
│   └── repositories.yml
└── assessments/
    └── CURRENT-STATE.md
```

## Current phase

**Phase 0 — Baseline and Scope Definition**

The first phase is to inventory the existing repositories, identify authoritative versus supporting or experimental code, understand platform dependencies, define what "complete" means, and establish a bounded program scope before building cross-repository orchestration.
