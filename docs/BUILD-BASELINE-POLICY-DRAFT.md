# Operation Desired State — Build Baseline Policy Draft

## Purpose

This policy defines the minimum operating-system build baseline for RHEL systems produced by the Operation Desired State architecture.

It applies to:

- bootstrap-critical seed images produced by `infra-image-builder`;
- RHEL systems provisioned later through Satellite;
- systems configured or converged through Ansible/AAP.

Product-specific requirements may extend this baseline, but should not silently weaken it.

## 1. Security baseline

The default RHEL server security baseline is **CIS Level 1 Server**.

Requirements:

- new RHEL server builds must target the applicable CIS Level 1 Server benchmark for the deployed RHEL major/minor release;
- controls that conflict with a supported Red Hat product requirement must be documented as explicit exceptions rather than silently omitted;
- product-specific exceptions must include rationale, affected control, compensating control where appropriate, and validation evidence;
- hardening must be implemented declaratively and remain idempotent;
- compliance state must be validated after provisioning rather than inferred from successful automation execution.

CIS Level 1 is the baseline, not the complete security architecture. Product-specific security controls, identity policy, network controls, signed automation, and configuration validation remain independently required.

## 2. Storage and filesystem policy

Disk layout must be designed for **operational growth, logging, auditing, patching, rollback-safe package operations, and product data growth** rather than minimum installation requirements.

A build is not considered correctly sized merely because the operating system installs successfully.

### General principles

- use LVM where supported and appropriate to preserve growth flexibility;
- leave deliberate free capacity in the volume group unless a product-specific layout has a better documented reason not to;
- isolate filesystems where doing so improves security, operational containment, or growth management;
- prevent ordinary log or audit growth from exhausting the root filesystem;
- provide sufficient capacity for package caching, updates, temporary files, and automation staging;
- size high-growth application data separately from the base OS wherever practical;
- filesystem and mount-option choices must remain compatible with the applicable CIS Level 1 controls and Red Hat product requirements;
- capacity assumptions must be documented and revisited as actual usage becomes known.

### Baseline filesystem intent

The generic RHEL server profile should explicitly evaluate separate storage for at least:

- `/`
- `/boot`
- `/boot/efi` where UEFI is used
- `/var`
- `/var/log`
- `/var/log/audit`
- `/tmp`
- `/home` where local home directories are materially used

The exact partition/LV sizes are **product profiles**, not universal constants.

### IdM profile

The IdM seed/build profile should reserve capacity for:

- directory-service growth;
- DNS data where IdM DNS is enabled;
- PKI/CA state where enabled;
- logs and audit data;
- package/update operations;
- normal database and service growth.

IdM should not be sized only to the current lab population. The layout should permit growth without requiring disruptive repartitioning.

### Satellite profile

Satellite requires a dedicated product-specific capacity model.

Its seed/build profile must account for, at minimum:

- operating-system growth;
- Satellite application state;
- database growth;
- content/repository growth;
- metadata growth;
- logs and audit data;
- package/update operations;
- temporary working space used during synchronization, publication, export/import, and maintenance operations where applicable.

High-growth Satellite content must not be allowed to consume the root filesystem.

Exact mount points and sizing will be defined from the supported Satellite architecture and the intended RITCSUSA content lifecycle rather than copied from the generic RHEL profile.

## 3. Kdump policy

**Kdump is disabled on all managed systems by policy.**

Requirements:

- seed images must not enable kdump;
- Satellite-provisioned systems must converge to kdump disabled;
- AAP configuration must not inadvertently re-enable it;
- validation should verify the declared disabled state after build/convergence.

This is an explicit local architecture decision and should be documented as such wherever a security or product baseline expects a different default.

## 4. Secrets and password handling

**Passwords and other reusable secrets must never be stored in plaintext in Git.**

Requirements:

- passwords at rest must be encrypted;
- plaintext passwords must not be embedded in playbooks, variables, templates, image blueprints, Kickstarts, scripts, documentation, manifests, or generated seed artifacts;
- secrets must not be committed to Git even temporarily with the expectation that they will be removed later;
- bootstrap images must not contain reusable environment credentials;
- secrets required during automation must be supplied at execution time from an approved encrypted secret source or encrypted artifact;
- generated logs and debug output must not expose secrets;
- automation should use `no_log` or equivalent protections where sensitive values could otherwise be emitted;
- private signing keys must not reside in source repositories or seed media;
- public verification keys may be distributed with signed automation where appropriate.

The long-term architecture should prefer managed machine/service identity, certificates, Kerberos, hardware-backed credentials, and short-lived/JIT credentials over standing passwords wherever practical.

## 5. Image-builder boundary

`infra-image-builder` is responsible for producing the two mandatory bootstrap-critical RHEL base images:

1. IdM base image
2. Satellite base image

These images must implement the applicable portions of this baseline but must **not** contain environment-specific identity state or reusable secrets.

The image factory should establish:

- storage layout;
- operating-system package baseline;
- CIS Level 1-compatible base hardening;
- kdump disabled;
- bootstrap networking/SSH prerequisites;
- automation prerequisites;
- integrity/provenance artifacts.

Application identity, realm/domain configuration, service credentials, trusts, subscriptions, and other environment-specific state belong to later automation stages.

## 6. Validation requirements

Every image/build profile should eventually provide automated validation for:

- expected filesystem/LV layout;
- free-space reserve and filesystem health;
- mount options required by policy;
- CIS Level 1 compliance state and documented exceptions;
- kdump disabled state;
- absence of plaintext or embedded reusable credentials;
- SSH/automation readiness;
- package/update readiness;
- artifact hash/signature verification where applicable.

Successful image creation alone is not sufficient evidence of compliance with this policy.

## 7. Open design items

The following remain to be defined during the `infra-image-builder` and platform-specific projects:

- exact IdM filesystem/LV sizes;
- exact Satellite filesystem/LV sizes;
- deliberate VG free-space reserve percentage or minimum;
- filesystem choices and mount options;
- supported encryption-at-rest mechanism for secrets used during bootstrap;
- approved secret source for the temporary seed Ansible environment;
- specific CIS exceptions required by IdM or Satellite, if any;
- automated post-build compliance and storage validation implementation.
