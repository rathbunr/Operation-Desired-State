# Operation Desired State — Backup Policy Draft

## Principle

Operation Desired State does not treat ordinary system backups as a primary recovery mechanism.

Lab infrastructure is expected to fail and be rebuilt from code, provisioning automation, desired-state configuration, documentation, and validation.

Backup effort is therefore limited to information that has value beyond the disposable runtime environment.

## Classification model

Every candidate data set or artifact should be placed into one of three outcomes:

### 1. Must Back Up

Data or artifacts whose loss would cause unacceptable permanent loss or materially prevent reconstruction.

Typical examples may include:

- personal/irreplaceable data;
- authoritative source/configuration repositories;
- unique documentation that is not reproducible elsewhere;
- critical recovery information required to regain access to external services;
- other artifacts explicitly classified as irreplaceable.

Requirements:

- multiple independent copies;
- at least one offsite/cloud copy;
- automated and frequent replication where practical;
- periodic verification of freshness and recoverability;
- documented restore procedure.

### 2. Nice to Back Up

Data or artifacts that are useful to retain because they reduce rebuild time, preserve convenience, or aid troubleshooting, but whose loss does not prevent full reconstruction.

Potential examples may include:

- optional seed images;
- exported appliance configuration snapshots when desired-state code already exists;
- selected logs or historical reports;
- large downloadable content caches when retaining them materially reduces recovery time.

Requirements are intentionally lighter and may vary by artifact. These backups must never become hidden dependencies for successful reconstruction.

### 3. Do Not Back Up / Rebuild Instead

Anything not explicitly classified as **Must Back Up** or **Nice to Back Up** is considered disposable and should normally be rebuilt rather than protected through backup.

Examples include ordinary lab VMs, operating-system installations, reproducible platform databases, regenerated PKI state, caches, transient logs, and other state that exists only because a running system currently exists.

## Decision rule

For each asset or data set, ask:

1. If this disappeared permanently, would something irreplaceable be lost or would reconstruction materially fail?
   - Yes → **Must Back Up**
2. If not, would retaining it significantly reduce recovery effort or preserve useful history?
   - Yes → **Nice to Back Up**
3. Otherwise → **Do Not Back Up / Rebuild Instead**

## Automation objective

Backup workflows for **Must Back Up** data should be automated where practical, preferably through Ansible or another version-controlled automation mechanism.

Offsite/cloud replication should be automated and frequent. Backup success should be verified through evidence of completed transfer and periodic restore testing rather than assumed from job completion alone.

## Relationship to Operation Desired State

The program's primary resilience mechanism remains reproducibility.

Backup exists to protect what cannot or should not be recreated. It is not intended to compensate for missing desired-state automation.
