# User Journeys

| Field | Value |
| --- | --- |
| Status | Approved |
| Document version | 1.0 |
| Owner | G. Konstantinos Fotopoulos (Costas Phot) |
| Milestone | M1 - Product and security design |
| Work item | AB#12 |
| Last updated | 25 October 2026 |

## Purpose

This document describes the principal user journeys for Aegis Access v1.0.0. It translates the product requirements into end-to-end interactions that can guide domain modelling, threat modelling, interface design, implementation, and testing.

The journeys must be read together with the [product requirements](requirements.md).

## Journey conventions

Each journey identifies:

- the primary actor;
- the actor's goal;
- required preconditions;
- the expected main flow;
- important alternative and failure paths;
- the successful outcome;
- the related product requirements.

Lifecycle-state names used here are provisional until the formal domain model and state-transition definitions are approved.

## Journey summary

| ID | Journey | Primary actor | Principal lifecycle coverage |
| --- | --- | --- | --- |
| UJ-001 | Request time-bound access | Employee | Request submission |
| UJ-002 | Review a request as a manager | Manager | Manager approval or rejection |
| UJ-003 | Perform a security review | Security reviewer | Security approval or rejection |
| UJ-004 | Receive and inspect an access grant | Employee | Grant activation |
| UJ-005 | Expire a time-bound grant | System | Automatic expiration |
| UJ-006 | Revoke an active grant | Security reviewer or Administrator | Manual revocation |
| UJ-007 | Investigate access history | Auditor | Audit investigation and export |
| UJ-008 | Manage the resource catalogue | Administrator | Resource administration |

## UJ-001: Request time-bound access

### Primary actor

Employee

### Goal

Request a specific permission for an organisational resource for a justified and limited period.

### Preconditions

- The employee is authenticated.
- The employee is mapped to an active internal user.
- The resource and permission are active and requestable.
- The employee is authorised to view the relevant catalogue entry.

### Trigger

The employee identifies a resource for which access is required.

### Main flow

1. The employee opens the resource catalogue.
2. The employee searches or filters the available resources.
3. The employee selects a resource and reviews its requestable permissions.
4. The employee selects the required permission.
5. The employee enters a business justification.
6. The employee selects a requested duration within the permitted limit.
7. The system displays a summary of the proposed request.
8. The employee submits the request.
9. The system validates the request and records it with a unique identifier.
10. The system routes the request to the required first approval stage.
11. The employee can view the request and its current status.

### Alternative and failure paths

- If the resource or permission becomes inactive before submission, the system rejects the request and asks the employee to review the catalogue again.
- If the justification is missing or invalid, the system prevents submission and identifies the field requiring correction.
- If the requested duration exceeds the applicable limit, the system rejects the duration and displays the permitted boundary.
- If an equivalent pending request or active grant already exists, the system prevents or clearly warns about the duplicate according to the approved business rule.
- If the employee is not authorised to request the permission, the backend rejects the operation without exposing sensitive policy details.

### Successful outcome

A valid request is recorded, auditable, visible to the employee, and routed to the correct approval stage.

### Related requirements

FR-006, FR-009-FR-014, FR-027, FR-032, FR-033, and FR-035.

## UJ-002: Review a request as a manager

### Primary actor

Manager

### Goal

Determine whether an employee's request has a valid business need and should proceed.

### Preconditions

- The manager is authenticated.
- The manager is authorised to review the request.
- The request is awaiting managerial review.
- The manager is not the requester and no separation-of-duties rule prohibits the decision.

### Trigger

A submitted request enters the manager's review queue.

### Main flow

1. The manager opens the pending-review dashboard.
2. The manager selects a request.
3. The system displays the requester, resource, permission, justification, requested duration, sensitivity, and relevant history.
4. The manager evaluates the business need.
5. The manager selects approve or reject.
6. The manager records a decision comment.
7. The system revalidates the manager's authority and the current request state.
8. The system records the decision, actor, timestamp, and comment.
9. An approved request proceeds to security review when required; otherwise, it proceeds towards grant creation.
10. A rejected request enters a terminal rejected state.
11. The requester can view the updated status.

### Alternative and failure paths

- If the manager attempts to review their own request, the system rejects and audits the attempt.
- If the request was already decided or changed by another reviewer, the system rejects the stale update and displays the current state.
- If a policy conflict exists, the system prevents approval and provides an appropriate explanation.
- If the manager no longer has authority over the request, the backend rejects the decision.

### Successful outcome

One attributable and valid managerial decision is recorded, and the request moves to the correct next state.

### Related requirements

FR-015, FR-017-FR-020, FR-027, FR-030, FR-032, FR-034, and FR-035.

