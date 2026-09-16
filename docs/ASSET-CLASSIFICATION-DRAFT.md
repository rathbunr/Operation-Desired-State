# Operation Desired State — Asset Classification Draft

This document classifies assets according to the recovery philosophy of Operation Desired State.

The classification is intentionally simple:

- **Must Back Up** — permanent loss is unacceptable or would materially prevent reconstruction.
- **Nice to Back Up** — useful for convenience, history, or rebuild speed, but not required for recovery success.
- **Rebuild / Do Not Back Up** — expected to be recreated from code, documentation, vendor media, or automation.
- **TBD** — requires explicit review before classification.

The location of an asset does **not** determine its classification. For example, data stored on the NAS may be Must Back Up, Nice to Back Up, or disposable depending on its value.

---

## 1. Must Back Up

### Authoritative desired-state source repositories

All repositories that are confirmed authoritative for an in-scope component are **Must Back Up**.

Reason: these repositories are part of the recovery control plane. Loss of both GitHub and all independent copies would destroy the intended reconstruction source.

Current confirmed/likely examples include:

| Asset | Classification | Notes |
|---|---|---|
| `Operation-Desired-State` | Must Back Up | Program charter, scope, dependency model, runbook, classifications, validation evidence |
| `infra-pfsense-desired-state` | Must Back Up | Likely authoritative pfSense desired-state source; current status already substantially validated |
| Any repository later confirmed authoritative | Must Back Up | Automatically inherits Must Back Up classification |

### Unique personal / irreplaceable data

Any personal data that cannot be regenerated, re-downloaded, or reasonably recreated is **Must Back Up**.

Examples may include:

- personal documents;
- photographs/video;
- unique records;
- original creative/work product not already protected elsewhere;
- unique recovery information;
- unique exported configuration/documentation that has not yet been converted to source-controlled desired state.

The exact NAS paths and data sets remain **TBD** until inventory/classification is performed.

### External recovery information not reproducible from the lab

Information required to regain access to cloud/vendor services is Must Back Up **if it is not already safely recoverable through the external provider/account**.

Examples may include recovery codes or unique account-recovery material. Credentials synchronized through Microsoft Edge are currently treated as part of the external bootstrap assumption rather than an in-lab backup target.

---

## 2. Nice to Back Up

These assets reduce rebuild effort or preserve useful history but must never be required for a successful reconstruction.

| Asset | Classification | Notes |
|---|---|---|
| Optional Windows seed/recovery images | Nice to Back Up | Can accelerate DC/MECM/WAC/workstation bootstrap, but desired-state automation remains authoritative |
| pfSense exported configuration snapshots | Nice to Back Up | Useful emergency accelerator once desired-state coverage is complete; not the target recovery mechanism |
| Cisco exported configuration snapshots | Nice to Back Up | Useful reference/accelerator if complete intent/config is documented elsewhere |
| NAS configuration export | Nice to Back Up / possibly Must | Current default is Nice if configuration is fully reproducible; elevate to Must until reproducibility is proven |
| BIOS/UEFI exported profiles where supported | Nice to Back Up | Useful accelerator; human-readable required settings remain authoritative |
| Selected troubleshooting logs / scan reports | Nice to Back Up | Retain only where they have learning/reference value |
| Cached installation media / ISOs | Nice to Back Up | Valuable when downloads are large or vendor availability is inconvenient; normally reproducible |
| Package/content caches | Nice to Back Up | Satellite or other caches can reduce recovery time but should not be required |
| Historical architecture snapshots | Nice to Back Up | Useful for learning and comparison if current source-controlled architecture exists |

---

## 3. Rebuild / Do Not Back Up

These assets are deliberately disposable.

### Infrastructure and runtime systems

| Asset | Classification | Recovery mechanism |
|---|---|---|
| Hyper-V host OS installations | Rebuild | Build/configuration code + documented BIOS/firmware intent |
| Hyper-V virtual machines | Rebuild | Provisioning + configuration automation |
| DC-01 / DC-02 runtime VMs | Rebuild | DC deployment automation and documented AD desired state |
| Red Hat IdM runtime VM | Rebuild | IdM deployment/config repositories |
| Satellite runtime VM/database | Rebuild | Installer/config/content automation |
| Ansible Automation Platform runtime/database | Rebuild | Installer + AAP configuration repositories |
| MECM runtime server/database | Rebuild | MECM deployment/configuration automation |
| Windows Admin Center runtime | Rebuild | Deployment/configuration automation |
| Zabbix runtime server/database/history | Rebuild | Deployment/configuration automation unless a future learning requirement elevates selected history |
| Heimdall runtime/database | Rebuild | Deployment automation |
| Nessus runtime state | Rebuild | Deployment/configuration automation |
| Linux infrastructure nodes | Rebuild | Image/build/base configuration automation |
| Windows infrastructure nodes | Rebuild | Hydration/deployment/GPO/configuration automation |
| User workstation/laptop OS | Rebuild | Workstation image/hydration desired-state workflow |
| PKI CA databases/history | Rebuild | PKI reconstruction; continuity not required |
| Issued certificates | Rebuild | Re-enroll/reissue after reconstructed PKI is available |
| Platform logs | Rebuild / discard | Retain only selected evidence explicitly classified Nice or Must |
| Temporary scan/output artifacts | Rebuild / discard | Regenerate through tooling |
| Downloadable vendor software | Rebuild / re-download | Preserve only where availability/licensing makes a local copy useful |

