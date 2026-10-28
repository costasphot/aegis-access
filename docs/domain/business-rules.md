# Business Rules

| Field | Value |
| --- | --- |
| Status | Approved |
| Document version | 1.0 |
| Owner | G. Konstantinos Fotopoulos (Costas Phot) |
| Milestone | M1 - Product and security design |
| Work item | AB#13 |
| Last updated | 28 October 2026 |

## Purpose

This document defines the mandatory business rules governing Aegis Access v1.0.0. These rules translate the approved requirements and user journeys into stable, testable domain constraints.

The rules must be read together with:

- the [product requirements](../product/requirements.md);
- the [user journeys](../product/user-journeys.md);
- the [roles and responsibilities](roles-and-responsibilities.md);
- the [domain glossary](glossary.md);
- the [lifecycle states](lifecycle-states.md).

## Rule identifiers

Each business rule uses a stable identifier in the form `BR-NNN`.

Identifiers:

- are unique within the project;
- are never reassigned to a different rule;
- remain stable when wording is clarified without changing meaning;
- are retired rather than reused when a rule is removed;
- must be referenced by relevant implementation and tests.

## Identity and authorisation rules

| ID | Rule | Traceability | Verification |
| --- | --- | --- | --- |
| BR-001 | A protected operation shall require an authenticated principal. | FR-001, FR-002; UJ-001-UJ-008 | A request without an authenticated principal is rejected. |
| BR-002 | Identity claims shall be trusted only after the backend successfully validates the token's signature, issuer, audience, lifetime, and required claims. | FR-002; UJ-001-UJ-008 | Tokens failing any required validation are rejected before domain processing. |
| BR-003 | A protected product operation shall require the authenticated identity to have an active internal user mapping. | FR-003; UJ-001-UJ-008 | An authenticated identity without a valid mapping cannot perform the operation. |
| BR-004 | An operation shall be denied when no explicit authorisation policy permits it. | FR-004, FR-005; UJ-001-UJ-008 | A user lacking a permitting policy receives a forbidden result. |
| BR-005 | Backend authorisation shall be enforced independently of interface visibility or client-supplied state. | FR-004; UJ-001-UJ-008 | A direct API request cannot bypass a hidden or disabled interface control. |
| BR-006 | Authorisation shall evaluate the actor's validated roles, assigned scope, organisational relationships, current entity state, and applicable policy. | FR-003-FR-005; UJ-002-UJ-003, UJ-006-UJ-008 | Removing any required condition causes the operation to be rejected. |
| BR-007 | Holding multiple roles shall not permit an actor to bypass a separation-of-duties, conflict-of-interest, scope, or lifecycle restriction. | FR-018; UJ-002, UJ-003, UJ-006 | A multi-role actor remains unable to perform an explicitly prohibited operation. |

## Resource-catalogue rules

| ID | Rule | Traceability | Verification |
| --- | --- | --- | --- |
| BR-008 | An active resource shall have a name, description, owner, sensitivity classification, status, and at least one defined permission. | FR-006-FR-008; UJ-001, UJ-008 | Activation is rejected when required metadata is missing. |
| BR-009 | A permission shall belong to exactly one resource definition within the Aegis Access catalogue. | FR-006-FR-008; UJ-001, UJ-008 | A permission must be associated with exactly one resource; associations with zero or multiple resources are rejected. |
| BR-010 | Only active resources and active requestable permissions may accept new access requests. | FR-009; UJ-001 | Submission against an inactive resource or permission is rejected. |
| BR-011 | Deactivating a resource or permission shall preserve its existing request, decision, grant, and audit history. | FR-007, FR-027-FR-030; UJ-007, UJ-008 | Historical records remain queryable after catalogue deactivation. |
| BR-012 | Catalogue visibility shall not guarantee that a visible permission is approvable for the current user. | FR-004-FR-006; UJ-001 | A visible permission may still be rejected by backend policy. |

## Access-request rules

| ID | Rule | Traceability | Verification |
| --- | --- | --- | --- |
| BR-013 | In v1.0.0, the requester and subject of an access request shall be the same user. | FR-010; UJ-001 | An attempt to submit a request for another user is rejected. |
| BR-014 | An access request shall identify exactly one subject, exactly one resource, and exactly one permission belonging to that resource. | FR-010; UJ-001 | Submission is rejected when a required association is missing or duplicated, or when the selected permission does not belong to the selected resource. |
| BR-015 | An access request shall include a non-empty business justification that satisfies the configured validation limits. | FR-011; UJ-001 | Empty, whitespace-only, or invalid justifications are rejected. |
| BR-016 | A requested duration shall be greater than zero and shall not exceed the maximum duration permitted by applicable policy. | FR-011, FR-012; UJ-001 | Zero, negative, or excessive durations are rejected. |
| BR-017 | A user shall not have more than one equivalent pending request for the same resource and permission. | FR-014; UJ-001 | A duplicate submission is rejected while an equivalent request remains pending. |
| BR-018 | A user shall not create an equivalent request while an active grant already provides the same resource and permission. | FR-014, FR-023; UJ-001, UJ-004 | Submission is rejected while an equivalent active grant exists. |
| BR-019 | Every accepted request shall receive a unique identifier and an initial lifecycle state. | FR-010, FR-027; UJ-001 | A successfully submitted request has one unique identifier and an initial lifecycle state. |
| BR-020 | Request submission shall record the requester, subject, resource, permission, justification, requested duration, and submission time. | FR-010, FR-011, FR-027, FR-028; UJ-001 | The request is not accepted unless all required information can be persisted. |

