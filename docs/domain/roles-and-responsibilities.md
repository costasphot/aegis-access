# Roles and Responsibilities

| Field | Value |
| --- | --- |
| Status | Approved |
| Document version | 1.0 |
| Owner | G. Konstantinos Fotopoulos (Costas Phot) |
| Milestone | M1 - Product and security design |
| Work item | AB#13 |
| Last updated | 28 October 2026 |

## Purpose

This document defines the product roles used by Aegis Access v1.0.0, including their responsibilities, goals, authority boundaries, and prohibited actions.

It establishes a shared role model for product requirements, business rules, authorisation design, interface design, implementation, and testing.

## Role model principles

- A product role represents a set of responsibilities, not an individual person.
- One authenticated user may hold multiple product roles.
- Roles are derived only from validated identity claims and configured internal mappings.
- Possession of a role does not automatically grant access to every resource or operation associated with that role.
- Backend policies make the authoritative authorisation decision.
- Interface visibility does not provide authorisation.
- Access is denied when no explicit rule permits an operation.
- Organisational relationships, ownership, resource sensitivity, and separation-of-duties rules may further restrict a role.
- Security-sensitive operations require attributable audit evidence.
- No role may modify or delete protected audit history through supported application operations.

## Role summary

| Role or actor | Primary responsibility | Authority boundary |
| --- | --- | --- |
| Employee | Request and monitor personal access | Personal requests and grants |
| Manager | Evaluate the business justification for assigned requests | Requests within the manager's authorised organisational scope |
| Security reviewer | Evaluate security-sensitive requests and revoke access when authorised | Assigned security reviews and permitted grant-revocation scope |
| Auditor | Inspect access-lifecycle evidence | Read-only access to authorised audit and reporting data |
| Administrator | Maintain governance configuration and operational data | Explicitly permitted administrative operations |
| System | Perform trusted automated lifecycle operations | Predefined backend processes and service permissions |

## Employee

### Purpose

The Employee role represents a workforce user who needs governed access to an organisational resource.

### Responsibilities

An Employee is responsible for:

- selecting the appropriate resource and permission;
- providing an accurate business justification;
- requesting only the duration required for the stated purpose;
- monitoring the status of personal requests;
- reviewing personal active, expired, and revoked grants;
- complying with the conditions associated with approved access;
- reporting access that is no longer required or appears incorrect.

### Permitted capabilities

Subject to policy and organisational scope, an Employee may:

- view requestable catalogue entries;
- submit an access request;
- view personal requests and their lifecycle history;
- view personal grants and their lifecycle state;
- receive relevant workflow and lifecycle notifications.

### Authority boundaries

An Employee:

- may act only for their own identity unless a later approved requirement explicitly introduces delegated requests;
- cannot approve or security-review their own request;
- cannot create, activate, extend, expire, or revoke a grant directly;
- cannot manage resource definitions or governance policy;
- cannot inspect another user's protected request or grant data without a separately authorised role;
- cannot modify audit records.

## Manager

### Purpose

The Manager role represents a workforce user authorised to evaluate the business justification for requests within a defined organisational scope.

### Responsibilities

A Manager is responsible for:

- confirming that a request supports a legitimate business need;
- evaluating whether the requested permission and duration are proportionate;
- recording a clear approval or rejection decision;
- identifying requests that require correction or escalation;
- avoiding decisions affected by a conflict of interest;
- reviewing only requests within the assigned organisational boundary.

### Permitted capabilities

Subject to policy and organisational scope, a Manager may:

- view requests assigned for managerial review;
- inspect information necessary for the decision;
- approve or reject an eligible request;
- record a decision comment;
- inspect the history of a request they are authorised to review.

### Authority boundaries

A Manager:

- cannot review a request outside their authorised organisational scope;
- cannot review or decide their own request;
- cannot bypass a required security review;
- cannot change the requester's submitted permission or duration while recording a decision;
- cannot create or activate a grant directly;
- cannot override a mandatory separation-of-duties rule;
- cannot modify completed decisions or protected audit records.

## Security reviewer

### Purpose

The Security reviewer role represents a user authorised to evaluate security-sensitive requests and security conditions affecting active grants.

### Responsibilities

A Security reviewer is responsible for:

- evaluating requests against applicable security policy;
- considering resource sensitivity and previous approval evidence;
- identifying policy and separation-of-duties conflicts;
- recording an attributable approval or rejection;
- revoking active grants when authorised and justified;
- documenting the reason for a security-sensitive decision.

### Permitted capabilities

Subject to assigned scope and policy, a Security reviewer may:

- view requests awaiting security review;
- inspect relevant request, decision, resource, and policy information;
- approve or reject an eligible security review;
- inspect relevant grant history;
- revoke an active grant;
- record security-review and revocation reasons.

### Authority boundaries

A Security reviewer:

