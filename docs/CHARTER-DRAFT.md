# Operation Desired State — Charter Draft

## Purpose

Operation Desired State exists to define a technology-agnostic architecture for reconstructing, validating, and continuously converging infrastructure toward an explicitly declared desired state.

The charter defines the enduring vision, activity model, control objectives, architectural constraints, interface contracts, evidence expectations, and convergence requirements. Technology is selected to implement that architecture; technology does not define the architecture.

The RITCSUSA lab is the initial proving ground for this model. It is intentionally a **bleeding-edge learning environment**. Failure is expected. Preserving running systems is not the objective; proving that systems and services can be reconstructed from controlled intent, implementation repositories, documented dependencies, and a minimal external control plane is.

Operation Desired State coordinates implementation domains rather than replacing them. Domain repositories remain authoritative for their implementation activities.

## Architectural hierarchy

Operation Desired State uses three distinct layers.

### 1. Architecture charter

The charter defines:

- the desired-state vision;
- activity boundaries;
- required outcomes;
- capability requirements;
- control objectives;
- cross-activity contracts;
- validation and evidence requirements;
- convergence expectations;
- recovery and reconstruction principles.

The charter should remain technology-agnostic wherever practical. It should describe **what must be true and what must be proven**, not mandate a product merely because that product is used in the current lab.

### 2. Validated reference designs

Reference designs translate charter requirements into concrete technology implementations.

A reference design is not merely an example architecture or product diagram. It is a **validated implementation pattern** that demonstrates, with retained evidence, that a selected technology stack can satisfy applicable charter requirements.

Each reference design must identify:

1. the charter requirements it implements;
2. the design decisions used to satisfy those requirements;
3. the implementation repositories or components that realize the design;
4. the validation methods used to test the design;
5. the retained evidence supporting the result;
6. known limitations, exceptions, and unproven assumptions;
7. the current validation state of each material requirement.

Reference designs may change as technology changes. The charter should change only when the architectural intent changes.

### 3. Implementation repositories

Implementation repositories contain the product- or platform-specific automation used to perform individual activities.

Repositories should align to activities and contracts rather than being combined simply because products participate in the same end-to-end workflow.

Examples include image construction, virtualization deployment, identity establishment, lifecycle management, configuration convergence, compliance validation, data protection, and monitoring.

Implementation repositories consume and produce explicit contracts. They should not require upstream architecture repositories to embed their implementation details.

## Reference-design conformance model

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

A reference design should use explicit validation states such as:

- Proven
- Partially Proven
- Failed
- Not Yet Validated
- Exception Accepted

A successful automation run alone is not proof of architectural conformance. Evidence must demonstrate that the implemented behavior satisfies the applicable charter requirement.

An implementation is considered conformant only when the associated reference design demonstrates that the applicable charter requirements are satisfied with retained validation evidence.

## Technology substitution principle

Technology is an implementation choice beneath the charter.

For example, the charter may require that a validated compute artifact be instantiated by an infrastructure-deployment activity while preserving artifact integrity and declared runtime requirements. One reference design may realize that requirement with Hyper-V; another may use VMware or another virtualization platform.

The architectural requirement remains stable while the implementation can change.

The same principle applies to identity, lifecycle management, orchestration, compliance, networking, storage, and other capabilities.

## Secondary objective — Reference Architecture

Operation Desired State also serves as a practical reference-architecture proving ground.

The intent is to demonstrate that a small-scale lab can implement disciplined desired-state engineering across networking, virtualization, identity, lifecycle management, automation, endpoint management, security, compliance, validation, recovery, and data protection in a way that is credible enough to inform larger enterprise architecture.

The RITCSUSA implementation is therefore a validated reference design beneath the higher-level charter, not the charter itself.

The guiding idea is effectively **"if you build it, they will come"**: define the architecture, implement it through bounded activities, validate those activities and their contracts, then use the working implementation, documentation, dependency model, maturity evidence, and reconstruction exercises as a concrete reference when proposing similar patterns elsewhere.

This does **not** mean the home lab is assumed to map one-for-one onto an enterprise environment. The value is in the architecture patterns, control boundaries, sequencing, validation methods, recovery model, and evidence produced. Enterprise adoption would still require scale, availability, governance, regulatory, organizational, and product-specific analysis.

A successful program should therefore produce artifacts that are useful in two contexts:

1. **RITCSUSA operational recovery and desired-state management**
2. **Reusable validated reference designs for enterprise architecture discussions**

## Working problem statement

The environment already contains substantial automation across networking, virtualization, identity, lifecycle management, endpoint configuration, security tooling, patching, compliance, and operations. However, the end-state definition, cross-platform dependency model, reconstruction sequence, completeness criteria, and evidence required to declare the environment fully desired-state managed are not yet defined as one program.