## UJ-003: Perform a security review

### Primary actor

Security reviewer

### Goal

Evaluate a security-sensitive request and approve only access that satisfies the applicable security and separation-of-duties rules.

### Preconditions

- The security reviewer is authenticated.
- The request requires security review.
- The required earlier approvals are complete.
- The request is awaiting security review.
- The reviewer is authorised and has no prohibited conflict.

### Trigger

An eligible request enters the security-review queue.

### Main flow

1. The security reviewer opens the pending security-review dashboard.
2. The reviewer selects a request.
3. The system displays the requested details, previous decisions, resource sensitivity, requested duration, and relevant policy information.
4. The system identifies any detected policy or separation-of-duties conflict.
5. The reviewer evaluates the security impact and previous approval evidence.
6. The reviewer selects approve or reject.
7. The reviewer records a decision comment.
8. The system revalidates the reviewer's authority, request state, and policy constraints.
9. The system records the decision and its audit evidence.
10. An approved request proceeds towards grant creation.
11. A rejected request enters a terminal rejected state.
12. The requester can view the updated status.

### Alternative and failure paths

- If a mandatory policy conflict exists, the system prevents approval.
- If the reviewer is also the requester or otherwise conflicts with a separation-of-duties rule, the system rejects and audits the attempt.
- If the request changes before the decision is saved, the system rejects the stale update.
- If previous approval evidence is incomplete, the request cannot proceed.

### Successful outcome

The security decision is attributable, policy-compliant, auditable, and followed by the correct lifecycle transition.

### Related requirements

FR-016-FR-020, FR-027, FR-030, FR-032, FR-034, and FR-035.

## UJ-004: Receive and inspect an access grant

### Primary actor

Employee

### Goal

Confirm that an approved request produced the expected time-bound access grant.

### Preconditions

- The employee is authenticated.
- Every required approval has succeeded.
- The approved request remains valid.
- No new policy conflict prevents grant creation.

### Trigger

The final required approval is recorded.

### Main flow

1. The system verifies that all required approvals are complete.
2. The system creates a grant for the approved subject, resource, permission, and duration.
3. The system records the activation and expiration times.
4. The system records the grant-creation audit event.
5. The employee receives an in-application notification.
6. The employee opens the personal grants dashboard.
7. The employee selects the new grant.
8. The system displays its resource, permission, state, activation time, expiration time, and originating request.
9. Protected operations recognise the grant only while it is active.

### Alternative and failure paths

- If any required approval is missing, the system does not create the grant.
- If the request was rejected, cancelled, or invalidated, grant creation is prevented.
- If grant creation fails, the system does not report a successful active grant and preserves sufficient evidence for diagnosis.
- If a duplicate active grant would be created, the system applies the approved duplicate-grant rule.

### Successful outcome

A valid, time-bound, auditable grant becomes visible to the employee and can be used only during its active lifecycle.

### Related requirements

FR-021-FR-023, FR-026-FR-028, FR-030, FR-032, and FR-034.

## UJ-005: Expire a time-bound grant

### Primary actor

System

### Supporting actor

Employee

### Goal

End access automatically when the approved duration expires.

### Preconditions

- An active grant exists.
- The grant has a valid expiration time.
- The current time reaches or passes that expiration time.

### Trigger

The grant reaches its expiration time.

### Main flow

1. The system identifies that the grant has reached its expiration time.
2. The system prevents the grant from authorising further protected operations.
3. The grant is represented as expired.
4. The system records an expiration audit event.
5. The employee receives an in-application notification.
6. The expired grant remains visible in the employee's grant history.
7. Auditors can trace the expired grant to its originating request and approvals.

### Alternative and failure paths

- If a scheduled background process is delayed, backend authorisation still rejects the grant after its expiration time.
- If expiration processing is retried, it does not create duplicate lifecycle transitions or contradictory audit evidence.
- If notification delivery fails, the grant remains expired and the failure does not restore access.

### Successful outcome

The grant no longer authorises access after its approved duration, and the expiration remains visible and auditable.

### Related requirements

FR-024, FR-026-FR-030, FR-034, NFR-004, and NFR-012.

## UJ-006: Revoke an active grant

### Primary actors

Security reviewer or Administrator

### Goal

Terminate active access before its scheduled expiration for a recorded reason.

### Preconditions

- The actor is authenticated.
- The actor is authorised to revoke the selected grant.
- The grant is currently active.

### Trigger

A security, employment, operational, or policy condition requires early termination of access.

### Main flow

