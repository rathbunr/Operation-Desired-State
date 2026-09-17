# Operation Desired State — Image Builder Charter

## Purpose

`infra-image-builder` is the authoritative repository for the bootstrap-critical RHEL seed-image factory used by Operation Desired State.

Its scope is intentionally narrow.

## Mandatory artifacts

The image factory must produce exactly two mandatory bootstrap base images:

1. **RHEL 10 IdM base image**
2. **Satellite bootstrap base image using the RHEL release explicitly supported by the selected Satellite version**

For the current reference design, Red Hat Satellite 6.19 uses RHEL 9 x86_64. The factory must not assume that Satellite Server runs on RHEL 10 merely because other bootstrap roles do.

These images exist only to cross the greenfield bootstrap gap before Satellite is available.

They are not intended to become a general-purpose image catalog for the rest of the environment.

## Architectural boundary

The image factory owns:

- installation and configuration of the Image Builder service/tooling on the designated builder host;
- deterministic definition of the IdM and Satellite bootstrap images;
- repeatable artifact generation;
- artifact integrity/provenance generation and validation;
- documentation of required inputs and build prerequisites;
- validation that the resulting images boot successfully and meet the declared base-image contract;
- production and retention of operational-acceptance and policy-compliance evidence for each releasable artifact.

The image factory does **not** own:

- IdM server configuration;
- Satellite application configuration;
- AD integration or trust;
- general RHEL fleet provisioning;
- application-specific configuration;
- steady-state package/content lifecycle for later RHEL systems.

Those responsibilities belong to their platform-specific repositories and management systems.

## Desired bootstrap handoff

```text
infra-image-builder
        |
        +--> RHEL 10 IdM base image
        |        |
        |        +--> deploy IdM VM
        |                 |
        |                 +--> IdM automation/configuration
        |
        +--> Satellite bootstrap base image
                 |
                 +--> deploy Satellite VM
                          |
                          +--> Satellite automation/configuration
                                   |
                                   +--> Satellite becomes RHEL provisioning/content authority
                                            |
                                            +--> AAP orchestrates remaining desired-state builds
```

Once Satellite and AAP are operational, normal RHEL systems should be provisioned and managed through that steady-state control plane rather than by expanding the seed image factory.

## Validation and release lifecycle

Bootstrap artifacts must pass a defined validation lifecycle before they are treated as releasable.

```text
Build
  -> Artifact Validation
  -> Provisioning Validation
  -> Operational Acceptance
  -> Policy Compliance
  -> Release Evidence
  -> Desired-State Integration
```

The lifecycle is intentionally technology-agnostic at the charter level:

- **Artifact Validation** proves provenance, integrity, format, and immutable build identity.
- **Provisioning Validation** proves that the selected delivery mechanism can instantiate the artifact correctly. The current reference design uses Hyper-V Generation 2 VHDX deployment; future reference designs may use UEFI HTTPS Boot or other supported mechanisms.
- **Operational Acceptance** proves that the resulting operating system is healthy, supportable, persistent across reboot, fit for the declared environment, and ready for the intended product installer.
- **Policy Compliance** independently evaluates the selected security policy such as CIS Level 1 Server, CIS Level 2 Server, or DISA STIG. A successful build or operational-acceptance run does not by itself prove compliance.
- **Release Evidence** binds provenance, artifact integrity, provisioning results, operational acceptance, compliance results, and exceptions to the exact artifact identity.
- **Desired-State Integration** validates the completed service in the full target environment when dependent capabilities are available. Examples include AD trust, DNS delegation, YubiKey/PIV, firewall policy, PKI, and cross-system orchestration.

Desired-State Integration may occur incrementally after initial image release when dependencies are intentionally unavailable during bootstrap qualification. Deferred checks must remain visible as warnings with a documented reason and follow-up point rather than disappearing from the evidence set.

## Operational-acceptance model

Operational Acceptance is divided into three logical classes:

1. **Image-intrinsic acceptance (40A)** — properties attributable to the artifact itself and expected to remain valid independent of the deployment network. Artifact defects block release.
2. **Environment fitness (40B)** — deployment-specific dependencies such as routing, DNS, time synchronization, CA trust, and authoritative content access. Failures block that deployment but do not invalidate an otherwise valid artifact.
3. **Product readiness (40C)** — thin, version-pinned prerequisites required before the supported IdM or Satellite installer is invoked.

