# Operation Desired State — Validated Reference Design Standard

## Purpose

This document defines the minimum structure for an Operation Desired State reference design.

A reference design translates the technology-agnostic ODS charter into a concrete implementation pattern and proves that the selected implementation satisfies the applicable charter requirements.

A reference design is not considered validated merely because its automation completes successfully.

## Required relationship to the charter

Every material design decision must trace to one or more charter requirements, principles, capability requirements, activity contracts, control objectives, or evidence requirements.

The expected chain is:

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

## Required sections

Each reference design should include the following sections.

### 1. Scope

Define:

- the capability or workflow being implemented;
- the applicable ODS activities;
- the intended operating context;
- explicit exclusions;
- assumptions and prerequisites.

### 2. Charter requirements implemented

List the applicable charter requirements and principles.

Requirements should be stated in capability or outcome terms rather than copied as product-specific implementation statements.

### 3. Design decisions

For each material decision, document:

- the requirement being addressed;
- the selected design;
- why the design satisfies the requirement;
- meaningful alternatives considered where relevant;
- constraints or tradeoffs introduced by the decision.

### 4. Technology realization

Map the reference design to the selected implementation technologies and repositories.

Technology belongs here, below the charter layer.

A reference design may contain product-specific requirements when those requirements arise from the selected realization. Those requirements do not automatically become charter requirements.

### 5. Activity and contract model

Identify:

- producer activities;
- consumer activities;
- authoritative ownership boundaries;
- inputs and outputs;
- artifact or API contracts;
- validation gates between activities;
- prohibited coupling.

Implementation repositories should align to activities and contracts rather than being merged solely because they participate in one end-to-end workflow.

### 6. Validation model

Define how each material requirement is proven.

Validation should distinguish where applicable:

- static validation;
- dry-run/check/plan validation;
- artifact integrity validation;
- runtime validation;
- functional validation;
- security/compliance validation;
- reboot or persistence validation;
- failure-path validation;
- idempotency/convergence validation;
- reconstruction/recovery validation.

### 7. Evidence

Identify retained evidence sufficient to reproduce or audit the validation result.

Evidence may include:

- source Git revision;
- input digest;
- artifact digest;
- machine-readable manifests;
- build records;
- test results;
- compliance scan results;
- runtime validation records;
- configuration snapshots where appropriate;
- signed release metadata where maturity requires it.

A successful job status alone is not sufficient evidence when the requirement concerns runtime behavior, functional behavior, security posture, or provenance.

### 8. Validation state

Use explicit validation states:

- **Proven** — evidence demonstrates the requirement is satisfied in the stated reference-design context.
- **Partially Proven** — some required validation is complete but material validation remains.
- **Failed** — current evidence demonstrates that the requirement is not satisfied.
- **Not Yet Validated** — implementation or validation has not yet produced sufficient evidence.
- **Exception Accepted** — the design intentionally deviates from a requirement under a documented exception and risk decision.

Do not use **Proven** for assumptions, inferred behavior, successful syntax checks, successful service starts, or successful automation completion unless those results directly prove the applicable requirement.

### 9. Limitations and portability

Document:

- lab-specific assumptions;
- scale limitations;
- availability limitations;
- hardware dependencies;
- vendor/product dependencies;
- security or regulatory limitations;
- areas requiring enterprise-scale validation;
- which components may be substituted without changing the charter-level design.

### 10. Reconstruction and convergence

Where applicable, document how the design is reconstructed from its defined starting condition and how convergence is proven after repeated execution.

## Inline validation expectation

Reference designs should keep validation criteria adjacent to the design decisions they prove whenever practical.

The preferred pattern is not:

```text
Design document
        +
separate test document with no traceability
```

It is:

```text
Design requirement
        |
        +-- implementation mapping
        +-- validation method
        +-- evidence location
        +-- current validation state
```

This allows the reference design to show both the intended pattern and whether that pattern has actually been demonstrated.

## Reference design versus implementation repository

The reference design owns the mapping and proof.

The implementation repository owns the implementation.

ODS should link to implementation repositories and consume their evidence, not duplicate their automation.

For example:

```text
Reference design requirement:
  A validated compute artifact must be instantiated without losing artifact identity.

Implementation activity A:
  image factory produces an artifact plus provenance manifest.

Contract:
  artifact digest + manifest schema.

Implementation activity B:
  virtualization deployment consumes the artifact and verifies the digest before deployment.

Validation:
  deployed instance is traced back to the exact producer artifact and passes runtime validation.
```

The implementation technologies used for activities A and B may change while the charter requirement remains stable.

## Conformance rule

A technology implementation is conformant to an ODS reference design only when:

1. applicable charter requirements are identified;
2. design decisions are documented;
3. implementation ownership is explicit;
4. cross-activity contracts are explicit;
5. required validation has been executed;
6. retained evidence supports the result;
7. material limitations and exceptions are documented.

## Evolution rule

- Change the **charter** when architectural intent changes.
- Change a **reference design** when the implementation pattern or technology realization changes.
- Change an **implementation repository** when implementation details change without altering the reference-design contract.

This separation is intended to let technology evolve without unnecessarily destabilizing the architecture above it.