The central question is therefore not:

> Can this system be restored from a backup?

It is:

> Can the intended environment be reconstructed and continuously converged from controlled desired-state sources, with dependencies, validation, recovery steps, and exceptions explicitly understood?

## Defined reconstruction starting condition

For a catastrophic lab loss, assume **the entire lab may be lost and rebuilt from scratch**.

The human starting point is a newly provisioned Windows workstation/laptop with:

- Internet access
- Microsoft Edge profile access sufficient to recover the user's surviving cloud-stored credentials
- access to the authoritative Git repositories
- access to cloud-hosted personal/critical information such as OneDrive or an equivalent service
- suitable replacement hardware, which may differ from the original hardware
- an external drive that can be used for seed/bootstrap media and image staging if useful

No in-lab password store, platform database, PKI state, VM backup, or system-image backup is assumed to survive.

Operation Desired State must therefore be reasonably hardware-agnostic where practical. Hardware-specific configuration may still exist, but the reconstruction model must distinguish required capabilities from assumptions about exact replacement models.

## Seed and bootstrap model

The initial seed node is the user's Windows workstation/laptop with an external drive.

This workstation is expected to provide the first recovery control point before normal lab management services exist.

Standalone media or prebuilt images may be maintained as **optional bootstrap accelerators**, not as a system-backup strategy. Candidate seed artifacts could include:

- DC-01 bootstrap image
- DC-02 bootstrap image
- MECM bootstrap image
- Windows Admin Center bootstrap image
- a known-good Windows workstation/laptop image

If maintained, these artifacts exist only to shorten bootstrap time. They are not authoritative, are not required for recovery success, and must never become a dependency that substitutes for reproducible build/configuration code.

The authoritative long-term source remains version-controlled desired state and documentation.

## Provisional greenfield reconstruction sequence

The current working sequence after catastrophic loss is:

1. Provision a Windows management workstation/laptop.
2. Recover access to Git and cloud-hosted critical information.
3. Prepare any useful seed/bootstrap media from the workstation and external drive.
4. Restore or replace foundational network hardware, using like-for-like or better-capability devices where practical.
5. Reconstitute pfSense and switching sufficiently to establish stable infrastructure networking.
6. Provision the first virtualization host from the seed environment.
7. Provision the primary Linux identity capability.
8. Provision the Linux lifecycle/content-management capability.
9. Provision the primary automation/orchestration capability.
10. Reprovision Active Directory domain controllers, endpoint-management services, Windows administration services, and remaining Windows infrastructure using desired-state automation and/or optional bootstrap media where advantageous.
11. Reconstruct remaining infrastructure, management systems, security services, endpoints, and workloads according to documented dependencies.
12. Restore NAS configuration and data according to the separate backup/data-classification model.
13. Validate functionality and verify convergence for each restored node/device.

The associated RITCSUSA reference design maps these capability-level activities to the currently selected technologies and repositories.

This sequence is provisional. Dependency analysis may move specific DNS, PKI, identity, imaging, or management functions earlier where evidence shows they are required for bootstrap.

## Scope boundary

The program scope is intentionally broad.

### In scope

All lab devices and managed endpoints are in scope **except the printer as an actively managed recovery target**.

This includes:

- firewall/routing platforms
- switching
- virtualization hosts
- Windows domain controllers
- Linux identity services
- Linux lifecycle/content-management services
- automation/orchestration services
- endpoint-management services
- Windows administration services
- monitoring
- security and compliance platforms
- Windows and Linux infrastructure servers
- user workstation/laptop rebuild to defined desired state
- physical networking configuration
- BIOS/UEFI configuration
- firmware current-state inventory
- seed/bootstrap tooling
- NAS configuration recovery where technically practical

Specific products belong in reference designs and implementation repositories rather than being treated as architectural requirements unless a product-specific constraint is itself part of a defined reference design.

### Special case — NAS

The NAS is not treated like an ordinary disposable compute node because it holds recoverable data, but its **configuration should be reproducible or restorable as code/documented state where possible**.

The desired recovery exercise is:

1. protect NAS data according to policy;
2. wipe/reinitialize the NAS;
3. reconstruct NAS configuration;
4. restore protected data;
5. validate permissions, shares, identity integration, and service behavior.

NAS data itself must be classified so it is clear which information:

- must exist in an offsite/cloud tier;
- should have multiple local copies;
- can be regenerated or re-downloaded;
- should not be retained at all.

### Out of scope

- printer recovery as a managed desired-state target
- traditional system/VM backup as a recovery strategy for lab infrastructure
- preservation of historical platform databases solely for configuration recovery
- preservation of existing PKI identity solely for continuity

## Physical infrastructure boundary

Physical infrastructure is in scope.

