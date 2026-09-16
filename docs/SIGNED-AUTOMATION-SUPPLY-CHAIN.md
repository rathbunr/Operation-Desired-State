# Signed Automation Supply Chain

## Purpose

Operation Desired State treats automation artifacts as part of a trusted software supply chain.

The objective is not merely to detect accidental modification. It is to establish verifiable integrity, provenance, and approval evidence for the automation used to bootstrap and reconstruct the environment.

This requirement is especially relevant if the architecture is later adapted to controlled, disconnected, isolated, or otherwise sensitive environments where an assessor must be able to verify what automation crossed a boundary and whether it remained unchanged before execution.

This document does not claim that digital signing alone establishes compliance. It defines a technical control that can provide evidence supporting integrity, provenance, change control, and trusted-software expectations.

## Scope

Signed automation should apply to authoritative execution units rather than only individual YAML files.

The protected project boundary may include:

- Ansible playbooks;
- roles;
- inventories and inventory plugins where appropriate;
- templates;
- scripts invoked by playbooks;
- requirements files;
- configuration files;
- policy files;
- other executable or security-relevant project content.

A signed playbook that consumes an unsigned or modified role is not sufficient. The signature boundary should protect the complete automation unit required for execution.

## Trust model

The intended chain is:

```text
Authoritative Git repository
        |
        v
Reviewed / approved project revision
        |
        v
Cryptographically signed automation project
        |
        v
Trusted public verification key
        |
        v
Seed-node verification
        |
        v
Bootstrap execution
        |
        v
AAP project synchronization / verification
        |
        v
Steady-state automation execution
```

## Seed-stage requirement

Seed automation must fail closed when integrity or signature validation fails.

Before executing authoritative seed automation:

1. identify the intended release, tag, or approved Git revision;
2. obtain the corresponding signature/manifest from the authoritative source;
3. validate the signature using a separately trusted public key;
4. validate protected project content against the signed manifest;
5. reject execution if verification fails;
6. record verification evidence in the reconstruction log.

Simple file hashes remain useful for integrity checks, but cryptographic signatures provide stronger provenance because they bind the approved content to a signing identity.

## Steady-state requirement

Once Ansible Automation Platform is available, project content-signature verification should be enforced where supported for authoritative infrastructure automation.

The steady-state goal is that unsigned or incorrectly signed authoritative automation cannot silently enter the execution path.

Exceptions must be explicit and documented rather than treated as the default.

## Key-management principles

- Signing private keys must not be embedded in repositories, seed media, execution environments, or AAP projects.
- Seed systems and AAP require only the trusted public verification material.
- The private signing key should be isolated from normal execution systems.
- Hardware-backed signing should be evaluated where practical.
- Key rotation and revocation procedures must be documented.
- Trust anchors must be recoverable independently of the infrastructure they are used to validate.
- Compromise of a signing key must have a defined response process.

## Approval model

A signature proves possession of a signing key; it does not by itself prove that a change was technically correct.

The desired control model therefore separates:

1. source-control review and approval;
2. release/revision selection;
3. cryptographic signing;
4. signature verification;
5. execution authorization;
6. post-execution validation.

This distinction should be preserved if the architecture is later mapped to formal security controls or assessment evidence.

## Validation evidence

Useful evidence may include:

- Git commit SHA or release/tag identifier;
- signer identity / signing-key fingerprint;
- signature verification result;
- signed manifest or protected-content inventory;
- execution timestamp;
- target environment;
- automation run identifier;
- post-execution validation result;
- idempotency/convergence evidence where applicable.

## Architectural principle

Operation Desired State should eventually be able to demonstrate:

> The infrastructure was reconstructed using automation whose source revision, integrity, provenance, approval, execution, and resulting state can be independently verified.

This is a reference-architecture objective and should be implemented incrementally. It should not unnecessarily block early proof-of-concept work before the signing workflow itself has been established and tested.