Persistence-sensitive checks must be evaluated before and after a controlled reboot rather than represented only by a single generic reboot flag.

Warnings are permitted only when they are explicitly non-blocking. Every warning must carry a structured reason and explanatory note so a reviewer can determine why it did not block release or deployment.

## Base-image design principle

The two seed images should contain only what is necessary to create a stable, supportable operating-system substrate for their respective platform installers.

They should not embed environment-specific identity state, secrets, trust relationships, or application configuration that properly belongs in later automation.

IPv6 kernel support must remain enabled in bootstrap images unless a documented supported-product exception requires otherwise. Routable IPv6 configuration is not required merely because the kernel stack is enabled.

### IdM base image

Expected characteristics include:

- RHEL 10;
- declared storage/partitioning layout appropriate for IdM;
- network bootstrap capability;
- SSH/remote-management readiness;
- Python and automation prerequisites;
- time synchronization client capability;
- required operating-system packages or prerequisites for the later IdM installation workflow;
- IPv6 kernel support enabled;
- Hyper-V guest compatibility where applicable;
- bootstrap administrative access;
- no preconfigured IdM realm/domain;
- no AD dependency during initial bootstrap qualification;
- no Satellite dependency during initial bootstrap qualification;
- no embedded environment secrets.

### Satellite base image

Expected characteristics include:

- the exact RHEL major/minor platform supported by the selected Satellite release;
- for the current Satellite 6.19 reference design, RHEL 9 x86_64;
- declared storage/partitioning layout appropriate for Satellite;
- network bootstrap capability;
- SSH/remote-management readiness;
- Python and automation prerequisites;
- time synchronization client capability;
- required operating-system prerequisites for the later Satellite installation workflow;
- IPv6 kernel/loopback support consistent with current Satellite requirements;
- Hyper-V guest compatibility where applicable;
- bootstrap administrative access;
- no preconfigured Satellite application state;
- no dependency on an already-existing Satellite server;
- no embedded environment secrets.

The exact supported RHEL/Satellite version pairing must be validated from current Red Hat product documentation as part of the implementation project rather than assumed.

## Provisioning boundary after Satellite

After Satellite is operational, it becomes the authoritative RHEL content/provisioning platform for subsequent managed Linux systems.

AAP assists by orchestrating deployment and configuration workflows, but the seed-image factory should not become the normal provisioning mechanism for those systems.

This yields a deliberate control-plane transition:

```text
Seed Image Factory
        -> IdM + Satellite bootstrap
        -> Satellite content/provisioning authority
        -> AAP orchestration
        -> Remaining RHEL infrastructure
```

Future downstream provisioning may use UEFI HTTPS Boot or other supported Satellite provisioning mechanisms. Provisioning-specific validation may differ from VHDX/Hyper-V validation, while the common operating-system acceptance and policy-compliance contracts should remain reusable.

## Initial engineering objective

The first implementation project for `infra-image-builder` should compare the current/RHIS-derived legacy image-bootstrap approach with current supported Image Builder mechanisms where appropriate.

The comparison should use equivalent IdM and Satellite base-image requirements and evaluate:

- reproducibility;
- product supportability;
- external dependencies;
- closed/disconnected-environment suitability;
- automation complexity;
- artifact integrity/signing support;
- Hyper-V deployment behavior;
- maintainability;
- ability to operate before Satellite exists.

The selected implementation should be based on evidence rather than age or novelty of the tooling.

## Acceptance criteria

For each mandatory image:

1. Build from version-controlled definitions.
2. Produce deterministic/repeatable output to the extent supported by the tooling.
3. Generate and validate integrity metadata before use.
4. Provision successfully through the selected supported delivery mechanism.
5. Pass image-intrinsic operational acceptance.
6. Pass deployment-environment fitness for the intended bootstrap environment.
7. Pass the applicable version-pinned product-readiness checks.
8. Pass an independent compliance scan against the selected security profile and retain the evidence.
9. Produce release evidence that binds all results to the exact image version, build digest, source revision, and artifact digest.
10. Require no pre-existing Satellite or AAP service where the bootstrap sequence does not yet provide those capabilities.
11. Contain no undocumented environment-specific state or secrets.
12. Record intentionally deferred desired-state integration checks as warnings with reason, note, owner/follow-up context, and non-blocking status.

The IdM image must successfully hand off to standalone IdM automation.

The Satellite image must successfully hand off to Satellite installation/configuration automation and ultimately establish the steady-state RHEL provisioning control plane.
