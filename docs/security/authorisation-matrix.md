# Access-Control Matrix

| Field | Value |
| --- | --- |
| Status | Approved |
| Document version | 1.0 |
| Owner | G. Konstantinos Fotopoulos (Costas Phot) |
| Milestone | M1 - Product and security design |
| Work item | AB#14 |
| Last updated | 14 November 2026 |

## Purpose

This document defines the implementation-independent access-control matrix for Aegis Access v1.0.0.

It assigns stable identifiers to protected operations and maps every operation to the product roles and trusted System actor that may perform it.

A matrix permission is never sufficient by itself. Every applicable identity, role, scope, assignment, relationship, lifecycle, separation-of-duties, time, and policy condition must also succeed.

It must be read together with:

- the [authorisation model](authorisation-model.md);
- the [product requirements](../product/requirements.md);
- the [user journeys](../product/user-journeys.md);
- the [roles and responsibilities](../domain/roles-and-responsibilities.md);
- the [domain glossary](../domain/glossary.md);
- the [business rules](../domain/business-rules.md);
- the [lifecycle states](../domain/lifecycle-states.md).

## Scope

This matrix defines:

- stable identifiers for protected product operations;
- the product roles or System actor that may perform each operation;
- the product roles or actors that receive no permission for each operation;
- contextual conditions required in addition to role possession;
- operation-level traceability to requirements, journeys, business rules, and lifecycle controls;
- globally prohibited behaviour that no role may override.

The matrix does not define:

- Microsoft Entra tenant administration;
- application-registration configuration;
- concrete token or claim names;
- ASP.NET Core policy code;
- Angular route-guard implementation;
- database permissions;
- external system administration;
- emergency-access capabilities.

## Operation-identifier convention

Protected operations use the following format:

`OP-<DOMAIN>-<NUMBER>`

The domain codes are:

| Code | Domain |
| --- | --- |
| `CAT` | Resource catalogue |
| `REQ` | Access requests |
| `APR` | Approval workflow |
| `GRT` | Access grants |
| `AUD` | Audit and reporting |
| `ADM` | Administration |
| `NTF` | Notifications |
| `SYS` | Automated system processing |

Numbers contain three digits and begin at `001` within each domain.

An operation identifier:

- represents one coherent protected action;
- remains stable across interface, API, implementation, audit, and test changes;
- must appear in the applicable backend policy and authorisation tests;
- must be recorded in audit evidence when required;
- must not be reused for a different operation;
- remains reserved when its operation is retired;
- requires a new identifier when one operation is divided into independently authorised actions.

## Role abbreviations

| Abbreviation | Role or actor |
| --- | --- |
| `EMP` | Employee |
| `MGR` | Manager |
| `SEC` | Security reviewer |
| `AUD` | Auditor |
| `ADM` | Administrator |
| `SYS` | System actor |

Each column describes the authority granted by that role independently.

A user holding multiple roles receives only the permissions explicitly granted by their effective roles, subject to every applicable mandatory restriction.

Holding one permitting role does not override a prohibition based on scope, assignment, relationship, lifecycle state, separation of duties, or another mandatory security rule.

## Decision notation

| Symbol | Meaning |
| `P` | The role or actor may permit the operation when every applicable condition succeeds. |
| `D` | The role or actor does not grant permission for the operation; deny by default. |

A `P` is a candidate permission rather than an unconditional allow.

A `D` means that the individual role provides no authority for the operation. Another role may provide a candidate permission only when no mandatory prohibition applies.

## Context conditions

Every human-role permission requires:

- `CTX-001`;
- `CTX-002`;
- `CTX-003`;
- `CTX-004`;
- `CTX-005` where audit evidence is required.

Every System-actor permission requires:

- `CTX-003`;
- `CTX-004`;
- `CTX-005` where audit evidence is required;
- `CTX-006`.

Additional operation-specific conditions are listed in the matrices.

