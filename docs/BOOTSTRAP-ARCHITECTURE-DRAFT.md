# Operation Desired State — Bootstrap Architecture Draft

This document captures the current intended greenfield bootstrap sequence for reconstructing the lab from a minimal external control plane.

The architecture is intentionally seed-driven: a small number of independently bootstrappable systems establish the platform services required to build everything else declaratively.

## Bootstrap objective

The lab should be reconstructable beginning with:

- a spare Windows workstation/laptop that can be wiped and rebuilt from documented manual instructions;
- Internet access;
- access to authoritative Git repositories and required cloud-hosted information;
- suitable physical replacement hardware;
- recovery/install media as needed.

The first major milestone is not to make the seed node feature-rich. It is to establish enough temporary compute and network capability to reach **Ansible-driven automation as quickly as practical**, then transition the seed node out of the critical path.

## Seed-node philosophy

The seed node is a **disposable temporary control plane**, not a permanent management platform.

Its own reconstruction should be intentionally simple and provable:

1. wipe the spare laptop;
2. rebuild it from explicit written instructions;
3. use scripts wherever they reduce manual variance without creating hidden dependencies;
4. enable local virtualization capability as required;
5. host temporary seed VMs or tooling where useful;
6. use that temporary environment to establish the network, first infrastructure services, and an Ansible-capable execution point;
7. hand control to the lab's managed automation stack;
8. remove the seed laptop from the normal critical dependency chain.

The seed node should therefore optimize for **independence, clarity, and minimum bootstrap time**, not long-term elegance.

Manual steps are acceptable at this layer when they are fully documented, deterministic, testable, and do not become permanent operational dependencies.

## Foundational control planes

Operation Desired State treats the following as foundational control planes rather than ordinary application services:

1. **Network** — connectivity, routing, segmentation, DNS path, DHCP, management reachability.
2. **Identity** — authentication, authorization, service identities, Kerberos, DNS-integrated identity dependencies, trust boundaries, and later cross-domain integration.
3. **Automation/management** — Ansible capability first, followed by Satellite, Ansible Automation Platform, MECM, and related orchestration systems that convert declared intent into managed infrastructure.

Identity is intentionally elevated because nearly every later management function eventually depends on a trustworthy answer to **who or what is allowed to act**.

Automation is intentionally elevated because the seed environment should transfer control to declarative automation as early as possible.

The architecture must therefore distinguish two identity states:

### Bootstrap identity

Bootstrap identity exists before enterprise/lab identity services are available.

It should be:

- minimal;
- local to the seed/bootstrap systems;
- independent of AD and IdM;
- sufficient only to establish the first trusted services;
- temporary or tightly bounded in purpose;
- documented so it does not become an undocumented standing administrative path.

Examples include local administrative credentials on the seed workstation, pfSense, Cisco switch, HV-01, and the first IdM/Satellite/AAP builds.

### Steady-state identity

Once IdM and AD are available, management should transition from bootstrap credentials toward managed identities and explicit trust relationships.

The desired steady state should define:

- Red Hat IdM as the initial Linux identity/Kerberos/DNS-integrated identity control plane;
- Active Directory as the Windows identity/DNS/Kerberos control plane;
- service accounts and machine identities used by automation;
- cross-realm or cross-domain trust only after both identity systems are independently healthy;
- certificate-based or hardware-backed authentication where appropriate;
- removal, disabling, or deliberate retention of bootstrap credentials after handoff;
- least-privilege and just-in-time access patterns where practical;
- explicit ownership of identity policy as code.

The long-term reference architecture should be able to show not only how systems are provisioned, but how administrative authority itself is bootstrapped, transferred to managed identity, validated, and recovered.

## Program boundary

Operation Desired State is currently a **proof of concept and reference-architecture implementation** for the RITCSUSA lab.

The architecture may eventually provide useful patterns for highly controlled, disconnected, or otherwise closed infrastructure environments, but enterprise productization, organizational adoption, commercial positioning, and any associated business pitch are outside the current implementation scope.

The current objective is to prove the technical patterns first.

