# Operation Desired State — Charter Draft

## Purpose

Operation Desired State exists to turn the RITCSUSA lab from a collection of successfully automated systems into a deliberately defined, reproducible, recoverable, and continuously convergent infrastructure program.

The program will coordinate existing domain repositories rather than replace them.

## Secondary objective — Reference Architecture

Operation Desired State also serves as a practical reference-architecture proving ground.

The intent is to demonstrate that a small-scale lab can implement disciplined desired-state engineering across networking, virtualization, identity, lifecycle management, automation, endpoint management, security, compliance, backup, validation, and recovery in a way that is credible enough to inform larger enterprise architecture.

The guiding idea is effectively **"if you build it, they will come"**: build and validate the architecture first, then use the working implementation, documentation, dependency model, maturity evidence, and reconstruction exercises as a concrete reference when proposing similar patterns elsewhere.

This does **not** mean the home lab is assumed to map one-for-one onto an enterprise environment. The value is in the architecture patterns, control boundaries, sequencing, validation methods, recovery model, and evidence produced. Enterprise adoption would still require scale, availability, governance, regulatory, organizational, and product-specific analysis.

A successful program should therefore produce artifacts that are useful in two contexts:

1. **RITCSUSA operational recovery and desired-state management**
2. **A reusable reference architecture and demonstrator for enterprise discussions**

## Working problem statement

The environment already contains substantial automation across networking, virtualization, identity, lifecycle management, endpoint configuration, security tooling, patching, compliance, and operations. However, the end-state definition, cross-platform dependency model, reconstruction sequence, completeness criteria, and evidence required to declare the environment fully desired-state managed are not yet defined as one program.

The central question is therefore not simply:

> Is there automation for this platform?

It is:

> Can the intended environment be reconstructed and continuously converged from controlled desired-state sources, with dependencies, validation, recovery, and exceptions explicitly understood?

## Defined reconstruction starting condition

For a catastrophic lab loss, assume **the entire lab may be lost and rebuilt from scratch**.

The human starting point is a newly provisioned Windows workstation/laptop with:

- Internet access
- Microsoft Edge profile access sufficient to recover the user's surviving cloud-stored credentials
- access to the authoritative Git repositories
- access to cloud-hosted personal/critical information such as OneDrive or an equivalent service
- suitable replacement hardware, which may differ from the original hardware
- an external drive that can be used for seed/bootstrap media and image staging

No in-lab password store, platform database, or PKI state is assumed to survive.

Operation Desired State must therefore be reasonably hardware-agnostic where practical. Hardware-specific configuration may still exist, but the reconstruction model must distinguish required capabilities from assumptions about exact replacement models.

## Seed and bootstrap model

The initial seed node is the user's Windows workstation/laptop with an external drive.

This workstation is expected to provide the first recovery control point before normal lab management services exist.

In addition to Git-based desired-state recovery, the program should evaluate maintaining a periodically refreshed **standalone Windows recovery media set** capable of accelerating bootstrap. Candidate seed artifacts include:

- DC-01 image
- DC-02 image
- MECM image
- Windows Admin Center image
- a known-good Windows workstation/laptop image

These artifacts are accelerators, not the sole authoritative recovery mechanism. They should be treated as periodically refreshed recovery media, likely on an annual cadence or another defined interval, and tested in isolation sufficiently to establish that they remain bootable and useful.

The authoritative long-term configuration source remains version-controlled desired state and documentation, not stale images.

## Provisional greenfield reconstruction sequence

The current working sequence after catastrophic loss is:

1. Provision a Windows management workstation/laptop.
2. Recover access to Git and cloud-hosted critical information.
3. Prepare seed/bootstrap media from the workstation and external drive.
4. Restore or replace foundational network hardware, using like-for-like or better-capability devices where practical.
5. Reconstitute pfSense and switching sufficiently to establish stable infrastructure networking.
6. Provision the first Hyper-V host from the seed environment.
7. Provision Red Hat IdM.
8. Provision Red Hat Satellite.
9. Provision Ansible Automation Platform.
10. Reprovision Active Directory domain controllers, MECM, Windows Admin Center, and remaining Windows infrastructure using the now-available platform stack and/or validated recovery media where advantageous.
11. Reconstruct remaining infrastructure, management systems, security services, and workloads according to documented dependencies.
12. Validate functionality and verify convergence for each restored node/device.