| ID | Condition |
| --- | --- |
| `CTX-001` | The validated external identity maps to exactly one active internal user. |
| `CTX-002` | The user currently holds the required approved product role. |
| `CTX-003` | Current trusted backend state is loaded and any required concurrency control succeeds. |
| `CTX-004` | No separation-of-duties, conflict-of-interest, scope, lifecycle, or mandatory policy rule prohibits the operation. |
| `CTX-005` | Required audit evidence can be persisted according to the operation's audit policy. |
| `CTX-006` | The trusted System actor has the exact service permission required for the automated operation. |
| `CTX-007` | The target request, grant, notification, or profile belongs to the acting user. |
| `CTX-008` | The resource and permission are visible, active, and requestable for the acting user. |
| `CTX-009` | Request data, justification, duration, duplicate checks, and submission rules are valid. |
| `CTX-010` | The Manager is assigned to the request and acts within the authorised organisational scope. |
| `CTX-011` | The Security reviewer is assigned to the request or grant and acts within the authorised security scope. |
| `CTX-012` | The entity is in the exact lifecycle state required by the operation. |
| `CTX-013` | Every required preceding approval has completed successfully. |
| `CTX-014` | The approved request is eligible to create exactly one matching grant. |
| `CTX-015` | The grant's state and activation or expiration time permit the operation. |
| `CTX-016` | The actor has revocation authority for the active grant and supplies a valid reason. |
| `CTX-017` | The Auditor acts within an authorised read-only audit scope and valid query or export limits. |
| `CTX-018` | The Administrator acts within an explicitly assigned administrative scope and supplies valid configuration data. |
| `CTX-019` | The notification belongs to, or is addressed to, the acting user. |
| `CTX-020` | Automated processing is idempotent, retry-safe, and limited to the intended entity and transition. |

## Resource-catalogue operations

| Operation ID | Protected operation | EMP | MGR | SEC | AUD | ADM | SYS | Additional conditions | Traceability |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| `OP-CAT-001` | Browse the requestable resource catalogue. | P | D | D | D | D | D | `CTX-008` | FR-006, FR-009; BR-010, BR-012; UJ-001 |
| `OP-CAT-002` | View requestable resource and permission details. | P | D | D | D | D | D | `CTX-008` | FR-006, FR-008; BR-008-BR-012; UJ-001 |
| `OP-CAT-003` | List administrative resource definitions. | D | D | D | D | P | D | `CTX-018` | FR-007, FR-008; BR-008-BR-011; UJ-008 |
| `OP-CAT-004` | View an administrative resource definition. | D | D | D | D | P | D | `CTX-018` | FR-007, FR-008; BR-008-BR-011; UJ-008 |
| `OP-CAT-005` | Create a resource definition. | D | D | D | D | P | D | `CTX-018` | FR-007, FR-008; BR-008, BR-009; UJ-008 |
| `OP-CAT-006` | Update a resource or permission definition. | D | D | D | D | P | D | `CTX-018` | FR-007, FR-008; BR-008, BR-009, BR-011; UJ-008 |
| `OP-CAT-007` | Activate a valid resource or permission. | D | D | D | D | P | D | `CTX-018` | FR-007-FR-009; BR-008-BR-010; UJ-008 |
| `OP-CAT-008` | Deactivate a resource or permission. | D | D | D | D | P | D | `CTX-018` | FR-007, FR-009; BR-010, BR-011; UJ-008 |

Catalogue visibility does not guarantee that a later request or approval will be permitted.

## Access-request operations

| Operation ID | Protected operation | EMP | MGR | SEC | AUD | ADM | SYS | Additional conditions | Traceability |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| `OP-REQ-001` | Submit an access request for the acting user. | P | D | D | D | D | D | `CTX-007`-`CTX-009` | FR-010-FR-012, FR-014; BR-013-BR-020; UJ-001; RT-001 |
| `OP-REQ-002` | List the acting user's access requests. | P | D | D | D | D | D | `CTX-007` | FR-013; BR-006; UJ-001 |
| `OP-REQ-003` | View the acting user's request details and history. | P | D | D | D | D | D | `CTX-007` | FR-013, FR-030; BR-047; UJ-001 |
| `OP-REQ-004` | View a request assigned for manager review. | D | P | D | D | D | D | `CTX-010`, `CTX-012` | FR-015, FR-017; BR-022, BR-025; UJ-002 |
| `OP-REQ-005` | View a request assigned for security review. | D | D | P | D | D | D | `CTX-011`, `CTX-012`, `CTX-013` | FR-016, FR-017; BR-022, BR-025, BR-029; UJ-003 |
| `OP-REQ-006` | Route a submitted request to its required approval stage. | D | D | D | D | D | P | `CTX-006`, `CTX-012`, `CTX-020` | FR-015, FR-016; BR-021; UJ-001-UJ-003; RT-002, RT-003 |

