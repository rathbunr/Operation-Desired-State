# Operation Desired State — Seed Integrity Requirements

## Purpose

The seed environment is the temporary trust anchor used to establish the first automation, network, virtualization, identity, and management capabilities during a greenfield rebuild.

Because seed artifacts execute before the normal lab trust fabric exists, their integrity must be independently verifiable before use.

## Requirement

All seed code and other executable/bootstrap artifacts must be cryptographically hashed as part of the seed-build process.

This includes, at minimum:

- PowerShell scripts;
- shell scripts;
- Ansible playbooks, roles, inventories, and bootstrap configuration;
- standalone build/task-sequence support files;
- pfSense bootstrap artifacts;
- Cisco bootstrap automation;
- Hyper-V seed automation;
- IdM/Satellite/AAP bootstrap automation;
- locally maintained installation or configuration payloads whose integrity materially affects the bootstrap chain.

## Integrity model

The preferred baseline is SHA-256 or stronger using supported platform tooling.

A seed release should contain a deterministic manifest that records, at minimum:

- relative artifact path;
- cryptographic hash;
- hash algorithm;
- seed/release identifier;
- source Git commit, tag, or immutable revision where applicable;
- manifest generation date/time.

The manifest should be generated automatically from the authoritative seed source and must not be manually maintained file-by-file.

## Separation of payload and trust evidence

A hash stored beside a payload can detect accidental corruption but does not, by itself, prove that both the payload and hash were not modified together.

For stronger integrity assurance, the authoritative manifest should therefore be anchored independently of the removable seed payload. Acceptable mechanisms may include:

- the authoritative Git repository and immutable commit/tag;
- a separately retained manifest copy;
- a digitally signed manifest or release artifact;
- another independently controlled integrity source established by the seed project.

The exact signing/trust mechanism will be selected during the seed-node implementation project. Hashing is mandatory; signing is a strong preferred enhancement where practical.

## Bootstrap validation gate

Before seed code is executed during a reconstruction exercise:

1. identify the intended seed release/revision;
2. obtain the authoritative integrity manifest;
3. calculate hashes for the local seed payload;
4. compare every required artifact to the manifest;
5. fail closed on missing, unexpected, or mismatched required artifacts;
6. record the validation result as recovery evidence;
7. only then begin execution of the seed workflow.

No integrity mismatch should be bypassed merely to continue the build. The artifact should be reacquired or regenerated from the authoritative source and verified again.

## Seed release lifecycle

The seed project should eventually produce a repeatable release process:

`authoritative Git revision -> build/stage seed payload -> generate integrity manifest -> optionally sign manifest/release -> copy to seed media -> verify copied media -> execute only after validation`

This makes the seed environment a small, explicit bootstrap supply chain rather than an unmanaged collection of recovery scripts.

## Relationship to desired state

Integrity validation does not make the seed artifacts authoritative by itself. Git remains the authoritative source for maintained seed code and documentation.

The integrity controls prove that the artifacts actually being executed during bootstrap correspond to an approved, known source state.
