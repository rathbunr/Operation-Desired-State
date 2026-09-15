# Operation Desired State — Charter Draft

## Purpose

Operation Desired State exists to turn the RITCSUSA lab from a collection of successfully automated systems into a deliberately defined, reproducible, recoverable, and continuously convergent infrastructure program.

The program will coordinate existing domain repositories rather than replace them.

## Working problem statement

The environment already contains substantial automation across networking, virtualization, identity, lifecycle management, endpoint configuration, security tooling, patching, compliance, and operations. However, the end-state definition, cross-platform dependency model, reconstruction sequence, completeness criteria, and evidence required to declare the environment fully desired-state managed are not yet defined as one program.

The central question is therefore not simply:

> Is there automation for this platform?

It is:

> Can the intended environment be reconstructed and continuously converged from controlled desired-state sources, with dependencies, secrets, validation, recovery, and exceptions explicitly understood?

## Defined reconstruction starting condition

For a catastrophic lab loss, the assumed human starting point is a newly provisioned laptop or management workstation with:

- Internet access
- access to the authoritative Git repositories
- access to required external secrets, credentials, licenses, subscriptions, and trust material
- replacement hardware that may differ from the original hardware

Operation Desired State must therefore be reasonably hardware-agnostic where practical. Hardware-specific configuration may still exist, but the reconstruction model must distinguish required capabilities from assumptions about exact replacement models.

A documented **seed node procedure** is a critical program artifact. The seed node exists to bootstrap the first managed infrastructure node before the normal management stack is available.

## Provisional greenfield reconstruction sequence

The current working sequence after catastrophic loss is:

1. Provision a management laptop/workstation and obtain Git/secrets access.
2. Build the seed node using explicit bootstrap instructions.
3. Use the seed node to image/provision the first Hyper-V host.
4. Restore or rebuild foundational network services, including pfSense and required switching configuration, at the point necessary to establish stable infrastructure networking.
5. Provision Red Hat IdM.
6. Provision Red Hat Satellite.
7. Provision Ansible Automation Platform.
8. With core automation/identity/content-management services available, reprovision Active Directory domain controllers and MECM.
9. Reconstruct remaining infrastructure, management systems, security services, and workloads according to documented dependencies.
10. Validate functionality and verify convergence for each restored node/device.

This sequence is provisional. Dependency analysis may move pfSense, switching, DNS, PKI, AD, or other services earlier where evidence shows they are required to bootstrap IdM, Satellite, AAP, or the first Hyper-V workload layer.

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

Because replacement hardware may differ after a catastrophic event, the desired-state model should capture both:

1. the **capability requirement** (for example, NIC count/speed, virtualization features, storage, VLAN/trunk support), and
2. the **current implementation state** on the presently installed hardware.

Human-readable state documentation is a required recovery artifact even when automation exists.

## Reconstruction target

The preferred recovery target is the current intended lab state as closely as practical.

Exact hardware identity is not required when equivalent replacement hardware is necessary. However, stable logical identities and architecture should be preserved where appropriate, including:

- hostnames
- DNS names
- IP addressing
- domains/realms
- VLANs and routing intent
- service roles
- policy
- trust relationships
- security controls
- required certificates/trust anchors

Where exact restoration is neither useful nor technically reasonable, the program should define the acceptable functional-equivalence boundary explicitly.

## Data and backup boundary

Routine restoration of platform databases is **not** a primary objective of Operation Desired State.

The preferred model is to rebuild infrastructure platforms from desired state rather than depend on database-level restoration merely to recover configuration.

Personal and irreplaceable data is different. The program must define or reference an **N-tier backup and recovery strategy for personal data** so that loss of infrastructure does not imply loss of personal information.

Data classification for recovery should therefore distinguish at least:

- reproducible infrastructure/configuration state
- trust anchors and secrets that must survive or be securely recoverable
- personal/irreplaceable data requiring multi-tier backup
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
- Externalize secrets from repositories.
- Document unsupported gaps and intentional exceptions explicitly.
- Treat complete reconstruction as a system dependency problem, not merely a collection of independent playbooks.
- Prefer hardware abstraction and capability requirements over unnecessary dependence on exact physical models.
- Preserve useful human-readable recovery documentation even when the same state is automated.
- Treat seed/bootstrap procedures as first-class infrastructure dependencies.

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

It is not intended to become the implementation monorepo.

## Success statement

Operation Desired State will be considered successful when **each in-scope individual host, node, or device and its intended configuration can be reconstructed from code and documented dependencies to full functionality, with configuration convergence validated and no undocumented critical recovery dependency**.

At the whole-lab level, success means a catastrophic loss can be approached from a newly provisioned management system, authoritative Git repositories, externalized secrets/trust material, replacement hardware of suitable capability, and documented seed-node procedures, then progressed through the dependency chain until the intended lab is reconstituted.

Personal/irreplaceable data must be recoverable through a separately defined multi-tier backup model; platform database restoration is not required merely to reproduce infrastructure configuration.
