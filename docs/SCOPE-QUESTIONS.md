# Operation Desired State — Scope Interview

These questions define the program boundary before assigning completion percentages or building cross-repository orchestration.

## Answered baseline

### 1. Starting condition — ANSWERED

For a catastrophic lab loss, the practical starting point is a newly provisioned laptop or management workstation with Internet access, Git access, and access to required external secrets/trust material.

A documented **seed node** build procedure is critical. The seed node is expected to bootstrap the first Hyper-V system before the normal automation stack exists.

Replacement hardware may differ materially from current hardware, so the program should be hardware-agnostic where practical.

### 2. Physical infrastructure boundary — ANSWERED

All physical infrastructure is in scope.

This includes physical hosts, pfSense, Cisco networking, NAS/storage infrastructure, and relevant BIOS/firmware state. Automation is preferred where practical, but human-readable documentation describing the current state is also a required recovery artifact.

### 3. Definition of reconstructable — ANSWERED

The preferred outcome is restoration of the lab to the current intended state as closely as practical.

Exact hardware duplication is not required. Logical/service identity, configuration, architecture, policy, and functionality should be restored where appropriate.

### 4. Data versus infrastructure — PARTIALLY ANSWERED

Platform database-level backups are not considered important simply for reproducing infrastructure configuration. The preferred pattern is to rebuild platforms from desired-state definitions.

Personal/irreplaceable data is explicitly important and requires an N-tier backup strategy.

Still to define:

- treatment of PKI private keys and trust anchors
- whether any directory/stateful service data must be restored rather than regenerated
- what personal-data backup tiers and media/location diversity are required

### 18. Definition of done — ANSWERED

> I will consider Operation Desired State successful when I can restore each individual host and configuration as code and restore the node or device to full functionality.

Working program interpretation:

> Each in-scope individual host, node, or device and its intended configuration can be reconstructed from code and documented dependencies to full functionality, with configuration convergence validated and no undocumented critical recovery dependency.

## Provisional reconstruction sequence

Current working sequence from catastrophic loss:

1. Provision management laptop/workstation and regain Git/secrets access.
2. Build the documented seed node.
3. Use the seed node to image/provision the first Hyper-V node.
4. Restore foundational network services, including pfSense and switching, as required for stable infrastructure connectivity.
5. Provision Red Hat IdM.
6. Provision Red Hat Satellite.
7. Provision Ansible Automation Platform.
8. Reprovision Active Directory domain controllers and MECM.
9. Reconstruct remaining infrastructure, security, management, and workload systems according to dependency order.
10. Validate full functionality and convergence for each restored node/device.

This order remains provisional until the dependency graph is built. In particular, pfSense, DNS, PKI, AD, and switching may need to occur earlier than the current conceptual sequence.

---

## Remaining interview questions

### 5. Secrets and trust anchors

What external secret/trust systems may be assumed to survive a total rebuild?

Potential examples:

- Ansible Vault files
- YubiKeys
- offline root CA
- GitHub credentials
- recovery keys
- external password manager
- vendor subscription credentials

Which of these are expected to exist outside the lab, and which must Operation Desired State explicitly back up or reconstruct?

### 6. Network bootstrap

If pfSense and switching were both wiped or replaced, which device is expected to come first and how is initial management connectivity established?

Is there a minimal temporary flat network/bootstrap configuration you would accept before the final VLAN/routing desired state is applied?

### 7. Identity bootstrap

Your current conceptual sequence places Red Hat IdM before AD during a catastrophic rebuild.

Is that intentional as the primary infrastructure identity bootstrap, or would local/bootstrap credentials be used until both IdM and AD are available?

How much of the seed-node and first-Hyper-V automation should be independent of either directory?

### 8. PKI scope

Should Operation Desired State include deterministic deployment/recovery of:

- Microsoft standalone root CA
- issuing CAs, if any
- IdM CA/KRA
- certificate templates/policies
- enrollment configuration
- service certificates

Which private keys must survive and be restored rather than regenerated?

### 9. Platform scope

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

### 10. Workload boundary

Should every lab workload eventually be reconstructable, or should Operation Desired State stop at infrastructure/management platforms and treat ordinary workloads as separate projects?

### 11. Endpoint boundary

Are user endpoints/workstations part of Operation Desired State?

Potential endpoint-related work includes:

- GPO desired state
- Windows hydration
- MECM
- workstation configuration
- security-agent deployment

### 12. Availability expectation during convergence

Should routine desired-state enforcement use production-like one-at-a-time/low-risk changes while greenfield reconstruction may use more aggressive sequencing?

### 13. Completion standard

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

### 14. Manual steps

Is the target literally zero manual steps, or zero **undocumented critical** manual steps with a small explicit bootstrap procedure allowed?

### 15. Orchestration target

Should the eventual program have one top-level workflow capable of coordinating the reconstruction sequence across repositories, or is a documented dependency graph plus independently executable repos sufficient?

### 16. Test environment

What can be safely destroyed to prove reconstruction?

Examples:

- individual VMs
- disposable replicas of services
- second Hyper-V host
- isolated VLAN/lab segment
- entire lab during a planned exercise

### 17. Scope horizon

Should the first release target:

- core infrastructure only
- core infrastructure plus management platforms
- the entire currently operating lab

A phased scope can retain the broader long-term vision.
