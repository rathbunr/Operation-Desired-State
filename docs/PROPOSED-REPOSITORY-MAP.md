# Operation Desired State — Proposed Repository Map

This document defines the proposed repository boundaries needed to support the full ODS effort. It is an architecture map, not a mandate to create every repository immediately.

Principle: **Operation Desired State orchestrates repositories; it does not replace them.** Each implementation repository should own one clear technical domain and remain independently testable.

## Program / control plane

### `Operation-Desired-State`
Owns:
- architecture
- dependency model
- bootstrap sequence
- policy
- repository authority map
- project decomposition
- recovery/reconstruction runbooks
- acceptance criteria
- maturity/evidence model

Does not own product implementation code.

## Bootstrap and image production

### `infra-image-builder` — existing, authoritative candidate
Owns:
- RHEL 10 Image Builder host setup
- IdM bootstrap image
- Satellite bootstrap image
- CIS L1/L2 and DISA STIG image profiles
- storage/layout policy implementation
- image validation
- artifact integrity/provenance
- Image Mode evaluation

### `infra-bootstrap-ansible` — proposed
Owns:
- minimal early Ansible execution environment
- bootstrap inventory
- bootstrap collections/dependencies
- handoff mechanics into AAP
- no long-term steady-state orchestration

### `infra-seed-node` — proposed
Owns:
- disposable seed laptop rebuild instructions
- seed virtualization/tooling setup
- seed artifact verification
- bootstrap prerequisites
- recovery entry point

## Network control plane

### `infra-pfsense-desired-state` — existing
Owns pfSense desired state, including networking, DNS Resolver, DHCP, firewall, VPN, routing, PKI integration, and validation.

### `infra-cisco-desired-state` — proposed
Owns:
- Catalyst 1300 bootstrap-safe state
- VLANs/access/trunks/LAGs
- management plane
- SNMP/NTP/DNS/syslog
- firmware procedure
- staged convergence and validation

## Virtualization / compute

### `infra-hyperv-lifecycle` — existing, authoritative candidate
Owns:
- Hyper-V host desired state
- vSwitch/VLAN configuration
- host storage/networking
- migration settings
- validation

### `infra-hyperv-seed` — proposed only if separation is justified
Would own deterministic HV-01 bootstrap installation/media preparation. Prefer keeping this inside `infra-hyperv-lifecycle` unless the bootstrap implementation becomes materially distinct.

## Identity / PKI

### `infra-redhat-rhel-idm` / `infra-idm-config` — existing overlap to reconcile
One repository should become authoritative for:
- standalone IdM deployment
- DNS/Kerberos/directory baseline
- IdM policy
- service identities
- host enrollment
- trust preparation
- validation

Do not retain overlapping authoritative IdM repositories indefinitely.

### `infra-ad-desired-state` — proposed unless `infra-dc-deployment` evolves into it
Owns:
- AD forest/domain creation
- DC provisioning
- AD-integrated DNS
- time hierarchy
- baseline GPO/domain configuration
- replication validation
- domain health evidence

### `infra-identity-integration` — proposed only if cross-platform integration becomes complex enough
Would own:
- AD/IdM DNS delegation/forwarding
- cross-realm/domain trust
- Kerberos integration
- identity handoff tests

Prefer keeping integration logic with the authoritative identity repos unless a separate orchestration boundary proves useful.

### `infra-pki-desired-state` — proposed
Owns:
- Microsoft standalone root CA / subordinate CA architecture where applicable
- certificate templates/policies
- trust distribution
- lifecycle/renewal automation
- hardware-backed identity integration where appropriate

## Red Hat platform management

### `infra-satellite-installer` — existing
Owns Satellite application installation/bootstrap.

### `infra-satellite-config` / `infra.satellite_configuration` — existing overlap to reconcile
One authority should own:
- organizations/locations
- products/repos
- sync plans
- content views
- lifecycle environments
- activation keys
- host groups
- provisioning templates/parameters
- Satellite policy/configuration

### `infra-satellite-ks` — existing
Owns Satellite Kickstart/provisioning template content if this remains sufficiently independent to justify a dedicated repository.

### `infra-satellite-maintenance` — existing
Owns operational maintenance workflows that do not belong in desired-state configuration.

## Ansible Automation Platform

### `aap-installer` — existing
Owns AAP installation/bootstrap.

### `aap-config-template` / `aap-config-export` — existing, authority to clarify
One or more repositories should explicitly own:
- controller organizations/teams
- credentials metadata
- projects
- inventories
- execution environments
- job templates/workflows
- schedules
- EDA integration

