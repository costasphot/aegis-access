# Lifecycle States

| Field | Value |
| --- | --- |
| Status | Approved |
| Document version | 1.0 |
| Owner | G. Konstantinos Fotopoulos (Costas Phot) |
| Milestone | M1 - Product and security design |
| Work item | AB#13 |
| Last updated | 28 October 2026 |

## Purpose

This document defines the valid lifecycle states, transitions, guards, and invariants for access requests and access grants in Aegis Access v1.0.0.

It establishes the lifecycle behaviour that the backend, interface, persistence model, automated processes, audit trail, and tests must enforce consistently.

It must be read together with:

- the [product requirements](../product/requirements.md);
- the [user journeys](../product/user-journeys.md);
- the [roles and responsibilities](roles-and-responsibilities.md);
- the [domain glossary](glossary.md);
- the [business rules](business-rules.md).

## Lifecycle principles

- The backend is authoritative for lifecycle state.
- A transition must be explicitly permitted from the current state.
- Every transition must satisfy its actor, policy, scope, and data guards.
- Invalid and stale transitions must leave the persisted state unchanged.
- Concurrent incompatible transitions must not both succeed.
- Successful security-relevant transitions must create attributable audit evidence.
- Interface state cannot override persisted backend state.
- Terminal entities remain available as historical evidence.
- Retrying an idempotent transition must not create duplicate effects.
- Expired or revoked grants must never provide authorisation.

## State categories

| Category | Meaning |
| --- | --- |
| Initial | The first persisted state assigned when an entity is created. |
| Intermediate | A non-terminal state from which further valid transitions are expected. |
| Terminal | A final state from which no ordinary lifecycle transition is permitted. |

## Access-request lifecycle

### Request states

| State | Category | Meaning |
| --- | --- | --- |
| `Submitted` | Initial | The request has been validated and persisted, and is awaiting workflow routing. |
| `PendingManagerReview` | Intermediate | The request is awaiting an authorised managerial decision. |
| `PendingSecurityReview` | Intermediate | The request is awaiting an authorised security decision. |
| `Approved` | Intermediate | Every required approval has succeeded and the request is eligible for grant creation. |
| `Rejected` | Terminal | A required reviewer rejected the request. |
| `GrantCreated` | Terminal | The approved request produced its single permitted access grant. |

### Request transition summary

| Transition ID | Current state | Trigger | Next state |
| --- | --- | --- | --- |
| RT-001 | None | Valid request submission | `Submitted` |
| RT-002 | `Submitted` | Manager review is required | `PendingManagerReview` |
| RT-003 | `Submitted` | No review stage is required by policy | `Approved` |
| RT-004 | `PendingManagerReview` | Manager approves and security review is required | `PendingSecurityReview` |
| RT-005 | `PendingManagerReview` | Manager approves and no security review is required | `Approved` |
| RT-006 | `PendingManagerReview` | Manager rejects | `Rejected` |
| RT-007 | `PendingSecurityReview` | Security reviewer approves | `Approved` |
| RT-008 | `PendingSecurityReview` | Security reviewer rejects | `Rejected` |
| RT-009 | `Approved` | Access grant is created successfully | `GrantCreated` |

### RT-001: Submit a request

Actor:

- Employee.

Required guards:

- the actor is authenticated and mapped to an active internal user;
- the requester and subject are the same user;
- the resource and permission are active and requestable;
- the business justification is valid;
- the requested duration is valid;
- no prohibited equivalent pending request or active grant exists;
- the actor is authorised to submit the request.

Required effects:

- the request receives a unique identifier;
- the request data is persisted;
- the initial state is `Submitted`;
- the submission time and actor are recorded;
- an audit record is created.

Failure behaviour:

- no request is created;
- no successful-submission event is reported;
- validation information is returned without exposing sensitive policy details.

Traceability:

- BR-013-BR-020;
- FR-010-FR-014;
- UJ-001.

### RT-002-RT-003: Route a submitted request

Actor:

- System.

Required guards:

- the request is currently `Submitted`;
- the workflow policy has been evaluated successfully;
- the required approval stages are known;
- a workflow requiring security review also requires prior manager review;
- the persisted request remains valid.

Required effects:

- the request enters the first required review state;
- when policy requires no human review stage, the request enters `Approved`;
- the selected route is deterministic for the same policy context;
- the routing event is recorded.