## Seeded bootstrap sequence

### Phase 0 — External control point

1. Wipe and provision the spare Windows workstation/laptop from the documented seed-node runbook.
2. Enable required local virtualization and administrative tooling using scripted steps wherever practical.
3. Recover GitHub and other cloud access.
4. Obtain required installation media, drivers, firmware utilities, and seed automation.
5. Establish the documented bootstrap credential set required to manage the seed workstation and initial infrastructure devices before AD/IdM exist.
6. Prepare the seed node to host temporary VMs or automation tooling required to reach Ansible capability.

This system is the only assumed external administrative control point.

### Phase 1 — Foundational network edge

7. Install or restore pfSense first where practical.
8. Restore required routing, VLAN, DNS-forwarding/resolver, DHCP, and management-network functionality.
9. Restore Cisco switching to the minimum bootstrap state needed for the required VLAN/trunk design.
10. Apply the declared Cisco desired state in a controlled dependency order once stable management connectivity exists.

Rationale: the first Linux infrastructure nodes may require reliable Internet access, routing, DNS, and package/update reachability before higher-level identity/content-management services exist.

The exact pfSense/Cisco ordering may vary depending on replacement hardware and temporary cabling, but stable bootstrap networking is a dependency gate before moving into managed infrastructure services.

The Cisco switch is a **first-class desired-state component**, not merely a documented appliance. Because switching configuration is order-sensitive and can remove the management path if applied incorrectly, Cisco automation must model sequencing and validation explicitly rather than treating the device as a flat configuration blob.

At minimum, the Cisco desired-state project must eventually capture:

- current physical port-to-device relationships;
- VLAN definitions;
- access-port membership;
- trunk/native VLAN configuration;
- allowed VLAN sets;
- LAG/LACP configuration where present;
- management addressing and management-plane access;
- spanning-tree intent where relevant;
- SNMP/monitoring configuration;
- NTP/DNS/syslog dependencies where configured;
- firmware current state and supported upgrade procedure;
- bootstrap-safe ordering;
- rollback/recovery path for loss of management connectivity;
- post-change validation.

The desired implementation should distinguish **bootstrap-safe minimum configuration** from **full steady-state configuration** so that reconstruction cannot deadlock on network automation that assumes the final network already exists.

### Phase 2 — Early Ansible execution capability

11. Establish a minimal Ansible-capable Linux execution environment as early as practical, either as a temporary VM on the seed laptop or on the first available virtualization substrate.
12. Configure only what is necessary to execute reviewed bootstrap playbooks against reachable infrastructure.
13. Begin replacing manual seed steps with Ansible-managed changes as soon as targets and network reachability permit.

This is intentionally **not yet AAP**. It is a lightweight bootstrap execution capability whose purpose is to minimize the amount of infrastructure that must be built manually.

The target architectural handoff is therefore:

**manual/scripted seed → minimal Ansible capability → managed IdM/Satellite/AAP stack → full desired-state reconstruction**

### Phase 3 — Seed Hyper-V host

14. Build **HV-01** as the first seeded Hyper-V host.

The preferred seed mechanism may be a MECM-derived standalone task sequence/media or an equivalently deterministic offline build process capable of installing Windows Server and Hyper-V without requiring production MECM infrastructure to already exist.

The HV-01 seed process should establish only what is necessary to host the first infrastructure VMs. It must not depend on AD, IdM, Satellite, AAP, or another lab-hosted management service.

Where the early Ansible execution environment can safely configure HV-01 remotely, it should assume increasing responsibility immediately after basic OS/network/remote-management readiness is established.

HV-01 becomes the first permanent virtualization substrate for the bootstrap control plane.

### Phase 4 — Red Hat IdM seed

15. Provision an IdM VM on HV-01 from a dedicated RHEL seed image/profile designed specifically for IdM.
16. Apply the IdM installation automation/playbook using the earliest practical Ansible execution point.
17. Bring IdM online in a **standalone initial state without external AD-domain awareness or cross-realm trust**.
18. Validate IdM DNS, Kerberos, directory, API, time dependencies, and administrative access before using IdM identities anywhere else.