A Manager or Security reviewer receives no authority to alter the request's original subject, resource, permission, justification, or duration.

## Approval-workflow operations

| Operation ID | Protected operation | EMP | MGR | SEC | AUD | ADM | SYS | Additional conditions | Traceability |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| `OP-APR-001` | List assigned manager-review requests. | D | P | D | D | D | D | `CTX-010` | FR-015; BR-022; UJ-002 |
| `OP-APR-002` | Approve the manager-review stage. | D | P | D | D | D | D | `CTX-010`, `CTX-012` | FR-015, FR-017-FR-020; BR-022-BR-027; UJ-002; RT-004, RT-005 |
| `OP-APR-003` | Reject the manager-review stage. | D | P | D | D | D | D | `CTX-010`, `CTX-012` | FR-015, FR-017-FR-020; BR-022-BR-028; UJ-002; RT-006 |
| `OP-APR-004` | List assigned security-review requests. | D | D | P | D | D | D | `CTX-011`, `CTX-013` | FR-016; BR-022, BR-029; UJ-003 |
| `OP-APR-005` | Approve the security-review stage. | D | D | P | D | D | D | `CTX-011`, `CTX-012`, `CTX-013` | FR-016-FR-020; BR-022-BR-027, BR-029; UJ-003; RT-007 |
| `OP-APR-006` | Reject the security-review stage. | D | D | P | D | D | D | `CTX-011`, `CTX-012`, `CTX-013` | FR-016-FR-020; BR-022-BR-029; UJ-003; RT-008 |

Self-review, self-approval, prohibited conflicts, and decisions outside the assigned scope are denied even when the actor holds the listed role.

## Access-grant operations

| Operation ID | Protected operation | EMP | MGR | SEC | AUD | ADM | SYS | Additional conditions | Traceability |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| `OP-GRT-001` | Create a grant from an approved request. | D | D | D | D | D | P | `CTX-006`, `CTX-014`, `CTX-020` | FR-021, FR-022; BR-030-BR-033; UJ-004; RT-009; GT-001, GT-002 |
| `OP-GRT-002` | Activate a scheduled grant. | D | D | D | D | D | P | `CTX-006`, `CTX-012`, `CTX-015`, `CTX-020` | FR-022, FR-026; BR-033, BR-034; UJ-004; GT-003 |
| `OP-GRT-003` | Expire a grant. | D | D | D | D | D | P | `CTX-006`, `CTX-012`, `CTX-015`, `CTX-020` | FR-024, FR-026; BR-034, BR-035, BR-038-BR-040; UJ-005; GT-004, GT-005 |
| `OP-GRT-004` | List the acting user's grants. | P | D | D | D | D | D | `CTX-007` | FR-023; UJ-004, UJ-005 |
| `OP-GRT-005` | View the acting user's grant details and history. | P | D | D | D | D | D | `CTX-007` | FR-023, FR-030; BR-040, BR-047; UJ-004, UJ-005 |
| `OP-GRT-006` | View a grant for possible revocation. | D | D | P | D | P | D | `CTX-011` or `CTX-018`, `CTX-012` | FR-025; BR-036; UJ-006 |
| `OP-GRT-007` | Revoke an active grant. | D | D | P | D | P | D | `CTX-016`, `CTX-012` | FR-025, FR-026; BR-036-BR-040; UJ-006; GT-006 |
| `OP-GRT-008` | Evaluate a grant for a protected resource operation. | D | D | D | D | D | P | `CTX-006`, `CTX-015`, `CTX-020` | FR-024, FR-026; BR-034, BR-035, BR-037-BR-039; UJ-004-UJ-006 |

An expired, revoked, pending, mismatched, or otherwise invalid grant is never treated as authorising evidence.

## Audit and reporting operations