Failure behaviour:

- the request remains `Submitted`;
- no approval or grant is invented;
- the failure is recorded for diagnosis and safe retry.

Traceability:

- BR-021;
- FR-015, FR-016, FR-020;
- UJ-001-UJ-003.

### RT-004-RT-006: Record a manager decision

Actor:

- Manager.

Required guards:

- the request is currently `PendingManagerReview`;
- the actor has the Manager role;
- the actor is assigned to the review;
- the actor is within the required organisational scope;
- the actor is neither the requester nor subject;
- no separation-of-duties or conflict-of-interest rule prohibits the decision;
- a valid decision comment is provided;
- the submitted request data has not been altered.

Required effects for approval:

- one attributable approval decision is stored;
- the request enters `PendingSecurityReview` when security review is required;
- otherwise, the request enters `Approved`;
- the decision and transition are audited.

Required effects for rejection:

- one attributable rejection decision is stored;
- the request enters `Rejected`;
- no later review or grant creation is permitted;
- the decision and transition are audited.

Failure behaviour:

- no decision or state transition is recorded as successful;
- the request remains in its previous valid state;
- prohibited attempts are audited when security-relevant.

Traceability:

- BR-022-BR-030;
- FR-015, FR-017-FR-020;
- UJ-002.

### RT-007-RT-008: Record a security decision

Actor:

- Security reviewer.

Required guards:

- the request is currently `PendingSecurityReview`;
- every required preceding approval is complete;
- the actor has the Security reviewer role;
- the actor is assigned and within the permitted review scope;
- the actor is neither the requester nor subject;
- no separation-of-duties, conflict-of-interest, or mandatory policy rule prohibits the decision;
- a valid decision comment is provided;
- the submitted request data has not been altered.

Required effects for approval:

- one attributable approval decision is stored;
- the request enters `Approved`;
- the decision and transition are audited.

Required effects for rejection:

- one attributable rejection decision is stored;
- the request enters `Rejected`;
- no grant may be created;
- the decision and transition are audited.

Failure behaviour:

- no decision or state transition is recorded as successful;
- the request remains in `PendingSecurityReview`;
- prohibited attempts are audited when security-relevant.

Traceability:

- BR-022-BR-030;
- FR-016-FR-020;
- UJ-003.

### RT-009: Complete grant creation

Actor:

- System.

Required guards:

- the request is currently `Approved`;
- all required approval decisions are present and valid;
- no rejection exists;
- no new mandatory policy conflict prevents grant creation;
- the request has not already produced a grant;
- the grant data exactly matches the approved request;
- the grant activation and expiration times are valid.

Required effects:

- exactly one access grant is persisted;
- the grant receives its valid initial state;
- the request enters `GrantCreated`;
- the relationship between the request and grant is preserved;
- grant creation and the request transition are audited.

Failure behaviour:

- the request remains `Approved`;
- no partial or duplicate grant is reported as successful;
- the operation may be retried safely after the failure is resolved.

Traceability:

- BR-030-BR-033, BR-048, BR-051;
- FR-021, FR-022;
- UJ-004.

## Request terminal states

### Rejected

`Rejected` is terminal because a required approval failed.

A rejected request:

- cannot return to a pending-review state;
- cannot become approved;
- cannot create a grant;
- remains visible as historical evidence;
- requires a new request if access is still needed.

### GrantCreated

`GrantCreated` is terminal because the request lifecycle has successfully produced its only permitted grant.

A request in `GrantCreated`:

- cannot produce another grant;
- cannot return to review;
- remains linked to the resulting grant;
- remains available for lifecycle and audit inspection.

## Request invariants

| ID | Invariant |
| --- | --- |
| INV-REQ-001 | A request has exactly one requester and one subject. |
| INV-REQ-002 | The requester and subject are the same user in v1.0.0. |
| INV-REQ-003 | A request refers to exactly one resource and one permission belonging to that resource. |
| INV-REQ-004 | Submitted request data cannot be silently changed by a reviewer. |
| INV-REQ-005 | A request cannot occupy more than one lifecycle state simultaneously. |
| INV-REQ-006 | A request cannot skip a required approval stage. |
| INV-REQ-007 | A request cannot have more than one authoritative decision for the same approval stage. |
| INV-REQ-008 | A rejected request cannot become approved or create a grant. |
| INV-REQ-009 | A request cannot create more than one grant. |
| INV-REQ-010 | Every successful request transition has attributable audit evidence. |

