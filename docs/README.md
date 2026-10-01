# Aegis Access Documentation

This directory contains the product, architecture, security, quality, operations, and development documentation for Aegis Access.

The documentation is maintained alongside the source code and evolves through the same issue, review, and version-control process as the application.

## Documentation status

A document may have one of the following statuses:

| Status | Meaning |
|---|---|
| Planned | The document has been identified but has not yet been created |
| Draft | The document is under active development and has not been accepted |
| Accepted | The document represents the current agreed position |
| Superseded | The document has been replaced by a newer decision or document |
| Retired | The document is no longer applicable and has not been replaced |

Planned documents are listed below without links. A link will be added only after the corresponding document contains meaningful content.

## Foundation

| Document | Status | Purpose |
|---|---|---|
| [Project Charter](../PROJECT_CHARTER.md) | Draft | Defines the project's purpose, objectives, scope, constraints, risks, and success criteria |
| [Source-Available Notice](../LICENSE) | Accepted | Defines project ownership, permitted evaluation, reuse restrictions, third-party licensing, and branding rights |
| Product Vision | Planned | Describes the intended users, value, direction, and long-term product boundaries |
| Roadmap | Planned | Describes planned milestones and release direction |
| Changelog | Planned | Records notable changes included in each release |

## Product documentation

Planned location: `docs/product/`

| Document | Status | Purpose |
|---|---|---|
| Product Requirements | Planned | Defines functional and non-functional requirements using stable identifiers |
| Personas | Planned | Describes the users, responsibilities, needs, and limitations represented by the product |
| Domain Glossary | Planned | Establishes precise and consistent domain terminology |
| Business Rules | Planned | Defines the rules governing requests, approvals, grants, expiration, and revocation |
| User Journeys | Planned | Describes important end-to-end interactions from each user's perspective |
| Scope | Planned | Maintains detailed release boundaries, exclusions, and deferred capabilities |

## Architecture documentation

Planned location: `docs/architecture/`

| Document | Status | Purpose |
|---|---|---|
| Architecture Overview | Planned | Summarises the system structure and principal architectural decisions |
| System Context Diagram | Planned | Shows Aegis Access, its users, and external systems |
| Container Diagram | Planned | Shows the frontend, backend, database, identity provider, worker, and operational services |
| Deployment Diagram | Planned | Shows how the system is deployed across its environments |
| Data Model | Planned | Defines the principal entities, relationships, ownership, and lifecycle |
| API Conventions | Planned | Defines resource naming, versioning, errors, pagination, filtering, and security conventions |
| Integration Design | Planned | Describes interactions with Microsoft Entra ID and other external systems |
| Background Processing | Planned | Defines expiration, notification, retry, and idempotency behaviour |
| Concurrency Strategy | Planned | Defines how conflicting and duplicate operations are prevented |

## Architecture Decision Records (ADRs)

Planned location: `docs/architecture/decisions/`

Architecture Decision Records, or ADRs, document significant technical decisions together with their context, alternatives, consequences, and status.

Planned initial decisions include:

- ADR-001: Use a modular monolith.
- ADR-002: Use PostgreSQL.
- ADR-003: Use Microsoft Entra application roles.
- ADR-004: Enforce authorisation within the backend.
- ADR-005: Store application audit events as append-only records.
- ADR-006: Use GitHub as the canonical repository.
- ADR-007: Develop through small vertical slices.

ADR numbers are assigned when a decision is formally proposed. Rejected and superseded ADRs remain in the repository to preserve the decision history.

## Security documentation

Planned location: `docs/security/`

| Document | Status | Purpose |
|---|---|---|
| Security Overview | Planned | Summarises the security model, protected assets, and principal controls |
| Threat Model | Planned | Identifies trust boundaries, threats, mitigations, and residual risks |
| Authorization Matrix | Planned | Maps roles and policies to permitted and forbidden operations |
| Security Requirements | Planned | Defines testable security requirements with stable identifiers |
| Data Classification | Planned | Classifies stored and processed data according to sensitivity |
| Secrets Management | Planned | Defines how credentials and environment-specific secrets are stored and rotated |
| Audit Model | Planned | Defines security-relevant events, integrity expectations, retention, and access |
| Vulnerability Management | Planned | Defines dependency review, scanning, remediation, and disclosure processes |
| Incident Response | Planned | Defines the response to security and operational incidents |
| Privacy Considerations | Planned | Describes personal-data processing, minimisation, retention, and deletion |

The repository-level `SECURITY.md` file will define how vulnerabilities should be reported once the repository is prepared for external visibility.

## Quality documentation

Planned location: `docs/quality/`

