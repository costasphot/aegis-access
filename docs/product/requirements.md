# Product Requirements

| Field | Value |
| --- | --- |
| Status | Approved |
| Document version | 0.1 |
| Owner | G. Konstantinos Fotopoulos (Costas Phot) |
| Milestone | M1 - Product and security design |
| Work item | AB#12 |
| Last updated | 25 October 2026 |

## Purpose

This document defines the functional and non-functional requirements for Aegis Access v1.0.0. It establishes a testable product baseline for subsequent domain, security, architecture, interface-design, and implementation work.

## Product summary

Aegis Access is an enterprise access-governance platform for requesting, reviewing, granting, expiring, revoking, and auditing time-bound access to organisational resources.

Microsoft Entra ID provides workforce authentication and application-role claims. Aegis Access provides the application-specific governance workflows, authorisation rules, access lifecycle, and audit evidence built around those identities.

## Product objectives

Aegis Access must:

- demonstrate secure enterprise authentication and authorisation practices;
- provide a complete and traceable access-request lifecycle;
- enforce time-bound access and separation-of-duties rules;
- provide clear evidence of who requested, approved, granted, or revoked access;
- support role-appropriate dashboards and workflows;
- maintain a professional, testable, and documented implementation baseline.

## Product roles

| Role | Principal goals |
| --- | --- |
| Employee | Discover available resources, request justified time-bound access, and monitor personal requests and grants. |
| Manager | Review requests from responsible employees and approve or reject them with a recorded decision. |
| Security reviewer | Review security-sensitive requests, identify policy conflicts, and approve or reject access. |
| Auditor | Inspect lifecycle histories and audit records without changing operational data. |
| Administrator | Manage resources, permissions, role mappings, policies, and operational configuration. |

## Requirement priorities

| Priority | Meaning |
| --- | --- |
| Must | Required for the v1.0.0 release. |
| Should | Important for v1.0.0 but may be deferred if release quality would otherwise be affected. |
| Could | Desirable enhancement that may be delivered after the required release scope. |

## Functional requirements

### Identity and access control

| ID | Requirement | Priority | Planned milestone | Acceptance measure |
| --- | --- | --- | --- | --- |
| FR-001 | The system shall authenticate workforce users through Microsoft Entra ID. | Must | M2 | An authorised test user can sign in and sign out using the configured Entra tenant. |
| FR-002 | The backend shall validate the issuer, audience, signature, lifetime, and required claims of every access token. | Must | M2 | Requests with valid tokens succeed, while missing, expired, malformed, or incorrectly issued tokens are rejected. |
| FR-003 | The system shall map authenticated identities and Entra application roles to internal user records and product roles. | Must | M2 | A signed-in user receives only the internal identity and roles corresponding to validated claims and configured mappings. |
| FR-004 | The backend shall enforce authorisation policies independently of interface visibility. | Must | M2 | Direct API requests that violate a policy are rejected even when the corresponding interface control is hidden. |
| FR-005 | Access shall be denied by default when no explicit authorisation rule permits an operation. | Must | M2 | An authenticated user without a permitted role or policy receives a forbidden response. |

### Resource catalogue

| ID | Requirement | Priority | Planned milestone | Acceptance measure |
| --- | --- | --- | --- | --- |
| FR-006 | Authorised users shall be able to view a catalogue of resources and their requestable permissions. | Must | M3 | A permitted user can view available resources and the permissions that may be requested. |
| FR-007 | Administrators shall be able to create, update, deactivate, and inspect resource definitions. | Must | M3 | An administrator can perform each operation, and unauthorised users cannot. |
| FR-008 | Each resource shall record an owner, sensitivity classification, status, and available permissions. | Must | M3 | A resource cannot enter the active catalogue without the required metadata. |
| FR-009 | The system shall prevent new requests for inactive resources or permissions. | Must | M3 | Attempts to request inactive catalogue items are rejected with an explanatory response. |

### Access requests

| ID | Requirement | Priority | Planned milestone | Acceptance measure |
| --- | --- | --- | --- | --- |
| FR-010 | An employee shall be able to request a defined permission for an available resource. | Must | M3 | A valid request is stored and assigned a unique identifier. |
| FR-011 | An access request shall include a business justification and requested duration. | Must | M3 | The system rejects submission when either field is missing or invalid. |
| FR-012 | The requested duration shall comply with the applicable maximum-duration policy. | Must | M3 | A request exceeding the permitted duration is rejected before review. |
| FR-013 | An employee shall be able to view the status and history of personal requests. | Must | M3 | The requester can view every recorded state transition and decision associated with a personal request. |
| FR-014 | The system shall prevent duplicate active requests for the same user, resource, and permission. | Should | M3 | A conflicting request is rejected while an equivalent request remains pending or granted. |

### Approval workflow

