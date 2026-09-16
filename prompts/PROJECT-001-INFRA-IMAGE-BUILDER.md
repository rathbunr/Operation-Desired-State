# Project 001 — RHEL 10 Bootstrap Image Factory

## Context

This project is part of **Operation Desired State (ODS)** for the RITCSUSA lab.

ODS objective:

> Make the RITCSUSA lab deterministically reconstructable and continuously convergent from version-controlled desired state, documented bootstrap dependencies, externally recoverable trust material, and automated validation.

This project owns the earliest RHEL image-production layer required before Satellite and Ansible Automation Platform (AAP) are available.

Repository:

- `rathbunr/infra-image-builder`

ODS architecture repository:

- `rathbunr/Operation-Desired-State`

## Project objective

Redesign and implement `infra-image-builder` as the authoritative RHEL 10 bootstrap image factory.

The factory must produce independently auditable RHEL 10 images for exactly two mandatory bootstrap roles:

1. Red Hat Identity Management (IdM)
2. Red Hat Satellite

Once Satellite is operational, Satellite becomes the authoritative RHEL lifecycle/content/provisioning platform and AAP assists with orchestration/configuration. `infra-image-builder` must not grow into a general-purpose image catalog for every downstream workload.

## Operating principles

- Diagnose and inspect existing code before replacing it.
- Prefer current, supported RHEL 10-native mechanisms.
- Treat Red Hat Infrastructure Standard (RHIS) as excellent reference material only; use RHIS-derived implementation directly only as a last-resort or where a specific pattern is clearly superior.
- One controlled change at a time unless safe to batch.
- Prefer supported, declarative, reproducible automation.
- Use Ansible dry-run/check/diff where meaningful.
- Validate runtime behavior after each substantive change.
- Verify idempotency/convergence.
- Preserve rollback/recovery before disruptive changes.
- Never weaken security controls just to make a build succeed.
- Do not commit plaintext secrets.
- For repository file replacements provided manually, use complete shell heredoc replacement commands rather than patches.

## Security policy

Default security baseline:

- CIS Level 1 Server

Additional supported targets:

- CIS Level 2 Server
- DISA STIG

Each role/profile combination must produce a distinct artifact so provenance and compliance evidence remain unambiguous.

Examples:

- IdM + CIS L1
- IdM + CIS L2
- IdM + DISA STIG
- Satellite + CIS L1
- Satellite + CIS L2
- Satellite + DISA STIG

The selected security profile must participate in the build/installation definition where the benchmark requires partitioning, mount options, packages, boot parameters, services, crypto policy, or other settings that should not be treated as a late remediation layer.

Do not assume a successful compose means a compliant system. Every image must be validated against its selected profile and produce retained evidence.

## Common baseline policy

Every generated image must satisfy the following unless a documented product-specific exception applies:

- RHEL 10
- SELinux enforcing
- firewalld enabled unless explicitly incompatible with supported product requirements
- required audit/logging services configured
- kdump disabled
- SSH only as required for bootstrap management
- bootstrap time synchronization available without depending on later AD/IdM integration
- only required bootstrap packages installed
- supported update path before Satellite exists
- no reusable plaintext passwords, API tokens, private keys, signing keys, vault passwords, or other secrets in Git or published image artifacts

Secrets must be injected at execution time from an approved encrypted source and suppressed from logs where appropriate.

Public keys/public certificates may be version-controlled when appropriate.

## Storage policy

Storage must be deliberately engineered rather than sized to installation minimums.

Requirements:

- UEFI/GPT-compatible layout suitable for Hyper-V Generation 2
- LVM where appropriate to allow controlled growth
- explicit capacity for `/var`, logging, audit logging, updates, and application growth
- separate handling of `/var/log` and `/var/log/audit` where required by selected policy
- unallocated VG capacity where it provides useful growth flexibility
- role-specific storage capacities
- Satellite content/database growth must not be constrained by a generic minimum OS layout
- additional VHDX disks are acceptable and preferred when product data growth warrants physical/logical separation

Exact filesystem/LV sizes must be derived from current RHEL 10 benchmark requirements, current Red Hat product requirements, expected lab scale, and documented growth margin. Do not blindly reuse RHEL 8/9 sizing.

## Artifact integrity and provenance

Every approved candidate image must record at minimum:

- source Git revision
- role
- security profile
- RHEL release/version
- build timestamp
- SHA-256 or stronger digest
- build result
- boot/runtime validation result
- compliance scan result

Desired mature state:

- signed release/manifest
- trusted verification key stored independently of the payload
- fail-closed verification before use