| Document | Status | Purpose |
|---|---|---|
| Quality Strategy | Planned | Defines the overall approach to correctness, maintainability, security, and reliability |
| Test Strategy | Planned | Defines unit, integration, authorization, end-to-end, accessibility, and performance testing |
| Definition of Ready | Planned | Defines when an issue contains enough information to begin implementation |
| Definition of Done | Planned | Defines the conditions required before work can be considered complete |
| Coding Standards | Planned | Defines project-specific C#, TypeScript, Angular, SQL, and documentation conventions |
| Review Checklist | Planned | Defines the checks performed before merging a pull request |
| Accessibility Strategy | Planned | Defines accessibility targets and verification practices |
| Performance Strategy | Planned | Defines performance objectives, measurements, and regression controls |

## Operations documentation

Planned location: `docs/operations/`

| Document | Status | Purpose |
|---|---|---|
| Environment Overview | Planned | Describes local, test, staging, and demonstration environments |
| Deployment Guide | Planned | Defines repeatable deployment and rollback procedures |
| Configuration Guide | Planned | Defines non-secret application and infrastructure configuration |
| Observability Guide | Planned | Defines logs, metrics, traces, health checks, dashboards, and alerts |
| Backup and Recovery | Planned | Defines database backup, restoration, and recovery verification |
| Database Migration Runbook | Planned | Defines safe migration, failure handling, and rollback procedures |
| Entra Outage Runbook | Planned | Defines expected behaviour and response during identity-provider disruption |
| Secret Compromise Runbook | Planned | Defines containment and credential-rotation procedures |
| Background Worker Runbook | Planned | Defines investigation and recovery for failed or delayed jobs |
| Cost Management | Planned | Defines cloud budgets, alerts, resource limits, and cleanup procedures |

## Development documentation

Planned location: `docs/development/`

| Document | Status | Purpose |
|---|---|---|
| Local Setup | Planned | Defines the tools and commands required to run the project locally |
| Development Workflow | Planned | Defines the issue, branch, pull-request, review, and merge process |
| [Repository Governance](development/repository-governance.md) | Draft | Defines protected-branch rules, merge controls, deferred safeguards, and verification |
| [Repository Strategy](development/repository-strategy.md) | Accepted | Defines GitHub ownership, GitLab mirroring, synchronisation, credential handling and failure recovery |
| [Design Workflow](development/design-workflow.md) | Draft | Defines the Figma structure, design lifecycle, traceability, versioning, sharing, and implementation handoff process |
| Commit Guidelines | Planned | Defines the project's human-readable commit-message style |
| Dependency Management | Planned | Defines dependency selection, locking, updates, and vulnerability handling |
| Database Development | Planned | Defines local database use, migrations, seed data, and test isolation |
| AI Usage | Planned | Defines permitted educational and review assistance and developer accountability |
| Troubleshooting | Planned | Records verified solutions to common development problems |

### Contribution and review

| Document | Purpose |
| --- | --- |
| [Contribution guide](../CONTRIBUTING.md) | Defines the repository-wide contribution, review, and completion requirements. |
| [Git workflow](development/git-workflow.md) | Defines the traceable workflow between Azure Boards, branches, commits, pull requests, and merges. |
| [Pull-request template](../.github/pull_request_template.md) | Provides the standard structure and review checklist for every pull request. |

## Document conventions

Documentation should:

- Use clear and direct language.
- Describe the current system accurately.
- Distinguish implemented behaviour from planned behaviour.
- Use stable identifiers for requirements and business rules.
- Link related requirements, issues, ADRs, tests, and pull requests.
- Explain the reason for significant decisions.
- Record meaningful alternatives and consequences.
- Avoid including secrets, credentials, private keys, or personal data.
- Include verification instructions where a procedure must be reproducible.
- Be updated in the same change as the behaviour it describes.

## Document metadata

Substantial controlled documents should begin with a metadata table containing, where applicable:

| Field | Description |
|---|---|
| Status | Draft, Accepted, Superseded, or Retired |
| Version | Version of the individual document |
| Owner | Person responsible for maintaining the document |
| Created | Original creation date |
| Last updated | Date of the most recent meaningful update |
| Related requirements | Requirements governed or supported by the document |
| Related decisions | Architecture decisions affecting the document |
| Supersedes | Earlier document replaced by the current document |

Small reference documents do not require metadata when it would add no practical value.

## Documentation review

- A requirement changes.
- A significant architecture decision is made.
- A security boundary or policy changes.
- An API contract changes.
- A database migration changes stored behaviour.
- A deployment or operational procedure changes.
- A release is prepared.
- Existing documentation no longer matches verified system behaviour.

Outdated documentation is treated as a defect.