The IdM seed must not depend on Satellite for its initial OS installation.

The IdM platform should be capable of receiving normal operating-system updates through the bootstrap network before Satellite is available, if required.

This phase establishes the initial Linux identity/DNS/Kerberos control plane and creates the first managed identity authority inside the reconstructed lab.

### Phase 5 — Red Hat Satellite seed

19. Provision the Satellite VM on HV-01 from a dedicated RHEL seed image/profile architected for Satellite.
20. Apply Satellite installation automation.
21. Configure the minimum content/subscription state required to support managed RHEL provisioning.
22. Where appropriate, enroll Satellite into IdM and replace bootstrap-local administrative dependencies with managed identity.

The Satellite build must not require AAP to exist first.

Satellite then becomes the authoritative RHEL lifecycle/content platform for subsequent managed Linux builds.

### Phase 6 — Ansible Automation Platform

23. Use the now-available Red Hat management stack to provision/install Ansible Automation Platform.
24. Configure AAP sufficiently to execute the authoritative infrastructure automation repositories.
25. Integrate AAP with the intended managed identity sources and automation service accounts, then explicitly document which bootstrap credentials can be retired or reduced.
26. Migrate remaining automation responsibility from the temporary seed-node Ansible environment to AAP.

AAP is the transition point from **temporary bootstrap automation** to the **steady-state managed automation control plane**.

Once this handoff is validated, the seed laptop should no longer be required for normal reconstruction steps except as an emergency/bootstrap recovery asset.

### Phase 7 — Remaining critical infrastructure on HV-01

27. Use Satellite/AAP and existing repositories to provision remaining critical RHEL systems on HV-01.
28. Use Windows build automation, standalone MECM media where useful, and AAP/PowerShell automation to provision critical Windows infrastructure on HV-01.
29. Build the Active Directory domain controllers and associated Windows infrastructure according to the dependency model.
30. Validate AD as an independent Windows identity/DNS/Kerberos authority before adding trust or external integration.
31. Once AD is available and independently healthy, add domain-aware integration to IdM only where required (for example DNS delegation/forwarding, trust, Kerberos integration, or other planned interoperability).

The initial IdM bootstrap intentionally precedes this integration so that neither identity platform creates a circular dependency on the other.

### Phase 8 — MECM and second Hyper-V host

32. Build or reconstruct MECM after the required Windows identity/network prerequisites exist.
33. Integrate MECM with the intended AD identities, service accounts, PKI configuration where applicable, and administrative roles.
34. Use MECM UEFI/PXE boot to provision **HV-02**.
35. Apply Hyper-V desired-state configuration to HV-02.
36. Validate host networking, management access, firmware/BIOS requirements, identity integration, and virtualization behavior.

HV-02 is therefore not a bootstrap dependency. It is a managed system produced by the management plane established through HV-01.

### Phase 9 — General reconstruction

37. Provision remaining Windows and Linux infrastructure.
38. Restore security, monitoring, compliance, endpoint-management, and supporting services.
39. Rebuild managed laptops/workstations to desired state.
40. Restore NAS configuration and separately classified data where required.
41. Execute full runtime, functional, identity, dependency, and convergence validation.

## Dependency model