## Access-grant lifecycle

### Grant states

| State | Category | Meaning |
| --- | --- | --- |
| `PendingActivation` | Initial | The grant exists but its activation time has not yet been reached. |
| `Active` | Initial or intermediate | The grant is within its valid time window and has not expired or been revoked. |
| `Expired` | Terminal | The grant's expiration time has been reached. |
| `Revoked` | Terminal | An authorised actor terminated the grant before expiration. |

### Grant transition summary

| Transition ID | Current state | Trigger | Next state |
| --- | --- | --- | --- |
| GT-001 | None | Approved request produces a future-dated grant | `PendingActivation` |
| GT-002 | None | Approved request produces an immediately effective grant | `Active` |
| GT-003 | `PendingActivation` | Activation time is reached before expiration | `Active` |
| GT-004 | `PendingActivation` | Expiration time is reached without successful activation processing | `Expired` |
| GT-005 | `Active` | Expiration time is reached | `Expired` |
| GT-006 | `Active` | Authorised revocation succeeds | `Revoked` |

### GT-001-GT-002: Create a grant

Actor:

- System.

Required guards:

- the originating request is `Approved`;
- all required approvals are valid;
- no rejection or mandatory policy conflict exists;
- the request has not already produced a grant;
- the subject, resource, and permission match the approved request;
- the activation time precedes the expiration time.

Initial-state selection:

- use `PendingActivation` when the activation time is in the future;
- use `Active` when the activation time has been reached and the expiration time remains in the future;
- do not create a grant whose expiration time has already been reached.

Required effects:

- exactly one grant is persisted;
- activation and expiration times are recorded;
- the grant is linked to the originating request;
- the selected initial state is recorded;
- grant creation is audited.

Traceability:

- BR-030-BR-034;
- FR-021, FR-022;
- UJ-004.

### GT-003: Activate a scheduled grant

Actor:

- System.

Required guards:

- the grant is currently `PendingActivation`;
- the activation time has been reached;
- the expiration time has not been reached;
- the grant has not been invalidated by applicable policy.

Required effects:

- the grant enters `Active`;
- activation is recorded and audited;
- the grant may contribute to authorisation only after the transition succeeds.

Failure behaviour:

- the grant does not provide authorisation merely because activation processing failed;
- processing may be retried safely while the grant remains eligible.

Traceability:

- BR-033, BR-034, BR-048, BR-051;
- FR-022, FR-026;
- UJ-004.

### GT-004-GT-005: Expire a grant

Actor:

- System.

Required guards:

- the grant is `PendingActivation` or `Active`;
- the expiration time has been reached.

Required effects:

- the grant enters `Expired`;
- the grant can no longer contribute to authorisation;
- expiration is recorded and audited;
- historical relationships remain intact;
- the affected user may receive a notification.

Enforcement rule:

The backend must reject use of the grant at or after its expiration time even when the persisted state or a background process has not yet been updated to `Expired`.

Failure behaviour:

- delayed processing never extends effective access;
- retries do not create duplicate transitions or contradictory audit evidence;
- notification failure does not restore access.

Traceability:

- BR-034, BR-035, BR-038-BR-040, BR-051, BR-052;
- FR-024, FR-026-FR-030, FR-034;
- UJ-005.

### GT-006: Revoke an active grant

Actor:

- Security reviewer or Administrator.

Required guards:

- the grant is currently `Active`;
- the actor is authenticated;
- the actor holds a permitted role;
- the actor is authorised within the required scope;
- a valid revocation reason is provided;
- no stale or concurrent transition has already changed the grant.

Required effects:

- the grant enters `Revoked`;
- the grant immediately stops contributing to authorisation;
- the actor, reason, timestamp, and transition are recorded;
- the revocation is audited;
- the affected user may receive a notification.

Failure behaviour:

- the previous valid state remains unchanged;
- unauthorised or duplicate attempts are rejected;
- notification failure does not reverse revocation.

Traceability:

- BR-036-BR-040, BR-048-BR-052;
- FR-025-FR-030, FR-034;
- UJ-006.

## Grant terminal states

### Expired

`Expired` is terminal because the approved time window has ended.

An expired grant:

- provides no authorisation;
- cannot return to `Active`;
- cannot be extended directly;
- remains available as historical evidence;
- requires a new request for future access.