### `aap-ee-utilities` — existing
Owns reusable execution-environment build assets/utilities.

## Windows management

### `infra-mecm-deployment` — existing
Owns MECM infrastructure deployment/reconstruction.

### `ops-mecm-operations` — existing
Owns MECM operational workflows after deployment.

### `win-hydration-kit` — existing
Candidate for Windows bootstrap/hydration assets where that boundary remains useful.

### `win-winrm-config` — existing
Owns WinRM desired state where not absorbed into a broader Windows baseline repository.

### `infra-windows-baseline` — proposed
Owns common Windows Server / Windows client baseline configuration, security policy, core management prerequisites, and reusable host configuration not specific to MECM or AD.

## RHEL operating-system baseline

### `infra-rhel-base-os` — existing
Owns common RHEL desired-state baseline after provisioning.

### `sec-rhel-cis-hardening` — existing
Owns CIS-specific RHEL hardening implementation and validation where not better implemented directly in the image/provisioning profile.

### `sec-rhel-grub` — existing
Reassess whether this should remain separate or become part of the authoritative RHEL baseline/security implementation.

### `infra-packer-rhel` — existing
Reassess against `infra-image-builder`; retain only if Packer has a distinct supported use case.

### `infra-image-mode` — existing
Natural candidate for RHEL Image Mode / bootc experimentation and later managed-fleet implementation. It should not duplicate the bootstrap responsibilities of `infra-image-builder`.

## Monitoring / security / compliance

### `infra-zabbix-deployment` — existing
Owns Zabbix deployment and desired state.

### `sec-nessus-deploy` — existing
Owns Nessus deployment.

### `ops-nessus-scan` — existing
Owns Nessus operational scan workflows.

### `ops-oscap-scan` / `ops-scap-compliance` / `sec-scc-scanner` — existing
Reconcile boundaries among compliance scanners, scan execution, evidence collection, and remediation orchestration.

### Heimdall / EDA repositories — existing family
Retain separate repositories where they represent distinct deploy/runtime/event-processing responsibilities, but document the authoritative ownership of each workflow.

## Applications / SSO

### `infra-keycloak` — proposed unless an existing repo already owns it
Owns Keycloak deployment/configuration if Keycloak becomes the selected SSO platform.

### `infra-sso` — proposed only if Red Hat SSO remains distinct from Keycloak in the architecture
Avoid parallel repos if one platform supersedes the other.

## Storage / NAS

### `infra-synology-desired-state` — proposed
Owns reproducible Synology configuration where APIs/automation allow:
- network settings
- shares
- permissions integration
- services
- monitoring
- configuration evidence

Actual protected data remains outside infrastructure-as-code repositories.

## Firmware / physical platform

### `infra-firmware-baseline` — proposed only if cross-platform firmware lifecycle becomes substantial
Could own inventory/evidence and supported-update procedures for:
- Hyper-V hosts
- Cisco
- pfSense appliance hardware
- Synology
- managed endpoints

Prefer platform-specific ownership first; create this only if central orchestration adds real value.

## Repository governance / supply chain

### `ops-repo-governance` — proposed only if needed
Could own automated checks for:
- repository naming
- required README/policy files
- branch/ruleset expectations
- secret scanning
- Ansible linting
- signed-project manifests
- release/integrity evidence

This may instead remain centralized in ODS plus reusable GitHub Actions templates.

## Immediate authoritative candidates

Repositories that already have a clear place in the ODS architecture and should be evaluated before creating replacements:

- `Operation-Desired-State`
- `infra-image-builder`
- `infra-pfsense-desired-state`
- `infra-hyperv-lifecycle`
- `infra-rhel-base-os`
- `infra-redhat-rhel-idm`
- `infra-idm-config`
- `infra-satellite-installer`
- `infra-satellite-config`
- `infra.satellite_configuration`
- `infra-satellite-ks`
- `infra-satellite-maintenance`
- `aap-installer`
- `aap-config-template`
- `aap-config-export`
- `aap-ee-utilities`
- `infra-dc-deployment`
- `infra-mecm-deployment`
- `ops-mecm-operations`
- `infra-image-mode`
- `infra-zabbix-deployment`

## Next governance action

Do not create every proposed repository now.

For each architecture domain:

1. inspect existing repositories;
2. identify authoritative ownership;
3. collapse or retire unnecessary overlaps;
4. create a new repository only where no existing repository cleanly owns the requirement;
5. record the decision in ODS.