## Approval-workflow rules

| ID | Rule | Traceability | Verification |
| --- | --- | --- | --- |
| BR-021 | The required approval stages shall be determined from the applicable resource sensitivity and workflow policy. A security review, when required, shall follow successful manager approval. | FR-015, FR-016; UJ-001-UJ-003 | The request receives the approval stages required by its policy context. |
| BR-022 | A review decision shall require an authenticated reviewer with the required role, assignment, scope, and organisational relationship. | FR-015-FR-018; UJ-002, UJ-003 | Removing any required reviewer condition causes the decision to be rejected. |
| BR-023 | An actor shall not review or approve a request for which they are the requester or subject. | FR-018; UJ-002, UJ-003 | A self-review or self-approval attempt is rejected and audited. |
| BR-024 | An actor shall not record a decision when a separation-of-duties or conflict-of-interest rule prohibits it. | FR-018; UJ-002, UJ-003 | A prohibited conflict causes the decision to be rejected and audited. |
| BR-025 | A decision may be recorded only while the request is awaiting that specific review stage. | FR-017, FR-019, FR-020; UJ-002, UJ-003 | A decision against a completed, rejected, or different stage is rejected. |
| BR-026 | Every completed review shall produce exactly one approval or rejection decision with an attributable reviewer, timestamp, and non-empty decision comment. | FR-017; UJ-002, UJ-003 | A decision missing any required evidence is rejected. |
| BR-027 | A reviewer shall not alter the original subject, resource, permission, justification, or requested duration while recording a decision. | FR-017; UJ-002, UJ-003 | Decision processing changes only review and lifecycle data. |
| BR-028 | A rejected request shall enter a terminal rejected state and shall not proceed to another review or grant creation. | FR-019; UJ-002, UJ-003 | Later approval and grant attempts for the rejected request are rejected. |
| BR-029 | A security review shall not begin until every required preceding approval stage has completed successfully. | FR-016, FR-019; UJ-003 | A security-review decision is rejected when prerequisite approval is missing. |
| BR-030 | A request shall become eligible for grant creation only after every required approval stage has completed successfully. | FR-021; UJ-002-UJ-004 | Grant creation is rejected when any required approval is absent or unsuccessful. |

## Grant-lifecycle rules

| ID | Rule | Traceability | Verification |
| --- | --- | --- | --- |
| BR-031 | One approved request shall create no more than one access grant. | FR-021, FR-022; UJ-004 | Repeated processing does not create duplicate grants. |
| BR-032 | A grant's subject, resource, and permission shall match the approved request exactly. | FR-021, FR-022; UJ-004 | Grant creation with different access parameters is rejected. |
| BR-033 | A grant shall have a valid activation time and an expiration time later than its activation time. | FR-022; UJ-004 | Grant creation is rejected when the time interval is missing or invalid. |
| BR-034 | Only a valid active grant may contribute to authorisation for its exact subject, resource, and permission. | FR-023, FR-026; UJ-004-UJ-006 | Pending, expired, revoked, mismatched, or otherwise invalid grants provide no authorisation. |
| BR-035 | A grant shall stop authorising operations when its expiration time is reached, regardless of background-processing status. | FR-024, FR-026; UJ-005 | An operation attempted at or after expiration is rejected even if an expiration job is delayed. |
| BR-036 | Revocation shall require an authenticated and authorised actor, an active grant, and a non-empty revocation reason. | FR-025; UJ-006 | Revocation is rejected when any required condition is absent. |
| BR-037 | Revocation shall prevent the affected grant from authorising subsequent operations immediately after the transition succeeds. | FR-025, FR-026; UJ-006 | A protected operation using the revoked grant is rejected. |
| BR-038 | Expired and revoked grants shall remain historical evidence but shall never return to an active state. | FR-023-FR-030; UJ-005-UJ-007 | A reactivation transition from expired or revoked is rejected. |
| BR-039 | Continued access after expiration or revocation shall require a new request and a new successful approval lifecycle. | FR-010, FR-021, FR-024-FR-026; UJ-001-UJ-006 | No operation can extend or reactivate the terminal grant directly. |
| BR-040 | Grant expiration and revocation shall preserve the originating request, decisions, and lifecycle history. | FR-027-FR-030; UJ-005-UJ-007 | Historical relationships remain queryable after the grant becomes terminal. |

## Audit and evidence rules

