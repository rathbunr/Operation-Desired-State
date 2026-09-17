# Operation Desired State — Validation and Release Lifecycle Draft

## Purpose

Operation Desired State requires each rebuildable system or deployable artifact to progress through explicit validation gates before it is treated as accepted desired state.

The lifecycle is technology-agnostic and applies whether a system is instantiated from a virtual-disk artifact, provisioned through UEFI HTTPS Boot, installed through another supported mechanism, or reconstructed by a future reference design.

```text
Build
  -> Artifact Validation
  -> Provisioning Validation
  -> Operational Acceptance
  -> Policy Compliance
  -> Release Evidence
  -> Desired-State Integration
```

## 1. Build

Build converts version-controlled intent and approved inputs into a candidate artifact or system state.

A successful build is not evidence of operational correctness or policy compliance.

## 2. Artifact Validation

Artifact Validation proves that the candidate output is the artifact that was intended to be built.

At minimum, applicable implementations should retain:

- source revision;
- build identity/version;
- declared role or purpose;
- target platform/release;
- security profile;
- build timestamp;
- cryptographic digest;
- build status;
- provenance sufficient to reproduce or explain the artifact.

## 3. Provisioning Validation

Provisioning Validation proves that the selected delivery mechanism can instantiate the candidate correctly without altering its intended identity or violating declared platform requirements.

Provisioning-specific tests belong here. Examples include virtual-disk placement and attachment, firmware mode, Secure Boot/vTPM configuration, or UEFI HTTPS Boot chain and installer handoff.

Provisioning mechanisms may differ between reference designs while later operating-system and policy controls remain common.

## 4. Operational Acceptance

Operational Acceptance proves that the instantiated system is healthy, supportable, persistent, fit for its declared environment, and ready for the next supported product or configuration activity.

Operational Acceptance uses three logical classes.

### 40A — Image/System Intrinsic

These checks evaluate properties attributable to the built system itself. A failure is an artifact/system defect and blocks release.

Typical areas include:

- boot/firmware state;
- operating-system identity;
- image/clone identity hygiene;
- SELinux;
- FIPS and crypto policy;
- kernel configuration;
- IPv6 kernel availability where required by supported platform behavior;
- systemd health;
- logging persistence;
- time-client configuration;
- package/database health;
- storage/layout/headroom;
- access-policy correctness;
- absence of embedded sensitive material;
- persistence across controlled reboot.

Persistence-sensitive controls should be evaluated before and after reboot and compared rather than represented by a single generic reboot check.

### 40B — Environment Fitness

These checks evaluate whether an otherwise valid system can operate in the current deployment environment.

Typical areas include:

- routing;
- DNS;
- time synchronization;
- CA trust;
- authoritative content/repository access;
- required service endpoints.

A 40B failure should block the affected deployment without automatically invalidating the underlying artifact.

### 40C — Product Readiness

These checks are intentionally thin and version-pinned to the supported prerequisites of the product or role that will consume the operating-system substrate.

Generic RHEL health checks should not be duplicated here. Product installers remain authoritative for their own detailed preflight behavior where practical.

## Operational result classes

Stage 40 evidence uses the following semantic outcomes:

- **PASS** — requirement satisfied.
- **FAIL** — intrinsic defect; artifact/system must not graduate.
- **BLOCK-DEPLOY** — artifact may remain valid, but the current environment or product prerequisite prevents this deployment from proceeding.
- **WARN** — intentionally non-blocking observation that requires explanation.

Every WARN must include a structured reason code and explanatory note. A warning without context is invalid evidence.

Recommended warning reason codes include:

- `dependency_not_available`
- `environment_not_in_scope`
- `optional_capability`
- `known_nonblocking_condition`
- `temporary_waiver`

Warnings used for intentionally deferred desired-state integration must state why the dependency is unavailable and where the validation must be revisited.

## 5. Policy Compliance

Policy Compliance is independent from Operational Acceptance.

Operational Acceptance may verify that required controls are active, such as SELinux enforcing or FIPS mode enabled, but it must not claim that these individual observations prove benchmark compliance.

Policy Compliance evaluates the complete selected baseline, such as CIS Level 1 Server, CIS Level 2 Server, DISA STIG, or another approved policy, using the authoritative compliance mechanism for that reference design.

Compliance evidence should retain, as applicable:

- benchmark/profile identity and version;
- scanner/content version;
- scan timestamp;
- machine-readable results;
- human-readable report;
- pass/fail summary;
- tailoring identity/hash;
- exceptions or waivers;
- exact artifact/system identity to which the scan applies.

Operational Acceptance and Policy Compliance should consume common desired-state policy variables where they overlap so that duplicated policy definitions do not drift.

## 6. Release Evidence

Release Evidence binds the complete validation chain to the exact artifact or reconstructed system identity.

A release-evidence bundle should include or reference:

- provenance and build identity;
- artifact integrity;
- provisioning-validation result;
- operational-acceptance evidence;
- policy-compliance evidence;
- warnings/exceptions and rationale;
- implementation revisions used for validation;
- cryptographic bundle digest;
- release decision/status.

Mature implementations should cryptographically sign release evidence where practical.

## 7. Desired-State Integration

Desired-State Integration validates the completed service against the wider environment once required dependencies are available.

Examples may include identity trust, DNS delegation, PKI, hardware-token authentication, firewall policy, application integration, orchestration, monitoring, lifecycle management, and other cross-system behavior.

This phase may be incremental. Bootstrap qualification must not expand indefinitely merely because later dependencies are not yet online.

When a material integration check is intentionally deferred, the earlier evidence must retain a WARN with sufficient notes to ensure a later reviewer understands why it was non-blocking and where it must be revisited.

## Provisioning-method independence

The lifecycle deliberately separates provisioning-specific checks from common runtime controls.

For example:

```text
Virtual disk / hypervisor path
  -> hypervisor-specific provisioning validation
  -> common Operational Acceptance
  -> common Policy Compliance

UEFI HTTPS Boot path
  -> firmware/network-boot/install-handoff validation
  -> common Operational Acceptance
  -> common Policy Compliance
```

The implementation collectors may differ, but equivalent requirements should retain stable semantic control IDs wherever practical.

## Evidence principle

A successful automation run alone is not evidence of conformance.

Every gate should produce machine-readable evidence sufficient to determine:

1. what was tested;
2. against which expected state;
3. what was observed;
4. whether the result blocks the artifact, blocks only the deployment, or is a documented warning;
5. which implementation performed the validation;
6. which exact artifact/system the result applies to;
7. what must be revisited later if a dependency was intentionally deferred.