If a firewall, switch, host, or other appliance is destroyed, the intended approach is to replace it with the same model or a better-capability model where practical. Exact hardware identity is not required.

Where replacement hardware differs materially, AI-assisted migration may be used to translate documented intent to the new platform. The architecture must therefore capture **intent and capability requirements**, not only device-specific CLI syntax.

The desired-state model should capture both:

1. the **capability requirement** (for example, NIC count/speed, virtualization features, storage, VLAN/trunk support), and
2. the **current implementation state** on the presently installed hardware.

Human-readable state documentation is a required recovery artifact even when automation exists.

## BIOS, firmware, and hardware lifecycle policy

BIOS/UEFI settings that materially affect operation or security must be documented and, where tooling permits, managed declaratively.

Firmware should be maintained at the latest appropriate supported revision as an operational objective.

The project should therefore record **current firmware state** for inventory and troubleshooting, but should not treat an old firmware version as a desired-state target merely because it was previously installed.

Recovery documentation should capture:

- current device model and hardware role;
- current firmware/BIOS versions for inventory;
- required BIOS/UEFI settings;
- required hardware capabilities;
- supported update method;
- post-update validation expectations.

## Reconstruction target

The preferred recovery target is the current intended lab state as closely as practical.

Exact hardware identity is not required when equivalent replacement hardware is necessary. Stable logical identities and architecture should be preserved where useful, including:

- hostnames
- DNS names
- IP addressing
- domains/realms
- VLANs and routing intent
- service roles
- policy
- trust relationships
- security controls

PKI continuity is **not** a hard recovery requirement. Existing CA identities, certificate databases, and historical certificate state may be lost and regenerated if rebuilding the environment cleanly is simpler and safer.

Where exact restoration is neither useful nor technically reasonable, the program should define the acceptable functional-equivalence boundary explicitly.

## Data, Git, and backup boundary

Operation Desired State deliberately separates **rebuildable systems** from **irreplaceable data**.

### Rebuildable systems

Infrastructure nodes, VMs, operating systems, platform databases, and PKI history are not protected through a traditional backup objective. Their protection mechanism is reproducibility:

- desired-state code;
- provisioning automation;
- configuration automation;
- documented dependencies;
- tested reconstruction procedures;
- runtime and functional validation;
- verified convergence.

A failed or destroyed lab system should normally be rebuilt rather than restored from a system backup.

### Irreplaceable data

Personal and other truly irreplaceable data must use an **N-tier backup and recovery strategy** independent of infrastructure rebuildability.

NAS data must be classified so the program can distinguish:

- irreplaceable data requiring local and offsite copies;
- source/configuration repositories requiring independent copies;
- regenerable/downloadable data;
- transient lab data that may be discarded;
- data that should not be retained at all.

### Git repositories

GitHub is the primary hosted source for important repositories, but it must not be the only surviving copy.

The program should include or reference Ansible-driven workflows for:

- mirroring/backing up GitHub repositories to one or more secondary local targets;
- automatically and frequently pushing approved repository/data sets to an offsite/cloud destination;
- validating backup freshness and recoverability;
- reporting failures and stale backup tiers.

Tier 4 offsite/cloud protection is explicitly expected to be **automated and frequent**, with cadence determined by the importance and rate of change of the protected data.

Cloud-hosted storage such as OneDrive may serve as one tier, but the program should not treat a single cloud location as the only copy of irreplaceable data.

## Orchestration and runbook objective

Operation Desired State should define a **single recovery flow and activity dependency model**, but it does not itself need to be the orchestration engine and does not require an unrealistic literal one-click rebuild.

The target is a tested, version-controlled runbook that:

1. defines dependency order;
2. identifies manual checkpoints;
3. links to the authoritative implementation repository/playbook for each activity;
4. records required inputs and prerequisites;
5. includes runtime and functional validation;
6. includes recovery/rollback expectations where relevant;
7. verifies idempotency/convergence where supported;
8. can be exercised progressively and periodically;
9. ultimately demonstrates that every in-scope component can be reconstructed to full functionality, excluding irreplaceable data handled by the separate backup model.

A selected automation platform may orchestrate increasing portions of this flow. The architecture defines the workflow requirements and contracts; the automation technology is a reference-design implementation choice.

## Architecture visualization requirement

The program must maintain a visual representation of the environment and its dependency model.

The initial preferred format is **Mermaid** because it is text-based, version-controlled, reviewable, and easy to keep adjacent to the architecture documentation.

Visio may also be used for presentation-quality or enterprise-facing diagrams where it adds value, but the repository should retain a text-based diagram source whenever practical.

At minimum, the visualization set should eventually include:

- activity and contract model;
- reference-design mappings;
- physical/network topology;
- logical VLAN and routing model;
- core service dependency graph;
- catastrophic-recovery/bootstrap sequence;
- identity/DNS/time/PKI relationships;
- management/control-plane dependencies;
- protected-data flow and storage tiers.