| ID | Rule | Traceability | Verification |
| --- | --- | --- | --- |
| BR-041 | Request submission, review decisions, grant creation, expiration, revocation, administrative changes, and audit exports shall create audit records. | FR-027-FR-031; UJ-001-UJ-008 | Each listed successful event produces an audit record. |
| BR-042 | An audit record shall identify the actor, action, affected entity, timestamp, and outcome. | FR-028; UJ-001-UJ-008 | Records missing required evidence are rejected or treated as failed persistence. |
| BR-043 | Automated events shall identify a distinguishable System actor rather than an invented human actor. | FR-028; UJ-004, UJ-005 | Automated grant or expiration records identify the configured System actor. |
| BR-044 | Supported application operations shall not update or delete protected audit records. | FR-027, FR-029; UJ-007 | Attempts to modify or delete audit evidence are rejected. |
| BR-045 | Audit searches and exports shall be limited by the auditor's authorised scope. | FR-029, FR-031; UJ-007 | Records outside the assigned scope are excluded or access is rejected. |
| BR-046 | An audit export shall reflect the active authorised filters and shall itself create an audit record. | FR-031; UJ-007 | Exported data matches the permitted query and an export event is recorded. |
| BR-047 | Lifecycle history shall preserve the chronological relationship between a request, its decisions, its resulting grant, and its final state. | FR-030; UJ-004-UJ-007 | An authorised user can reconstruct the complete ordered lifecycle. |

## Consistency and failure rules

| ID | Rule | Traceability | Verification |
| --- | --- | --- | --- |
| BR-048 | A lifecycle transition and its required operational persistence shall complete atomically or leave the previous valid state unchanged. | FR-020; UJ-002-UJ-006; NFR-012 | An induced persistence failure does not leave a partial transition. |
| BR-049 | Concurrent incompatible operations shall produce at most one successful lifecycle transition. | FR-020; UJ-002, UJ-003, UJ-006 | Competing decisions or transitions cannot both become authoritative. |
| BR-050 | A stale update shall not overwrite a newer valid state or decision. | FR-020; UJ-002, UJ-003, UJ-006, UJ-008 | An update using an outdated concurrency value is rejected. |
| BR-051 | Retrying an operation designed to be idempotent shall not create duplicate requests, decisions, grants, transitions, or audit evidence. | FR-020, FR-021, FR-027; UJ-001-UJ-006; NFR-012 | Repeating the same operation produces one authoritative result. |
| BR-052 | Failure of a secondary notification operation shall not reverse a successfully completed security decision or lifecycle transition. | FR-034; UJ-004-UJ-006; NFR-012 | A notification failure leaves the authoritative domain state unchanged. |
| BR-053 | A failed primary operation shall not be reported as successful. | FR-035; UJ-001-UJ-008; NFR-012 | The response and persisted state reflect the actual failure. |
| BR-054 | Error responses, logs, notifications, and audit records shall not expose credentials, access tokens, or unnecessary sensitive implementation details. | FR-035; UJ-001-UJ-008; NFR-003, NFR-008 | Security review and automated checks find no prohibited sensitive values. |

## Rule combination and conflict resolution

Every applicable mandatory rule and constraint must be satisfied before an operation is permitted. The presence of one permitting rule does not override another applicable restriction.

In particular:

- an authorisation policy cannot override a separation-of-duties restriction;
- a product role cannot override an organisational-scope restriction;
- an organisational relationship cannot override a lifecycle invariant;
- workflow configuration cannot override a mandatory security rule;
- interface behaviour cannot override any backend-enforced restriction.

When applicable rules appear to conflict:

1. Deny the operation.
2. Apply the most restrictive valid interpretation.
3. Record the ambiguity as an unresolved domain or policy question.
4. Resolve it through a traceable and reviewed change before permitting the affected operation.

Rule ordering may determine evaluation sequence or error reporting, but it must never allow one mandatory constraint to bypass another.

## Configurable policy parameters

The following values may be configurable without changing the meaning of the business rules:

- maximum request duration;
- resource and permission sensitivity classification;
- approval stages required for each sensitivity level;
- manager and security-review assignment scope;
- justification and decision-comment validation limits;
- audit-query and export boundaries;
- notification-event configuration.

Configuration values must remain validated, version-controlled where appropriate, and auditable when changed.

## Assumptions and unresolved questions

The baseline currently assumes that:

- delegated requests are not supported in v1.0.0;
- rejected requests are terminal and require a new request;
- expired or revoked grants cannot be reactivated;
- duplicate pending requests and requests duplicating active grants are rejected;
- every request follows the approval workflow selected by applicable policy.

Later M1 work must confirm:

- the maximum request durations for each sensitivity level;
- the exact mapping from sensitivity to required approval stages;
- the authoritative source of organisational relationships;
- the permitted administrative revocation scopes;
- the concurrency-control mechanism used during implementation;
- any retention requirements affecting lifecycle and audit evidence.

Unresolved questions do not permit broader access. The most restrictive documented rule applies until an approved decision replaces the assumption.

## Change control

A business-rule change must:

- use a traceable Azure Boards work item;
- preserve existing rule identifiers when meaning remains unchanged;
- introduce a new identifier when the rule's meaning changes materially;
- update affected requirements, journeys, lifecycle definitions, diagrams, tests, and implementation;
- complete the established pull-request review workflow.