### Network devices

The devices themselves are not backup objects.

- pfSense: rebuild configuration from authoritative desired state.
- Cisco switch: rebuild from documented intent and future desired-state automation.
- Replacement hardware may be like-for-like or better-capability equipment.

Exported configurations may remain **Nice to Back Up** as accelerators until code coverage is proven complete.

---

## 4. Git repository classification rule

Repository backup classification follows **authority**, not simply existence.

### Must Back Up

- authoritative implementation repositories;
- this program/control-plane repository;
- unique documentation repositories that contain unreproducible recovery knowledge.

### Nice to Back Up

- useful historical repositories;
- superseded implementation repositories retained for reference;
- experimental repositories with learning value;
- utilities that can be recreated but would be inconvenient to lose.

### Do not intentionally protect

- disposable test repositories;
- temporary repositories;
- duplicates after canonical content has been verified elsewhere;
- abandoned experiments with no retained value.

No repository should be deleted merely because it falls into this final category until ownership/canonical status has been explicitly reviewed.

---

## 5. Provisional repository groups requiring authority review

The repository inventory reveals several areas where canonical ownership must be established before backup classification can be finalized:

### Red Hat IdM

- `infra-redhat-rhel-idm`
- `infra-idm-config`

Questions:
- Is one deployment and one configuration repo intentionally authoritative?
- Is either superseded?

### Red Hat Satellite

- `infra-satellite-installer`
- `infra-satellite-config`
- `infra-satellite-maintenance`
- `infra-satellite-ks`
- `infra.satellite_configuration`
- `ops-satellite-registration`
- `ops-sat-menu`
- `temp-sat-cac`

Questions:
- Which repos represent current desired state versus operational tooling, experiments, or superseded work?

### Ansible Automation Platform

- `aap-installer`
- `aap-config-template`
- `aap-config-export`
- `aap-ee-utilities`
- `aap-inventory-ldap`

Questions:
- Which are required to rebuild AAP itself?
- Is `aap-config-export` source of truth, evidence, or generated backup/export?

### Event-Driven Automation / compliance

Multiple production/development/integration repos exist. These require a distinction between current operational automation and retained experiments.

### RHEL build/image path

- `infra-rhel-base-os`
- `infra-packer-rhel`
- `infra-image-builder`
- `infra-image-mode`
- `rhis-builder-hyperv-lz`
- `rhis-one-click`

Questions:
- Which path is the current seed-to-RHEL provisioning architecture?
- Which repos are experimental or superseded?

### Windows workstation tooling

- `win-kitty-shell`
- `win-kittyshell`
- `win-hydration-kit`

Questions:
- Which KittyShell repo is canonical?
- Is workstation desired state primarily owned by hydration/MECM/GPO, with KittyShell treated as user tooling?

---

## 6. NAS data classification worksheet

NAS data should be classified by dataset/share/path, not by NAS volume.

| Dataset / Share | Owner / Purpose | Must Back Up | Nice to Back Up | Rebuild / Discard | Offsite Required | Notes |
|---|---|---:|---:|---:|---:|---|
| Personal documents | TBD | TBD | TBD | TBD | TBD | Inventory required |
| Photos/video | TBD | TBD | TBD | TBD | TBD | Inventory required |
| Git mirrors | Recovery control plane | Yes |  |  | Yes | Automated frequent mirror/offsite copy desired |
| ISO/install media | Software cache |  | Likely |  | No by default | Re-download if practical |
| VM files | Runtime infrastructure |  |  | Yes | No | Rebuild instead |
| Backup/recovery images | Bootstrap accelerator |  | Likely |  | TBD | Do not make required dependency |
| Scan/report history | Learning/evidence | TBD | TBD | TBD | No by default | Retain selectively |

This table will be replaced with actual NAS shares/datasets during discovery.

---

## 7. Classification acceptance rule

An asset is not considered fully classified until all of the following are known:

1. **Classification** — Must / Nice / Rebuild.
2. **Authoritative source** — where the recoverable truth lives.
3. **Recovery method** — restore, rebuild, re-download, or regenerate.
4. **Offsite requirement** — yes/no.
5. **Automation owner** — repository/playbook/tool responsible for protecting or reconstructing it.
6. **Validation method** — how successful recovery is proven.

The classification matrix should eventually drive both the backup automation and the whole-lab reconstruction runbook.