This project participates in the ODS signed automation and seed-integrity architecture.

## Image Mode evaluation

Evaluate both:

1. traditional/current RHEL 10 Image Builder path
2. RHEL Image Mode / bootc path

Do not assume Image Mode is the preferred bootstrap mechanism.

For each role, Image Mode must prove supportability and equivalence or superiority against:

- Red Hat support for IdM/Satellite on that lifecycle model
- Hyper-V deployment
- storage/partition requirements
- CIS/STIG implementation
- offline/closed-space suitability
- artifact signing/provenance
- update/rollback behavior
- downstream IdM/Satellite installation
- SCAP validation
- deterministic rebuildability

Use current authoritative Red Hat documentation when supportability or product requirements may have changed.

## Existing repository state

`infra-image-builder` already exists and contains older automation including:

- `setupimagebuilder.yml`
- `buildimage.yml`
- `collections/requirements.yml`
- `roles/image_builder_apache_ssl`
- `roles/image_builder_cockpit_ssl`
- `roles/image_builder_setup_sat`

The current code is historical/reference material. It was created around an earlier Image Builder design and must not be assumed to satisfy current RHEL 10, storage, secret-handling, CIS/STIG, integrity, or audit requirements.

The repository README and `docs/DESIRED-STATE.md` have already been updated to reflect the ODS target architecture.

Do not delete useful historical code until it has been inspected and either migrated, archived, or proven obsolete.

## Implementation boundary

`infra-image-builder` owns:

- Image Builder host preparation
- build dependencies
- role/profile image definitions
- storage layout implementation
- security-profile integration
- composition
- image boot/runtime tests
- SCAP validation
- artifact hash/signature metadata
- evidence production

It does not own:

- IdM realm/domain configuration
- IdM trust configuration
- Satellite application configuration
- Satellite content views/repos/lifecycle environments/activation keys/host groups
- steady-state RHEL provisioning after Satellite exists
- environment secrets

## Expected downstream flow

```text
Disposable seed node
      -> minimal Ansible capability
      -> Image Builder host
      -> signed/validated IdM image
      -> standalone IdM
      -> signed/validated Satellite image
      -> Satellite
      -> AAP
      -> Satellite + AAP provision remaining RHEL estate
```

## Initial work sequence

1. Inventory the complete current `infra-image-builder` repository.
2. Inspect all existing roles/playbooks/defaults/templates/tasks and identify reusable logic.
3. Verify the currently supported RHEL 10 Image Builder interfaces and `infra.osbuild` collection support from authoritative Red Hat/Ansible sources.
4. Compare traditional Image Builder versus Image Mode/bootc for this exact bootstrap use case.
5. Define the target repository structure before rewriting implementation code.
6. Define IdM and Satellite storage profiles from current compliance/product requirements.
7. Define security-profile variables and controlled choices.
8. Define secret-injection and bootstrap-account handling.
9. Implement builder-host setup.
10. Implement IdM image build first.
11. Boot and validate the IdM artifact on Hyper-V.
12. Run SCAP validation and retain evidence.
13. Verify rebuild reproducibility/idempotency.
14. Implement Satellite image using the same framework with Satellite-specific storage requirements.
15. Repeat validation.
16. Add image digest/manifest generation and then project/artifact signing.
17. Document the final bootstrap procedure and acceptance evidence.

## Acceptance criteria

The project is complete when a clean builder can, from version-controlled automation and externally supplied secrets/trust material:

1. prepare the RHEL 10 image-build environment;
2. build an IdM image for a selected supported security profile;
3. build a Satellite image for a selected supported security profile;
4. produce deterministic role/profile artifact metadata;
5. boot each image successfully as a fresh Hyper-V Generation 2 VM;
6. demonstrate required filesystem/storage layout and growth capacity;
7. demonstrate kdump disabled and required security controls active;
8. demonstrate no prohibited plaintext secrets in Git or the released image;
9. produce and retain SCAP evidence for the selected profile;
10. cryptographically hash/sign and verify the approved artifact/manifest;
11. prove each image is suitable for its downstream IdM or Satellite automation;
12. rerun the build and demonstrate reproducible/idempotent/convergent behavior to the practical extent supported by the image-build toolchain.

## Working style

Act as a senior Red Hat infrastructure architect/engineer. Keep the work evidence-driven and implementation-focused. Challenge circular dependencies and unsupported assumptions. Prefer native supported mechanisms over clever workarounds. Keep the bootstrap path intentionally small, auditable, and recoverable.