| ID | Requirement | Priority | Planned milestone | Acceptance measure |
| --- | --- | --- | --- | --- |
| FR-015 | Requests requiring managerial approval shall be routed to an authorised manager. | Must | M4 | A submitted request appears in the appropriate manager review queue. |
| FR-016 | Security-sensitive requests shall require security review after managerial approval. | Must | M4 | A qualifying request cannot be granted until both required approvals are recorded. |
| FR-017 | Reviewers shall be able to approve or reject a request and record a decision comment. | Must | M4 | Each review produces a timestamped decision attributed to the authenticated reviewer. |
| FR-018 | A reviewer shall not be permitted to approve a request when doing so violates a separation-of-duties rule. | Must | M4 | A prohibited self-approval or conflicting approval attempt is rejected and audited. |
| FR-019 | Rejected requests shall not proceed to later approval or grant stages. | Must | M4 | Rejection moves the request to a terminal rejected state and prevents grant creation. |
| FR-020 | Concurrent decisions shall not produce duplicate or contradictory approval results. | Must | M4 | Competing updates result in one valid transition, with stale updates safely rejected. |

### Grant lifecycle

| ID | Requirement | Priority | Planned milestone | Acceptance measure |
| --- | --- | --- | --- | --- |
| FR-021 | The system shall create an access grant only after all required approvals are complete. | Must | M5 | No active grant exists before the final required approval is recorded. |
| FR-022 | Every grant shall have an activation time, expiration time, subject, resource, and permission. | Must | M5 | A grant cannot become active without all required lifecycle information. |
| FR-023 | An employee shall be able to view personal active, expired, and revoked grants. | Must | M5 | The employee dashboard distinguishes grants by lifecycle state. |
| FR-024 | The system shall automatically expire a grant when its approved duration ends. | Must | M5 | A grant becomes unusable after its expiration time without manual intervention. |
| FR-025 | An authorised administrator or security reviewer shall be able to revoke an active grant and record a reason. | Must | M5 | Revocation immediately prevents further use and records the actor, time, and reason. |
| FR-026 | Expired or revoked grants shall not authorise protected operations. | Must | M5 | Requests relying on an expired or revoked grant are rejected by the backend. |

### Audit and reporting

| ID | Requirement | Priority | Planned milestone | Acceptance measure |
| --- | --- | --- | --- | --- |
| FR-027 | The system shall record security-relevant actions in an append-only audit trail. | Must | M6 | Creation, decisions, grants, expirations, revocations, and administrative changes produce audit records. |
| FR-028 | Each audit record shall identify the actor, action, affected entity, timestamp, and outcome. | Must | M6 | Every stored audit record contains all required fields or an explicit system-actor value. |
| FR-029 | Auditors shall be able to search and filter audit records without modifying them. | Must | M6 | An auditor can query by supported criteria and cannot create, update, or delete audit records. |
| FR-030 | Authorised users shall be able to inspect the complete history of an access request and resulting grant. | Must | M6 | The displayed history contains ordered submissions, decisions, transitions, and lifecycle events. |
| FR-031 | Auditors shall be able to export filtered audit data in a documented format. | Should | M6 | The exported data matches the active filters and contains the documented fields. |

### Interface and notifications

| ID | Requirement | Priority | Planned milestone | Acceptance measure |
| --- | --- | --- | --- | --- |
| FR-032 | Each product role shall receive a dashboard appropriate to its permitted responsibilities. | Must | M3-M6 | Users see the relevant requests, decisions, grants, or audit information for their roles. |
| FR-033 | Lists shall support search, filtering, sorting, and pagination where the expected data volume requires them. | Should | M3-M6 | Supported list views return stable and correct results for each control. |
| FR-034 | The system shall provide in-application notifications for relevant workflow decisions and lifecycle events. | Should | M4-M5 | The affected user receives a notification after each configured event. |
| FR-035 | The interface shall clearly explain validation failures and forbidden operations without exposing sensitive implementation details. | Must | M2-M6 | Users receive actionable messages, while internal exceptions, tokens, and policy details remain undisclosed. |

## Non-functional requirements

