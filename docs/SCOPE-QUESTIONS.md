# Operation Desired State — Scope Interview

These questions are intended to define the program boundary before assigning completion percentages or building cross-repository orchestration.

## 1. Starting condition

What is the agreed starting point for a full reconstruction?

Examples:

- bare physical hardware with firmware configured
- installed hypervisors with no VMs
- existing network appliances reset to factory state
- minimal management workstation/builder available
- GitHub and external secret stores available
- Internet access available

The answer determines what Operation Desired State must itself bootstrap versus what it may treat as a prerequisite.

## 2. Physical infrastructure boundary

Should the program own desired state for physical infrastructure such as:

- Hyper-V host BIOS/firmware settings
- Dell server/client firmware
- Cisco switching
- NAS configuration
- UPS/power infrastructure
- pfSense installation/bootstrap

Or should some of those remain documented prerequisites?

## 3. Definition of reconstructable

Does "reconstructable" mean:

- produce functionally equivalent infrastructure
- reproduce exact hostnames, addresses, identities and policy
- restore application data as well as infrastructure configuration
- rebuild without restoring full-system backups

Which of these is required?

## 4. Data versus infrastructure

Where should the program boundary sit between infrastructure state and persistent data?

Examples:

- AD database
- IdM directory/KRA/CA data
- Satellite content and database
- AAP controller database
- Zabbix history
- MECM database/content library
- user/home data on Synology
- certificates/private keys

Should recovery of those data sets be part of Operation Desired State, or should the program only rebuild the platform that receives restored data?

## 5. Secrets and trust anchors

What external secret/trust systems may be assumed to survive a total rebuild?

Potential examples:

- Ansible Vault files
- YubiKeys
- offline root CA
- GitHub credentials
- recovery keys
- external password manager
- vendor subscription credentials

This is critical because some trust roots cannot be recreated deterministically from ordinary configuration code.

## 6. Network bootstrap

If pfSense and switching were both wiped, which device is expected to come first and how is initial management connectivity established?

This defines the earliest bootstrap dependency.

## 7. Identity bootstrap

For a greenfield reconstruction, which identity system is expected first?

- Active Directory
- Red Hat IdM
- independent local/bootstrap credentials

How much of later automation is allowed to depend on AD/IdM credentials or Kerberos?

## 8. PKI scope

Should Operation Desired State include deterministic deployment/recovery of:

- Microsoft standalone root CA
- issuing CAs, if any
- IdM CA/KRA
- certificate templates/policies
- enrollment configuration
- service certificates

Which private keys must be restored rather than regenerated?

## 9. Platform scope

Which major platforms are mandatory for the first formal program scope?

Candidate set:

- pfSense
- Cisco switching
- Hyper-V
- Active Directory/DNS
- Windows Time
- Microsoft PKI
- Red Hat IdM
- Satellite
- AAP
- Zabbix
- Synology
- MECM
- Heimdall/compliance pipeline
- Nessus
- endpoint/security tooling

## 10. Workload boundary

Should application workloads be considered in scope only when they provide infrastructure/management capabilities, or should every lab workload eventually be reconstructable through this program?

## 11. Endpoint boundary

Are user endpoints/workstations part of Operation Desired State, or should the program stop at server and infrastructure platforms?

Potential endpoint-related work includes:

- GPO desired state
- Windows hydration
- MECM
- workstation configuration
- security agent deployment

## 12. Availability expectation during convergence

Should routine desired-state enforcement be designed for production-like availability with rolling/one-at-a-time changes, while greenfield rebuild workflows may be more aggressive?

If so, these should be separate operating modes.

## 13. Completion standard

For an individual capability, which of these are mandatory before it can be called complete?

- desired state documented
- configuration version controlled
- secrets externalized
- dry run available
- deployment automated
- runtime validation automated
- functional validation automated
- idempotency verified
- rollback/recovery documented
- greenfield reconstruction tested
- disaster recovery tested
- ongoing drift detection implemented

## 14. Manual steps

Is the goal literally zero manual steps, or is the target zero **undocumented critical** manual steps with a small, explicit bootstrap procedure allowed?

## 15. Orchestration target

Is the long-term goal a top-level workflow capable of coordinating the entire reconstruction sequence, or is a documented dependency graph plus independently executable repositories sufficient?

## 16. Test environment

What can be safely destroyed to prove reconstruction?

Examples:

- individual VMs
- disposable replicas of services
- second Hyper-V host
- isolated VLAN/lab segment
- entire lab during a planned exercise

This will determine how aggressively greenfield claims can be validated.

## 17. Scope horizon

Should the first release of Operation Desired State aim for:

- core infrastructure only
- core infrastructure plus management platforms
- the entire currently operating lab

A phased scope can still retain the broader long-term vision.

## 18. Definition of done

Complete this sentence:

> I will consider Operation Desired State successful when I can __________.

This answer should become the anchor for the final charter and acceptance criteria.