## Working objective

Create an evidence-based program model that can answer:

1. What capabilities and activities are in scope?
2. What charter requirements apply to each activity?
3. Which validated reference designs implement those requirements?
4. Which repository is authoritative for each implementation activity?
5. What dependencies and contracts exist between activities and repositories?
6. What manual or out-of-band steps still exist?
7. What validation proves a component is functioning as intended?
8. What evidence proves configuration is convergent and reproducible?
9. What is required to reconstruct the environment from a defined starting condition?
10. What criteria must be met before Operation Desired State can be declared complete?
11. Which architecture patterns are sufficiently validated to serve as a reference model beyond the lab?
12. Which optional bootstrap accelerators are worth maintaining in addition to source-controlled rebuild automation?
13. What data must survive, where is it stored, and how is its recoverability proven?
14. Can every in-scope node/device be independently reconstructed and validated?

## Guiding principles

- Technology implements the architecture; technology does not define the architecture.
- The charter defines intent, outcomes, activities, contracts, controls, validation, and evidence requirements at a technology-agnostic level wherever practical.
- Reference designs map charter requirements to concrete technology implementations and must be validated with retained evidence.
- Reference designs may evolve as technology changes without requiring the charter to change unless architectural intent changes.
- Repositories should align to activities and contracts, not be combined merely because products participate in the same workflow.
- Implementation repositories remain authoritative for their implementation domains.
- Failure is expected; unrecoverable configuration drift is not.
- Rebuildability is preferred over system-backup dependence.
- Code and documented intent are the primary recovery mechanisms for lab systems.
- Diagnose from evidence before changing configuration.
- Prefer supported, declarative, reproducible configuration.
- Do not duplicate implementation code into this program repository.
- Use dry-run, check, diff, or plan workflows where available.
- Validate runtime state after change.
- Validate function, not only syntax or service state.
- Verify idempotency/convergence when tooling supports it.
- Establish rollback or recovery proportional to the scenario and risk.
- For greenfield/disposable rebuild scenarios, prefer recoverability over preserving disposable state.
- Document unsupported gaps and intentional exceptions explicitly.
- Treat complete reconstruction as a system dependency problem, not merely a collection of independent playbooks.
- Prefer hardware abstraction and capability requirements over unnecessary dependence on exact physical models.
- Preserve useful human-readable recovery documentation even when the same state is automated.
- Treat seed/bootstrap procedures as first-class infrastructure dependencies.
- Separate demonstrated architecture patterns from assumptions that require enterprise-scale validation.
- Treat optional images/media as bootstrap accelerators, not substitutes for authoritative desired state.
- Assume in-lab PKI and platform databases can be lost unless a future requirement explicitly changes that boundary.
- Treat data classification and recoverability as distinct from infrastructure reconstruction.
- Prefer automated, testable data-protection flows over undocumented manual copies.
- Keep architecture diagrams version-controlled wherever practical.

## Program role

This repository is intended to become the architectural and evidence control plane for:

- architecture charter and scope
- activity model and capability requirements
- validated reference designs
- charter-to-design-to-implementation traceability
- repository catalog
- authoritative ownership mapping
- dependency and contract graph
- maturity assessment
- cross-repository validation standards
- reconstruction sequencing
- recovery expectations
- gap management
- program roadmap
- seed/bootstrap documentation
- personal-data recovery requirements and references
- Git repository protection requirements
- optional recovery-media lifecycle and test requirements
- architecture diagrams
- reference-design documentation and evidence

It is not intended to become the implementation monorepo or the mandatory orchestration engine.

## Success statement

Operation Desired State will be considered successful when **each in-scope individual host, node, device, managed endpoint, and required capability can be reconstructed from controlled intent and authoritative implementation sources to full functionality, with configuration convergence validated and no undocumented critical recovery dependency**.

At the whole-lab level, success means a catastrophic loss can be approached from a newly provisioned management system, authoritative Git repositories, surviving cloud-accessible credentials/information, suitable replacement hardware, external seed media where useful, and documented bootstrap procedures, then progressed through a tested recovery runbook until the intended lab is reconstituted.

Existing system images, VM backups, platform databases, and PKI identities do not need to survive if the corresponding service can be cleanly rebuilt and returned to full intended functionality.

Personal/irreplaceable data must be recoverable through a separately defined multi-tier backup model. Source repositories must also have N-tier protection so GitHub is not the sole surviving copy.

As a secondary success measure, the program should leave behind defensible validated reference designs: documented patterns, design decisions, diagrams, dependency models, implementation mappings, validation evidence, recovery procedures, and lessons learned that can be used to inform enterprise architecture discussions without claiming that the lab itself is an enterprise production design.
