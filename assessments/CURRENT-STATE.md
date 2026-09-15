# Current-State Assessment — Initial Baseline

## Status

This is an evidence-based starting point, not a final maturity rating.

The repository estate demonstrates substantial automation already exists across core infrastructure, platform services, endpoint management, compliance, security tooling, and operations. The primary program-level gap is not lack of automation; it is the absence of one authoritative model describing scope, ownership, dependency order, reconstruction sequence, validation standards, and completion criteria.

## Initial observations

### 1. Network edge / pfSense

`infra-pfsense-desired-state` is currently one of the strongest examples of explicit desired-state work.

Observed characteristics include:

- Ansible-based declarative configuration
- pinned `pfsensible.core` dependency
- staged playbook structure
- check/diff workflow
- runtime validation
- explicit documentation of unsupported module gaps
- backup/rollback awareness
- convergence/idempotency checks for completed changes
- documentation of DNS/time remediation and Operation Desired State concepts

Known gaps remain around complete Unbound ownership, broader object coverage, VPN ownership, and disaster-recovery reconstruction testing.

### 2. Identity

The estate contains both IdM deployment and IdM configuration repositories:

- `infra-redhat-rhel-idm`
- `infra-idm-config`

The authoritative boundary between deployment/bootstrap and steady-state configuration must be defined.

Active Directory has a dedicated deployment repository:

- `infra-dc-deployment`

A dedicated GPO desired-state repository is not yet present and is expected to become a significant Windows configuration/policy workstream.

### 3. Satellite

Satellite appears intentionally decomposed across multiple repositories:

- installer/deployment
- configuration
- maintenance
- kickstart/provisioning content
- registration operations
- utility and temporary work

This decomposition may be appropriate, but canonical ownership and repository relationships need to be documented before reconstruction sequencing can be trusted.

### 4. Ansible Automation Platform

AAP has separate repositories for:

- installation
- configuration template/state
- exported configuration
- execution environment utilities
- LDAP inventory
- EDA integrations

This suggests a reasonably mature layered design, but a fresh-install-to-operational reconstruction path has not yet been assessed at the program level.

### 5. RHEL lifecycle

The repository estate contains multiple layers relevant to RHEL lifecycle:

- base OS configuration
- image build tooling
- Satellite kickstart/provisioning
- registration
- patching
- Insights
- CIS hardening
- GRUB hardening
- compliance scanning

The relationship between image-build repositories (`infra-packer-rhel`, `infra-image-builder`, `infra-image-mode`, `rhis-builder-hyperv-lz`) requires classification to determine current, legacy, experimental, and authoritative paths.

### 6. Virtualization

`infra-hyperv-lifecycle` exists for Hyper-V lifecycle work.

The program must distinguish:

- physical host bootstrap
- Hyper-V role and host configuration
- virtual switching/network integration
- VM creation
- VM guest provisioning
- integration-service policy
- recovery/rebuild of hosts and guests

### 7. Monitoring and management

Dedicated deployment/operations repositories exist for:

- Zabbix
- MECM
- Nessus
- CrowdStrike
- OpenSCAP/SCC/SCAP
- Heimdall

The program should determine which of these are required core services versus optional workloads when defining reconstruction tiers.

### 8. Continuous monitoring / event-driven automation

The estate contains substantial EDA/compliance integration work involving OpenSCAP, ServiceNow, Jira, Heimdall, and related automation.

This appears to be a higher-level capability built on top of the infrastructure foundation and should likely be modeled as a dependent program tier rather than a bootstrap prerequisite.

## Provisional conclusion

Operation Desired State is not beginning from zero. The environment already contains a meaningful implementation base.

The immediate need is to convert that implementation estate into an explicit systems model:

1. classify repositories
2. identify authoritative ownership
3. define scope
4. map dependencies
5. define reconstruction tiers
6. assess maturity by capability
7. identify manual dependencies and gaps
8. define acceptance criteria
9. test reconstruction in controlled stages

No percentage-complete estimate should be assigned until scope and maturity definitions are agreed.