### Revoked

`Revoked` is terminal because an authorised actor ended the grant early.

A revoked grant:

- provides no authorisation;
- cannot return to `Active`;
- cannot be extended directly;
- preserves its revocation actor, time, and reason;
- requires a new request for future access.

## Grant invariants

| ID | Invariant |
| --- | --- |
| INV-GRT-001 | A grant originates from exactly one approved request. |
| INV-GRT-002 | A grant has exactly one subject, resource, and permission matching its approved request. |
| INV-GRT-003 | A grant's expiration time is later than its activation time. |
| INV-GRT-004 | A grant cannot occupy more than one lifecycle state simultaneously. |
| INV-GRT-005 | Only an `Active` grant within its valid time window may contribute to authorisation. |
| INV-GRT-006 | A grant provides no authorisation at or after its expiration time. |
| INV-GRT-007 | An expired or revoked grant cannot become active again. |
| INV-GRT-008 | Revocation requires an authorised actor and recorded reason. |
| INV-GRT-009 | Expiration and revocation preserve the originating request and lifecycle history. |
| INV-GRT-010 | Every successful grant transition has attributable audit evidence. |

## Invalid transitions

The following transitions are explicitly invalid:

- `Submitted` directly to `GrantCreated`;
- `PendingManagerReview` directly to `GrantCreated`;
- `PendingSecurityReview` directly to `GrantCreated`;
- `Rejected` to any other request state;
- `GrantCreated` to any other request state;
- `Approved` back to a pending-review state;
- `PendingActivation` directly to `Revoked` in v1.0.0;
- `Expired` to `Active`;
- `Revoked` to `Active`;
- `Expired` to `Revoked`;
- `Revoked` to `Expired`;
- any transition whose required actor, guard, policy, or current state is invalid.

An invalid transition must:

- be rejected by the backend;
- leave persisted domain state unchanged;
- avoid creating successful-transition side effects;
- return a controlled domain error;
- create audit evidence when the attempt is security-relevant;
- avoid exposing sensitive internal policy details.

## Concurrency behaviour

Lifecycle-changing operations must use concurrency controls that ensure:

- two reviewers cannot both create authoritative decisions for the same stage;
- approval and rejection cannot both succeed for one stage;
- expiration and revocation cannot both become the authoritative terminal transition;
- stale updates cannot overwrite newer states;
- duplicate grant-creation attempts produce at most one grant;
- retries remain safe and idempotent where required.

The implementation mechanism will be selected during architecture and implementation design. Regardless of mechanism, the lifecycle guarantees in this document remain mandatory.

## Request-to-grant relationship

The request and grant lifecycles are separate but connected:

1. A valid request begins in `Submitted`.
2. The request completes every approval stage required by policy.
3. Successful completion places the request in `Approved`.
4. Successful grant creation places the request in `GrantCreated`.
5. The grant begins in `PendingActivation` or `Active`.
6. The grant eventually reaches `Expired` or `Revoked`.
7. The request, decisions, grant, and terminal grant state remain linked for audit and lifecycle inspection.

A grant-state transition does not reopen or modify the completed request.

## Assumptions and unresolved questions

The lifecycle baseline currently assumes that:

- draft requests are not persisted before submission;
- delegated requests are not supported in v1.0.0;
- rejected requests cannot be reopened;
- approved requests remain `Approved` when grant creation fails and may be retried safely;
- requests cannot be edited after submission;
- grant renewal requires a new request;
- only active grants may be manually revoked in v1.0.0;
- terminal states remain permanently non-authorising.

Later design or implementation work must confirm:

- whether grant activation is always immediate for v1.0.0;
- the exact concurrency-control mechanism;
- the retry schedule for failed routing, grant creation, and expiration processing;
- whether a request-expiration or requester-cancellation capability belongs in a later release;
- how policy changes affect pending requests and active grants;
- whether deactivating a resource or permission automatically revokes affected active grants or only prevents new requests.

Until an open question is resolved through an approved change, the most restrictive documented interpretation applies.

## Change control

A lifecycle change must:

- use a traceable Azure Boards work item;
- update affected business rules, requirements, journeys, diagrams, tests, and implementation;
- preserve historical interpretation of existing lifecycle evidence;
- identify any migration or backward-compatibility impact;
- complete the established pull-request review workflow.