| Operation ID | Protected operation | EMP | MGR | SEC | AUD | ADM | SYS | Additional conditions | Traceability |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| `OP-AUD-001` | Search and filter audit records. | D | D | D | P | D | D | `CTX-017` | FR-027-FR-029; BR-041-BR-045; UJ-007 |
| `OP-AUD-002` | View an audit record. | D | D | D | P | D | D | `CTX-017` | FR-027-FR-030; BR-041-BR-045; UJ-007 |
| `OP-AUD-003` | Inspect a complete request-and-grant lifecycle history. | D | D | D | P | D | D | `CTX-017` | FR-029, FR-030; BR-045, BR-047; UJ-007 |
| `OP-AUD-004` | Export filtered audit evidence. | D | D | D | P | D | D | `CTX-017` | FR-031; BR-045, BR-046; UJ-007 |

No supported product role or System operation may update or delete protected audit evidence.

Employee, Manager, Security reviewer, and Administrator views of operational data do not provide unrestricted access to the audit-record repository.

## Administrative operations

| Operation ID | Protected operation | EMP | MGR | SEC | AUD | ADM | SYS | Additional conditions | Traceability |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| `OP-ADM-001` | List internal users. | D | D | D | D | P | D | `CTX-018` | FR-003; BR-003, BR-006 |
| `OP-ADM-002` | View an internal user and identity mapping. | D | D | D | D | P | D | `CTX-018` | FR-003; BR-003, BR-006 |
| `OP-ADM-003` | Provision an internal identity mapping. | D | D | D | D | P | D | `CTX-018` | FR-003; BR-003 |
| `OP-ADM-004` | Activate or reactivate an internal user. | D | D | D | D | P | D | `CTX-018` | FR-003-FR-005; BR-003-BR-006 |
| `OP-ADM-005` | Deactivate an internal user. | D | D | D | D | P | D | `CTX-018` | FR-003-FR-005; BR-003-BR-006 |
| `OP-ADM-006` | Manage external-to-product role-mapping configuration. | D | D | D | D | P | D | `CTX-018` | FR-003-FR-005; BR-003-BR-007 |
| `OP-ADM-007` | Manage approved workflow-policy parameters. | D | D | D | D | P | D | `CTX-018` | FR-012, FR-015, FR-016; BR-021-BR-024 |

Administrative authority does not permit an Administrator to:

- approve a manager-review stage;
- approve a security-review stage;
- modify or delete protected audit evidence;
- reactivate an expired or revoked grant;
- bypass separation-of-duties or lifecycle restrictions;
- perform Microsoft Entra tenant administration through Aegis Access;
- create an undocumented emergency override.

## Notification operations

| Operation ID | Protected operation | EMP | MGR | SEC | AUD | ADM | SYS | Additional conditions | Traceability |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| `OP-NTF-001` | View the acting user's notifications. | P | P | P | P | P | D | `CTX-019` | FR-034; UJ-004-UJ-006 |
| `OP-NTF-002` | Create and deliver an application notification. | D | D | D | D | D | P | `CTX-006`, `CTX-019`, `CTX-020` | FR-034; BR-052; UJ-004-UJ-006 |

Notification delivery failure does not reverse a completed security decision or lifecycle transition.

## Automated recovery operations

| Operation ID | Protected operation | EMP | MGR | SEC | AUD | ADM | SYS | Additional conditions | Traceability |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| `OP-SYS-001` | Retry eligible routing, grant, activation, expiration, audit, or notification processing. | D | D | D | D | D | P | `CTX-006`, `CTX-020` | BR-048-BR-053; NFR-012; UJ-001-UJ-006 |

A retry must not create duplicate requests, decisions, grants, transitions, notifications, or audit evidence.

## Global prohibitions

The following prohibitions apply regardless of role possession:

