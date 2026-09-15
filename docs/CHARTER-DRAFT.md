# Operation Desired State — Charter Draft

## Purpose

Operation Desired State exists to turn the RITCSUSA lab from a collection of successfully automated systems into a deliberately defined, reproducible, recoverable, and continuously convergent infrastructure program.

The program will coordinate existing domain repositories rather than replace them.

## Working problem statement

The environment already contains substantial automation across networking, virtualization, identity, lifecycle management, endpoint configuration, security tooling, patching, compliance, and operations. However, the end-state definition, cross-platform dependency model, reconstruction sequence, completeness criteria, and evidence required to declare the environment fully desired-state managed are not yet defined as one program.

The central question is therefore not simply:

> Is there automation for this platform?

It is:

> Can the intended environment be reconstructed and continuously converged from controlled desired-state sources, with dependencies, secrets, validation, recovery, and exceptions explicitly understood?

## Working objective

Create an evidence-based program model that can answer:

1. What systems and capabilities are in scope?
2. Which repository is authoritative for each capability?
3. What dependencies exist between platforms and repositories?
4. What manual or out-of-band steps still exist?
5. What validation proves a component is functioning as intended?
6. What evidence proves configuration is convergent and reproducible?
7. What is required to reconstruct the environment from a defined starting condition?
8. What criteria must be met before Operation Desired State can be declared complete?

## Guiding principles

- Diagnose from evidence before changing configuration.
- Prefer supported, declarative, reproducible configuration.
- Treat implementation repositories as authoritative for their domains.
- Do not duplicate implementation code into this program repository.
- Use dry-run, check, diff, or plan workflows where available.
- Validate runtime state after change.
- Validate function, not only syntax or service state.
- Verify idempotency/convergence when tooling supports it.
- Establish rollback or recovery proportional to the scenario and risk.
- For greenfield/disposable rebuild scenarios, prefer recoverability over preserving disposable state.
- Externalize secrets from repositories.
- Document unsupported gaps and intentional exceptions explicitly.
- Treat complete reconstruction as a system dependency problem, not merely a collection of independent playbooks.

## Program role

This repository is intended to become the control plane for:

- architecture and scope
- repository catalog
- authoritative ownership mapping
- dependency graph
- maturity assessment
- cross-repository validation standards
- reconstruction sequencing
- recovery expectations
- gap management
- program roadmap

It is not intended to become the implementation monorepo.

## Provisional success statement

Operation Desired State will be considered successful when the defined in-scope RITCSUSA environment can be reconstructed from an agreed baseline, using version-controlled desired-state sources and externalized secrets, then validated for intended functionality, security posture, and convergence with no undocumented critical manual dependencies.

This success statement is provisional and must be refined during scope definition.