1. The actor searches for and selects the active grant.
2. The system displays the grant, subject, resource, permission, expiration time, originating request, and relevant history.
3. The actor chooses to revoke the grant.
4. The actor enters a revocation reason.
5. The system displays a confirmation summary.
6. The actor confirms the revocation.
7. The backend revalidates the actor's authority and the grant's current state.
8. The system prevents the grant from authorising further operations.
9. The system records the actor, reason, timestamp, and revocation event.
10. The affected employee receives an in-application notification.
11. The revoked grant remains available in lifecycle and audit history.

### Alternative and failure paths

- If the grant is already expired or revoked, the system rejects the duplicate transition and displays its current state.
- If the actor lacks authority, the backend rejects and audits the attempt.
- If the revocation reason is missing, the system prevents confirmation.
- If notification delivery fails, revocation remains effective.

### Successful outcome

Access is terminated immediately and an attributable reason is preserved in the audit trail.

### Related requirements

FR-025-FR-030, FR-032, FR-034, and FR-035.

## UJ-007: Investigate access history

### Primary actor

Auditor

### Goal

Determine who requested, reviewed, granted, expired, or revoked access and when each event occurred.

### Preconditions

- The auditor is authenticated.
- The auditor has read-only audit permissions.
- Relevant audit data exists.

### Trigger

An audit, investigation, review, or evidence request is initiated.

### Main flow

1. The auditor opens the audit dashboard.
2. The auditor searches or filters by supported criteria, such as subject, resource, action, outcome, or date range.
3. The system displays matching audit records in chronological order.
4. The auditor selects a request, grant, or audit event.
5. The system displays the related lifecycle history and attributable actors.
6. The auditor follows the relationship from the request through approvals to the resulting grant and its final state.
7. When required, the auditor exports the filtered evidence in the documented format.
8. The system records the export action as a security-relevant event.

### Alternative and failure paths

- If no records match the criteria, the system returns an empty result without implying that an error occurred.
- If a query exceeds permitted boundaries, the system requests narrower criteria or applies documented pagination.
- If the auditor attempts to alter operational or audit data, the backend rejects the operation.
- If an export fails, the system does not present an incomplete file as a successful export.

### Successful outcome

The auditor obtains traceable, read-only evidence of the complete access lifecycle without changing operational records.

### Related requirements

FR-027-FR-033, FR-035, NFR-004, and NFR-008.

## UJ-008: Manage the resource catalogue

### Primary actor

Administrator

### Goal

Maintain accurate resource and permission definitions that can safely participate in access-governance workflows.

### Preconditions

- The administrator is authenticated.
- The administrator has resource-management permission.

### Trigger

A resource or permission must be introduced, corrected, deactivated, or reviewed.

### Main flow

1. The administrator opens the resource-management interface.
2. The administrator searches for an existing resource or chooses to create one.
3. The administrator enters or updates the resource name, description, owner, sensitivity classification, status, and requestable permissions.
4. The system validates the resource definition.
5. The administrator reviews and confirms the change.
6. The backend revalidates the administrator's authority.
7. The system saves the resource definition.
8. The system records the administrative change in the audit trail.
9. The updated catalogue is presented to authorised users.

### Alternative and failure paths

- If required metadata is missing, the system prevents activation.
- If the administrator attempts to deactivate a resource with active grants, the system applies the approved lifecycle rule and clearly identifies the impact.
- If another administrator changed the resource concurrently, the system rejects the stale update.
- If the actor lacks permission, the backend rejects and audits the attempt.

### Successful outcome

The catalogue contains a valid and attributable resource definition, and requesters see only currently available options.

### Related requirements

FR-006-FR-009, FR-027-FR-030, FR-032, FR-033, and FR-035.

## Cross-journey expectations

All journeys must satisfy the following expectations:

- backend authorisation is enforced independently of interface visibility;
- unauthorised operations fail securely;
- lifecycle transitions are validated against the current persisted state;
- security-relevant actions produce attributable audit evidence;
- sensitive authentication data and internal exception details are not exposed;
- concurrent operations cannot create contradictory lifecycle results;
- failure of a secondary operation, such as a notification delivery, cannot reverse a completed security decision;
- each interface clearly communicates the current state and the next permitted action.

## Journey-to-role coverage

| Role | Covered journeys |
| --- | --- |
| Employee | UJ-001, UJ-004, and UJ-005 |
| Manager | UJ-002 |
| Security reviewer | UJ-003 and UJ-006 |
| Auditor | UJ-007 |
| Administrator | UJ-006 and UJ-008 |

## Baseline approval

The user-journey baseline was reviewed for coverage of the principal product roles and access lifecycle stages. It remains consistent with the approved project charter and product requirements.

The baseline becomes authoritative for subsequent design and implementation work when merged into `main` through the established pull-request workflow.

Future revisions must use a traceable Azure Boards work item and reviewed pull request.