- cannot review their own requests;
- cannot act when a prohibited conflict exists;
- cannot bypass an incomplete required approval;
- cannot approve a request that violates a mandatory policy;
- cannot silently alter the original request;
- cannot create arbitrary grants outside the approved lifecycle;
- cannot modify or delete protected audit records.

## Auditor

### Purpose

The Auditor role represents a user who examines access-governance evidence without participating in operational decisions.

### Responsibilities

An Auditor is responsible for:

- investigating access-lifecycle activity;
- verifying who performed each security-relevant action;
- tracing requests through decisions, grants, expiration, or revocation;
- applying appropriate search and filtering criteria;
- exporting evidence only for authorised purposes;
- preserving the confidentiality and integrity of reviewed information.

### Permitted capabilities

Subject to assigned audit scope, an Auditor may:

- search and filter audit records;
- inspect request, decision, grant, and lifecycle history;
- view attributable actors, timestamps, actions, and outcomes;
- export permitted audit evidence in the documented format.

### Authority boundaries

An Auditor:

- may access operational and audit information only in a read-only capacity when acting under the Auditor role;
- cannot submit or decide requests through the Auditor role;
- cannot create, activate, extend, expire, or revoke grants;
- cannot manage resources, role mappings, or policy;
- cannot modify or delete audit records;
- cannot access evidence outside the assigned audit scope.

## Administrator

### Purpose

The Administrator role represents a user authorised to maintain product configuration and supported operational data.

### Responsibilities

An Administrator is responsible for:

- maintaining resource and permission definitions;
- maintaining approved role mappings and governance configuration;
- ensuring required resource metadata remains accurate;
- deactivating unavailable catalogue entries safely;
- revoking active grants when operationally authorised;
- recording reasons for security-relevant administrative actions;
- avoiding the use of administrative authority for ordinary business approval.

### Permitted capabilities

Subject to explicit policy, an Administrator may:

- create, update, inspect, and deactivate resource definitions;
- manage requestable permissions and required resource metadata;
- maintain supported identity and role mappings;
- inspect operational information required for administration;
- revoke an active grant with a recorded reason;
- access health and configuration information required for operation.

### Authority boundaries

An Administrator:

- does not automatically receive access to every governed resource;
- cannot approve their own access request;
- cannot bypass required managerial or security decisions;
- cannot create a grant outside the approved request lifecycle;
- cannot rewrite completed decisions or lifecycle history;
- cannot modify or delete protected audit records;
- must use only the minimum administrative capability required.

## System actor

### Purpose

The System actor represents trusted backend processes performing predefined automated operations. It is not an interactive product role assigned to a person.

### Responsibilities

The System actor is responsible for:

- validating tokens and enforcing backend policies;
- routing requests to the required approval stages;
- creating grants after all required approvals;
- enforcing grant activation and expiration times;
- preventing expired or revoked grants from authorising operations;
- recording automated lifecycle and audit events;
- initiating supported notifications and background operations.

### Authority boundaries

The System actor:

- may perform only explicitly implemented service operations;
- cannot invent approvals or bypass required decisions;
- cannot grant broader access than the approved request;
- must use a distinguishable system identity in audit records;
- must treat retries idempotently where duplicate processing could create contradictory lifecycle results;
- must not expose credentials, tokens, or sensitive implementation details.

## Organisational relationships

Product roles and organisational relationships are distinct concepts.

For example:

- a Manager role permits managerial-review behaviour;
- a reporting relationship determines which employee requests that manager may review;
- a Security reviewer role permits security-review behaviour;
- an assigned review scope determines which requests that reviewer may decide;
- an Administrator role permits configured administrative behaviour;
- an administrative scope determines which resources or settings may be managed.

A role without the required relationship or scope does not authorise the operation.

## Multiple-role users

A user may hold more than one product role. When roles overlap:

- each operation must still satisfy its complete authorisation policy;
- one role cannot be used to bypass a restriction imposed by the operation's business rules;
- separation-of-duties rules take precedence over convenience;
- self-approval and prohibited conflicts remain forbidden;
- the audit trail records the authenticated actor and performed operation;
- the interface should clearly distinguish role-specific work areas.

## Clarifications

- A resource owner is resource metadata and an organisational relationship; it is not a separate v1.0.0 product role.
- An approval is a recorded decision, not a permanent user role.
- Microsoft Entra ID authenticates the workforce identity and supplies configured claims; Aegis Access applies its own product roles and governance rules.
- Detailed operation-by-role permissions will be defined by the separate authorisation matrix.
- Detailed organisational-boundary and separation-of-duties rules will be defined in the relevant security-design documents.

## Assumptions and open questions

The following matters require confirmation during later M1 design work:

- how manager-to-employee relationships are represented;
- how security-review scopes are assigned;
- whether resource ownership affects review routing;
- which administrators may revoke grants;
- which audit scopes are required for the demonstration environment;
- how emergency administrative intervention is represented without bypassing auditability.

These questions do not permit broader access by default. Until resolved, authorisation must use the most restrictive documented interpretation.