This sequence is provisional. Dependency analysis may move specific DNS, PKI, identity, imaging, or management functions earlier where evidence shows they are required for bootstrap.

## Physical infrastructure boundary

Physical infrastructure is in scope.

This includes, at minimum, documenting and where practical automating the desired state for:

- Hyper-V hosts
- pfSense
- Cisco switching
- Synology/NAS infrastructure
- physical networking
- firmware/BIOS configuration where it materially affects functionality or security
- other physical appliances required to reconstitute the lab

If a firewall, switch, or other appliance is destroyed, the intended approach is to replace it with the same model or a better-capability model where practical. Exact hardware identity is not required.

Where replacement hardware differs materially, AI-assisted migration may be used to translate documented intent to the new platform. The architecture must therefore capture **intent and capability requirements**, not only device-specific CLI syntax.

The desired-state model should capture both:

1. the **capability requirement** (for example, NIC count/speed, virtualization features, storage, VLAN/trunk support), and
2. the **current implementation state** on the presently installed hardware.

Human-readable state documentation is a required recovery artifact even when automation exists.

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

## Data, PKI, and backup boundary

Routine restoration of platform databases is **not** a primary objective of Operation Desired State.

The preferred model is to rebuild infrastructure platforms from desired state rather than depend on database-level restoration merely to recover configuration.

PKI history and continuity are also not considered irreplaceable. A catastrophic rebuild may establish new CA identities and reissue certificates as needed.

Personal and irreplaceable data is different. The program must define or reference an **N-tier backup and recovery strategy for personal/critical data** so that loss of infrastructure does not imply loss of important information.

Cloud-hosted storage such as OneDrive may serve as one tier, but the program should ultimately avoid treating a single cloud location as the only copy of irreplaceable data.

Data classification for recovery should therefore distinguish at least:

- reproducible infrastructure/configuration state
- cloud-recoverable credentials and account access
- personal/irreplaceable data requiring multi-tier backup
- disposable PKI state that may be regenerated
- operational/history data that may be disposable

## Working objective

Create an evidence-based program model that can answer:

1. What systems and capabilities are in scope?
2. Which repository is authoritative for each capability?
3. What dependencies exist between platforms and repositories?
4. What manual or out-of-band steps still exist?
5. What validation proves a component is functioning as intended?
6. What evidence proves configuration is convergent and reproducible?
7. What is required to reconstruct the environment from a defined starting condition?
8. What criteria must be met before Operation Desired State can be declared complete?
9. Which architecture patterns are sufficiently validated to serve as a reference model beyond the lab?
10. Which recovery accelerators (for example, standalone images) are worth maintaining in addition to source-controlled rebuild automation?

## Guiding principles

- Diagnose from evidence before changing configuration.
- Prefer supported, declarative, reproducible configuration.
- Treat implementation repositories as authoritative for their domains.
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
- Treat images/backups as recovery accelerators, not substitutes for authoritative desired state.
- Assume in-lab PKI and platform databases can be lost unless a future requirement explicitly changes that boundary.

## Program role

This repository is intended to become the control plane for:

- architecture and scope
- repository catalog
- authoritative ownership mapping
- dependency graph
- maturity assessment
- cross-repository validation standards
- reconstruction sequencing
- recovery expectations
- gap management
- program roadmap
- seed/bootstrap documentation
- personal-data recovery requirements and references
- recovery-media lifecycle and test requirements
- reference-architecture documentation and evidence

It is not intended to become the implementation monorepo.

## Success statement

Operation Desired State will be considered successful when **each in-scope individual host, node, or device and its intended configuration can be reconstructed from code and documented dependencies to full functionality, with configuration convergence validated and no undocumented critical recovery dependency**.

At the whole-lab level, success means a catastrophic loss can be approached from a newly provisioned Windows management system, authoritative Git repositories, surviving cloud-accessible credentials/information, suitable replacement hardware, an external seed drive, and documented bootstrap procedures, then progressed through the dependency chain until the intended lab is reconstituted.

Existing platform databases and PKI identities do not need to survive if the corresponding service can be cleanly rebuilt and returned to full intended functionality.

Personal/irreplaceable data must be recoverable through a separately defined multi-tier backup model.

As a secondary success measure, the program should leave behind a defensible reference architecture: documented patterns, dependency models, validation evidence, recovery procedures, and lessons learned that can be used to inform enterprise architecture discussions without claiming that the lab itself is an enterprise production design.