| ID | Prohibited behaviour |
| --- | --- |
| `PRO-001` | Performing a protected operation without a validated principal or trusted System actor. |
| `PRO-002` | Treating an interface control, route guard, or client-supplied role as authoritative. |
| `PRO-003` | Reviewing or deciding one's own request. |
| `PRO-004` | Acting outside an assigned organisational, administrative, security, or audit scope. |
| `PRO-005` | Bypassing a separation-of-duties or conflict-of-interest restriction. |
| `PRO-006` | Altering submitted request data while recording a review decision. |
| `PRO-007` | Skipping a required approval or lifecycle stage. |
| `PRO-008` | Creating more than one grant from one approved request. |
| `PRO-009` | Using a pending, expired, revoked, mismatched, or invalid grant as authorising evidence. |
| `PRO-010` | Returning a terminal request or grant to an active lifecycle state. |
| `PRO-011` | Updating or deleting protected audit evidence through supported product operations. |
| `PRO-012` | Allowing a human role to impersonate the System actor or attribute an automated action to an invented human. |
| `PRO-013` | Allowing the System actor to invent or perform a human approval decision. |
| `PRO-014` | Exposing credentials, complete access tokens, unnecessary claims, policy internals, or exception details. |
| `PRO-015` | Using an undocumented administrative or emergency override. |

A permitting matrix entry cannot override a global prohibition.

## Multiple-role evaluation

When a user holds multiple product roles:

1. The backend identifies the protected operation.
2. The backend determines whether at least one current role has a `P` entry.
3. The backend loads every applicable contextual condition.
4. The backend evaluates all mandatory restrictions and global prohibitions.
5. The operation is permitted only when a role supplies a candidate permission and every required condition succeeds.
6. Otherwise, the operation is denied.

A `P` from one role does not erase a restriction associated with the actor, target, relationship, scope, or lifecycle state.

For example:

- an Employee who is also a Manager cannot approve their own request;
- an Administrator who is also a Security reviewer cannot bypass an assignment or conflict restriction;
- an Auditor who holds another operational role receives no write authority from the Auditor role;
- a Manager who lacks the Employee role receives no employee self-service authority from the Manager role alone.

## Backend and interface use

The backend must use the operation identifier as the stable key for:

- authorisation-policy selection;
- policy-handler implementation;
- audit categorisation;
- automated authorisation tests;
- security-review traceability;
- denied-operation diagnostics.

The Angular application may use operation information to improve navigation and presentation. It must not calculate or assert the authoritative result.

The backend must deny:

- an unknown operation identifier;
- an operation without a registered policy;
- an operation whose required context cannot be loaded;
- an operation for which no current role provides a `P`;
- an operation blocked by a mandatory condition or global prohibition.

## Traceability expectations

Each implementation of a protected operation must trace to:

- its stable operation identifier;
- the applicable matrix row;
- its functional and non-functional requirements;
- its business rules;
- its user journey;
- its lifecycle transition or invariant where applicable;
- its backend authorisation policy;
- its audit policy;
- its positive and negative automated tests.

Changes to permissions, roles, conditions, or operation meaning require coordinated updates to the authorisation model, this matrix, affected requirements, tests, audit definitions, and implementation.

## Assumptions and unresolved questions

The matrix currently assumes that:

- a role column represents authority granted by that role independently;
- users may hold multiple product roles;
- the Employee role supplies self-service capabilities;
- manager and security-review assignments are available as trusted backend context;
- organisational, administrative, security, and audit scopes can be evaluated by the backend;
- System-actor permissions are narrower than human administrative permissions;
- no supported role may modify or delete protected audit evidence;
- no emergency-access role exists in v1.0.0.

Architecture and implementation design must resolve:

- the concrete ASP.NET Core policy names associated with operation identifiers;
- the storage and retrieval mechanism for assignments, relationships, and scopes;
- the exact Microsoft Entra application-role values;
- the role-mapping administration and approval process;
- the representation and validation of administrative scope;
- the authorisation-context caching and invalidation strategy;
- the detailed audit history for permitted and denied operations;
- whether resource or permission deactivation revokes affected active grants;
- whether any emergency-access capability belongs in a later release;
- whether integration with external protected resources requires additional operations.

Until an unresolved question is decided through an approved change, the most restrictive documented interpretation applies.

## Change control

A matrix change must:

- use a traceable Azure Boards work item;
- preserve existing operation identifiers unless an operation is formally retired;
- assign new identifiers to new or independently authorised operations;
- identify every affected role, condition, prohibition, requirement, rule, journey, lifecycle control, audit event, and test;
- update the authorisation model when decision semantics change;
- receive security-focused review;
- complete the established pull-request workflow.
