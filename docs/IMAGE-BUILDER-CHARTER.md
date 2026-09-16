# Operation Desired State — Image Builder Charter

## Purpose

`infra-image-builder` is the authoritative repository for the bootstrap-critical RHEL seed-image factory used by Operation Desired State.

Its scope is intentionally narrow.

## Mandatory artifacts

The image factory must produce exactly two mandatory bootstrap base images:

1. **RHEL 10 IdM base image**
2. **RHEL 10 Satellite base image**

These images exist only to cross the greenfield bootstrap gap before Satellite is available.

They are not intended to become a general-purpose image catalog for the rest of the environment.

## Architectural boundary

The image factory owns:

- installation and configuration of the Image Builder service/tooling on the designated builder host;
- deterministic definition of the IdM and Satellite RHEL 10 base images;
- repeatable artifact generation;
- artifact integrity/provenance generation and validation;
- documentation of required inputs and build prerequisites;
- validation that the resulting images boot successfully and meet the declared base-image contract.

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
        +--> RHEL 10 Satellite base image
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

## Base-image design principle

The two seed images should contain only what is necessary to create a stable, supportable operating-system substrate for their respective platform installers.

They should not embed environment-specific identity state, secrets, trust relationships, or application configuration that properly belongs in later automation.

### IdM base image

Expected characteristics include:

- RHEL 10;
- declared storage/partitioning layout appropriate for IdM;
- network bootstrap capability;
- SSH/remote-management readiness;
- Python and automation prerequisites;
- time synchronization client capability;
- required operating-system packages or prerequisites for the later IdM installation workflow;
- Hyper-V guest compatibility where applicable;
- bootstrap administrative access;
- no preconfigured IdM realm/domain;
- no AD dependency;
- no Satellite dependency;
- no embedded environment secrets.

### Satellite base image

Expected characteristics include:

- RHEL 10-compatible or otherwise explicitly supported RHEL base version for the selected Satellite release;
- declared storage/partitioning layout appropriate for Satellite;
- network bootstrap capability;
- SSH/remote-management readiness;
- Python and automation prerequisites;
- time synchronization client capability;
- required operating-system prerequisites for the later Satellite installation workflow;
- Hyper-V guest compatibility where applicable;
- bootstrap administrative access;
- no preconfigured Satellite application state;
- no dependency on an already-existing Satellite server;
- no embedded environment secrets.

The exact supported RHEL/Satellite version pairing must be validated as part of the implementation project rather than assumed.

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

## Initial engineering objective

The first implementation project for `infra-image-builder` should compare the current/RHIS-derived legacy image-bootstrap approach with a RHEL 10-native Image Builder implementation where appropriate.

The comparison should use equivalent IdM and Satellite base-image requirements and evaluate:

- reproducibility;
- RHEL 10 supportability;
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
4. Boot successfully as a fresh Hyper-V VM.
5. Reach the expected bootstrap network and remote-management state.
6. Pass an explicit OS/base-image validation checklist.
7. Successfully hand off to the corresponding platform automation.
8. Require no pre-existing Satellite or AAP service.
9. Contain no undocumented environment-specific state or secrets.

The IdM image must successfully hand off to standalone IdM automation.

The Satellite image must successfully hand off to Satellite installation/configuration automation and ultimately establish the steady-state RHEL provisioning control plane.