```mermaid
flowchart TD
    LAPTOP[Disposable seed laptop] --> BOOTID[Bootstrap identity]
    LAPTOP --> ANSBOOT[Minimal Ansible execution]
    LAPTOP --> PFSENSE[pfSense bootstrap]
    LAPTOP --> CISCOBOOT[Cisco bootstrap minimum]
    BOOTID --> PFSENSE
    BOOTID --> CISCOBOOT
    PFSENSE --> NET[Stable routed bootstrap network]
    CISCOBOOT --> NET
    NET --> CISCOFULL[Cisco full desired state]
    NET --> HV1[HV-01 seed build]
    ANSBOOT --> CISCOFULL
    ANSBOOT --> HV1
    BOOTID --> HV1
    HV1 --> IDM[Standalone IdM seed VM]
    ANSBOOT --> IDM
    IDM --> LINUXID[Managed Linux identity]
    IDM --> SAT[Satellite seed VM]
    ANSBOOT --> SAT
    SAT --> AAP[AAP]
    AAP --> HANDOFF[Automation handoff]
    ANSBOOT --> HANDOFF
    HANDOFF --> LINUX[Remaining RHEL infrastructure]
    HANDOFF --> WIN[Critical Windows infrastructure]
    WIN --> AD[Active Directory / DNS]
    AD --> WINID[Managed Windows identity]
    AD --> MECM[MECM]
    MECM --> HV2[HV-02 UEFI/PXE build]
    AD --> IDMINT[IdM/AD domain-aware integration]
    IDM --> IDMINT
    IDMINT --> TRUST[Cross-realm / integrated identity]
    HV2 --> REST[Remaining workloads / services]
    LINUX --> REST
    WIN --> REST
```

## Reuse-before-build principle

Operation Desired State should **prefer supported upstream or reference automation over creating new local implementations from scratch**.

Candidate reference sources include Red Hat Infrastructure Standard (RHIS) and other Red Hat-supported/reference repositories covering technologies such as:

- Red Hat IdM;
- RHEL base operating-system configuration;
- Red Hat Satellite;
- Ansible Automation Platform;
- Red Hat SSO / Keycloak;
- related Red Hat infrastructure technologies.

These repositories are not automatically authoritative for RITCSUSA. They should be treated as **reference implementations and accelerators** subject to review.

For each applicable platform, the preferred decision sequence is:

1. identify an applicable supported/reference implementation;
2. assess whether its assumptions match the RITCSUSA architecture;
3. reuse it directly where appropriate;
4. wrap or parameterize it where local policy differs;
5. fork only when sustained divergence is justified;
6. create new automation only when an existing implementation does not reasonably satisfy the requirement.

The goal is to avoid unnecessary reinvention while preserving explicit local ownership of desired state.

## Architecture observations and refinements

### 1. The seed node should be intentionally disposable

The seed laptop should be something that can be wiped repeatedly during testing without affecting the steady-state environment.

A successful seed-node design is proven when another rebuild from written instructions produces the same bootstrap capability without relying on undocumented local state.

### 2. Ansible capability should be established before AAP

Waiting for Satellite and AAP before using Ansible would leave too much early infrastructure in the manual bootstrap domain.

A lightweight Ansible control environment should therefore be created as early as practical. AAP later becomes the managed enterprise-style automation platform and absorbs those workflows.

### 3. pfSense should likely precede IdM

This reduces bootstrap coupling. IdM can then be installed and updated over a stable routed network without relying on later infrastructure services.

### 4. Cisco configuration requires staged convergence

The switch cannot safely be treated as an all-at-once replacement configuration during bootstrap. A minimal management/trunk/VLAN state must exist first, followed by controlled convergence toward the full desired state.

The Cisco implementation should use dry-run/diff capabilities where available and make changes in an order that preserves the active management path.

### 5. HV-01 must be independently seedable

HV-01 is a true bootstrap node. Its build path cannot depend on the production MECM server, AD, IdM, Satellite, or AAP.

### 6. Identity requires an explicit bootstrap-to-managed handoff

The architecture cannot assume enterprise identity already exists, but it also should not leave bootstrap-local credentials as permanent hidden dependencies.

Each major control-plane service should therefore document:

1. how it is first administered before IdM/AD exist;
2. when managed identity becomes available;
3. how service/admin identity is migrated or integrated;
4. which bootstrap credentials are disabled, rotated, vaulted, or deliberately retained;
5. how identity functionality is validated before downstream dependencies are allowed to consume it.

This identity handoff is part of the desired state, not an operational afterthought.

### 7. IdM should initially be standalone

The first IdM instance should establish its own domain/realm and required DNS/Kerberos services without depending on AD. Domain-awareness and trust should be a later integration phase after AD is rebuilt.

This avoids an identity bootstrap loop.

### 8. AD should also be independently healthy before trust