| ID | Requirement | Priority | Planned milestone | Acceptance measure |
| --- | --- | --- | --- | --- |
| NFR-001 | The system shall apply least-privilege and deny-by-default authorisation. | Must | M2-M7 | Automated authorisation tests verify permitted and forbidden operations for every product role. |
| NFR-002 | Authentication and authorisation decisions shall be enforced by the backend. | Must | M2-M7 | API tests confirm that client-side manipulation cannot bypass access policies. |
| NFR-003 | Secrets and environment-specific credentials shall not be stored in source control. | Must | M2-M7 | Repository and automated secret scans report no committed credentials. |
| NFR-004 | Security-relevant events shall be attributable and protected from ordinary application-level modification. | Must | M5-M7 | Operational roles cannot update or delete audit records through supported interfaces or APIs. |
| NFR-005 | The system shall validate untrusted input and return controlled error responses. | Must | M2-M7 | Automated tests cover representative invalid, malformed, oversized, and unauthorised inputs. |
| NFR-006 | Common interactive API requests shall complete within two seconds at the 95th percentile under the documented demonstration workload. | Should | M7 | Performance tests meet the response-time target using the recorded workload and environment. |
| NFR-007 | The interface shall be usable with keyboard navigation and shall target WCAG 2.2 Level AA. | Should | M3-M7 | Documented accessibility checks find no unresolved release-blocking violations in principal workflows. |
| NFR-008 | The application shall provide structured logs, health checks, metrics, and trace correlation without logging credentials or access tokens. | Must | M7 | Operational checks demonstrate each signal and confirm that sensitive authentication data is excluded. |
| NFR-009 | Critical domain, security, and lifecycle behaviour shall be covered by automated tests. | Must | M2-M7 | The continuous-integration pipeline executes and passes the defined unit, integration, and security-focused test suites. |
| NFR-010 | The application shall be reproducibly buildable and runnable using documented commands and containerised dependencies where appropriate. | Must | M2-M7 | A clean environment can build, test, and start the application by following repository documentation. |
| NFR-011 | Database changes shall use reviewed, repeatable migrations. | Must | M2-M7 | A clean database can be created and upgraded using committed migrations. |
| NFR-012 | The system shall handle transient dependency failures without corrupting request, decision, grant, or audit state. | Must | M4-M7 | Failure tests demonstrate safe rollback, retry, or rejection behaviour for critical operations. |
| NFR-013 | Application behaviour, configuration, security decisions, and operational procedures shall remain documented and version controlled. | Must | M1-M8 | Repository documentation and ADRs are updated and reviewed with the affected changes. |
| NFR-014 | The release shall be deployable to a documented staging or demonstration environment through an automated pipeline. | Must | M7-M8 | The release pipeline deploys a versioned build and records its validation result. |

## Business rules

- Access is granted only for a defined subject, resource, permission, and duration.
- Every request must contain a business justification.
- A user cannot approve their own request.
- Required approval stages depend on the resource and permission sensitivity.
- A request becomes grantable only after every required approval succeeds.
- A rejection ends the approval workflow.
- Expired and revoked grants provide no authorisation.
- Lifecycle transitions must follow the documented state model.
- Organisational and separation-of-duties boundaries must be enforced by the backend.
- Security-relevant actions must create attributable audit records.

## Assumptions

- A dedicated Microsoft Entra tenant or suitable development tenant is available.
- Test identities and application roles can be configured without production access.
- Users have one or more supported product roles.
- Resource and permission definitions are maintained within Aegis Access.
- The v1.0.0 release demonstrates governance workflows without provisioning real external-system permissions.

## Dependencies

- Microsoft Entra ID for workforce authentication and application-role claims.
- Angular for the browser-based interface.
- ASP.NET Core for the backend API.
- A relational database for operational and audit data.
- GitHub for source control, pull requests, and automated workflows.
- Azure Boards for roadmap and work-item traceability.
- Figma for interface design and prototypes.
- A documented hosting environment for the staging or demonstration release.

## Constraints

- The project is independently designed and developed.
- The repository is public and source-available but is not open-source.
- The initial implementation uses a modular monolith rather than microservices.
- Security-sensitive decisions must be enforced by trusted backend components.
- The project must remain suitable for educational, portfolio, and potential academic evaluation.
- Scope must remain achievable through incremental milestone delivery.

## Exclusions

Aegis Access v1.0.0 will not:

- replace Microsoft Entra Privileged Identity Management;
- manage production Entra tenants;
- assign real privileged permissions in external systems;
- provision permissions to third-party applications;
- operate as a multi-tenant software-as-a-service product;
- provide customer identity and access management;
- store passwords or act as a password vault;
- provide native mobile or desktop applications;
- include billing or subscription management;
- make autonomous machine-learning or AI access decisions;
- implement dynamic risk-adaptive authentication;
- use a microservices architecture.

## Milestone traceability

| Milestone | Requirement coverage |
| --- | --- |
| M1 - Product and security design | Requirements, journeys, domain rules, authorisation model, architecture, threat model, ADRs, and interface baseline |
| M2 - Authenticated walking skeleton | FR-001-FR-005 and the supporting security, build, database, and testing requirements |
| M3 - Resource and request management | FR-006-FR-014 and the applicable interface requirements |
| M4 - Approval workflow | FR-015-FR-020, approval notifications, concurrency safety, and workflow audit events |
| M5 - Grant lifecycle | FR-021-FR-026, lifecycle notifications, expiration, revocation, and grant audit events |
| M6 - Audit and reporting | FR-027-FR-031 and auditor-facing search, history, and export capabilities |
| M7 - Operational hardening | Performance, accessibility, observability, resiliency, security validation, deployment, and operational documentation |
| M8 - v1.0.0 release | Final validation, release evidence, versioning, deployment, and portfolio presentation |

## Baseline approval

The requirements baseline was reviewed for consistency with the approved project charter and the related user journeys. Its requirements are uniquely identified, prioritised, testable, and mapped to the planned milestones.

The baseline becomes authoritative for subsequent design and implementation work when merged into `main` through the established pull-request workflow.

Future revisions must use a traceable Azure Boards work item and reviewed pull request.