AD must prove its own DNS, Kerberos, replication, time, and administrative behavior before cross-realm or IdM-aware integration is introduced.

Trust is therefore an integration layer between two healthy identity systems, not a bootstrap requirement for either one.

### 9. Satellite must be seedable without itself

The Satellite host OS must come from a dedicated seed build path because Satellite cannot provide its own initial content/provisioning dependency.

### 10. AAP is the bootstrap-to-steady-state boundary

Before AAP exists, reconstruction uses a temporary Ansible execution environment and other seed techniques. After AAP exists, normal version-controlled desired-state automation should take over wherever practical.

### 11. HV-02 should be treated as evidence

Provisioning HV-02 through MECM UEFI/PXE after the management stack exists is a useful proof that the bootstrap environment has crossed into normal managed reconstruction.

### 12. Reference automation should be adapted, not blindly copied

Upstream/reference repositories are valuable because they encode product knowledge and established deployment patterns. They still require architecture review against the lab's network, identity, security, lifecycle, and bootstrap constraints before becoming part of the authoritative implementation.

## Validation gates

Each phase should have an explicit gate before the next begins.

Examples:

- **Seed-node gate:** spare laptop can be wiped and rebuilt from the runbook with no hidden dependency; virtualization and bootstrap tooling are functional.
- **Bootstrap identity gate:** required local/bootstrap administrative paths work and are documented; no later identity service is assumed.
- **Early Ansible gate:** minimal control environment can execute a harmless test play against at least one bootstrap target using documented credentials/connectivity.
- **Network bootstrap gate:** pfSense management reachable, Cisco management reachable, Internet/routing available, required bootstrap VLANs operational.
- **Cisco convergence gate:** intended VLAN/trunk/LAG state applied without loss of management; required endpoints reachable across expected paths.
- **HV-01 gate:** Hyper-V operational, intended vSwitch/VLAN topology available, remote management working.
- **IdM gate:** DNS/Kerberos/IPA API functional in standalone mode; intended managed Linux identities can authenticate and authorize as designed.
- **Satellite gate:** content/subscription lifecycle functional, capable of serving/building a managed RHEL host, and intended identity integration validated.
- **AAP gate:** inventories/credentials/projects/execution environment sufficiently functional to run infrastructure automation; automation identities work without relying on undocumented bootstrap credentials.
- **Automation handoff gate:** the workflows needed for subsequent reconstruction execute from AAP without relying on the seed laptop's local Ansible state.
- **AD gate:** DNS, Kerberos, replication, time hierarchy, administrative access, and intended Windows identities functional before trust.
- **Identity integration gate:** IdM and AD are independently healthy; DNS forwarding/delegation and trust are validated without creating circular dependencies.
- **MECM gate:** PXE/UEFI task sequence can successfully build HV-02 using intended Windows identity dependencies.
- **HV-02 gate:** host reaches declared desired state and passes functional validation.

## Project decomposition candidates

Once this bootstrap architecture is accepted, it naturally decomposes into separate projects/prompts:

1. Seed laptop manual rebuild runbook
2. Seed laptop local virtualization/tooling automation
3. Early bootstrap Ansible execution environment
4. pfSense bootstrap/reconstruction
5. Cisco desired-state bootstrap and staged convergence
6. HV-01 standalone seed build
7. IdM standalone seed build using reviewed upstream/reference automation where practical
8. Identity bootstrap-to-managed handoff model
9. Satellite standalone seed build using reviewed upstream/reference automation where practical
10. AAP bootstrap and desired-state configuration using reviewed upstream/reference automation where practical
11. Seed-Ansible-to-AAP automation handoff
12. AD/DC reconstruction
13. AD/IdM DNS, Kerberos, and trust integration
14. MECM reconstruction
15. HV-02 managed PXE/UEFI reconstruction
16. RHEL fleet reconstruction
17. Windows infrastructure/endpoints reconstruction
18. Red Hat SSO/Keycloak and related application identity services
19. Validation and whole-lab recovery runbook

These projects should remain independently testable while participating in the single Operation Desired State recovery flow.
