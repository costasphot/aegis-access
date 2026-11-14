# Authorisation Model

| Field | Value |
| --- | --- |
| Status | Approved |
| Document version | 1.0 |
| Owner | G. Konstantinos Fotopoulos (Costas Phot) |
| Milestone | M1 - Product and security design |
| Work item | AB#14 |
| Last updated | 14 November 2026 |

## Purpose

This document defines the implementation-independent authorisation model for Aegis Access v1.0.0.

It establishes how authenticated Microsoft Entra identities are associated with internal users, product roles, organisational scope, relationships, lifecycle state, and policy before a protected operation is permitted.

The model provides the security baseline for backend policies, interface behaviour, audit evidence, implementation, and testing.

It must be read together with:

- the [product requirements](../product/requirements.md);
- the [user journeys](../product/user-journeys.md);
- the [roles and responsibilities](../domain/roles-and-responsibilities.md);
- the [domain glossary](../domain/glossary.md);
- the [business rules](../domain/business-rules.md);
- the [lifecycle states](../domain/lifecycle-states.md).

## Scope

This document defines:

- the separation between authentication and application authorisation;
- the trust boundary between Microsoft Entra ID and Aegis Access;
- the mapping from validated external identities to internal users and product roles;
- the information required for an authorisation decision;
- deny-by-default and least-privilege behaviour;
- backend and interface enforcement responsibilities;
- scope, relationship, lifecycle, and policy constraints;
- multiple-role and separation-of-duties behaviour;
- failure and audit expectations;
- assumptions and deferred implementation details.

The detailed operation-to-role mapping is maintained in the companion [access-control matrix](authorisation-matrix.md).

The following remain outside this document's scope:

- Microsoft Entra tenant creation and administration;
- application-registration configuration;
- concrete token and claim names;
- ASP.NET Core policy implementation;
- Angular route guards and interface implementation;
- database-schema and infrastructure design;
- automated authorisation-test implementation.

These decisions must conform to this model when introduced.

## Authorisation principles

- Authentication and authorisation are separate security decisions.
- Microsoft Entra ID establishes the external identity of a successfully authenticated principal.
- Aegis Access remains authoritative for internal-user status, product roles, organisational scope, relationships, lifecycle state, and application permissions.
- A valid identity token does not independently grant permission to perform a product operation.
- Every protected operation is denied unless an explicit policy permits it.
- Every required role, scope, relationship, lifecycle, and policy condition must be satisfied.
- Backend authorisation is enforced independently of interface visibility or client-supplied state.
- Angular route guards, hidden controls, and disabled controls improve usability but do not form a security boundary.
- Authorisation decisions use current trusted backend state.
- Holding multiple roles does not bypass a mandatory restriction.
- Expired, revoked, inactive, or otherwise invalid access never contributes to authorisation.
- Authorisation failures reveal no unnecessary policy or implementation details.
- Security-relevant authorisation activity produces attributable audit evidence.

## Responsibility boundaries

| Responsibility | Authoritative component |
| --- | --- |
| Workforce authentication | Microsoft Entra ID |
| Token issuance | Microsoft Entra ID |
| Token validation | Aegis Access backend |
| External-to-internal identity mapping | Aegis Access backend |
| Internal-user activation status | Aegis Access |
| External application-role assignment and revocation | Microsoft Entra ID through approved identity-administration processes |
| Product-role mapping configuration | Aegis Access through approved administrative processes |
| Current product-role resolution | Aegis Access backend using validated application-role information and current approved mappings |
| Organisational scope and relationships | Aegis Access |
| Resource and permission state | Aegis Access |
| Request and grant lifecycle state | Aegis Access |
| Application authorisation decision | Aegis Access backend |
| Interface visibility and navigation | Angular application using backend-provided context |
| Security audit evidence | Aegis Access backend |

The Angular application may use authorised context to present appropriate navigation and controls. The backend must nevertheless repeat the complete authorisation decision for every protected operation.

## Authorisation decision model

A protected operation is permitted only when every applicable stage succeeds:

1. The backend receives an access token.
2. The backend validates the token's signature, issuer, audience, lifetime, and required claims.
3. The validated external identity resolves to exactly one active internal user.
4. Approved mappings determine the user's current product roles.
5. The requested operation is identified using its stable operation identifier.
6. The backend loads the current trusted resource, relationship, scope, and lifecycle data required by the operation.
7. Every applicable role, assignment, ownership, organisational-scope, separation-of-duties, lifecycle, and policy condition is evaluated.
8. The operation is permitted only when an explicit policy allows it and no mandatory restriction prohibits it.
9. The authorisation decision, including a denial, and any resulting action produce audit evidence when required by the operation's audit policy.

Failure at any stage denies the operation. An earlier successful stage cannot override a later failure.

## Identity and trust model

### External identity

Microsoft Entra ID authenticates workforce users and issues identity tokens to approved client applications.

The backend trusts identity information only after validating:

- the token signature;
- the expected issuer;
- the intended audience;
- the token lifetime;
- the originating tenant;
- every claim required for identity and role mapping.

Stable tenant and object identifiers are used for identity mapping. Mutable values such as display names and email addresses must not serve as authoritative identity keys.

A valid token proves that Microsoft Entra ID authenticated the principal. It does not prove that the principal has an active Aegis Access account or permission to perform a requested operation.

### Internal user mapping

A validated external identity must resolve to exactly one active internal user before it can perform a protected product operation.

The operation is denied when:

- no internal mapping exists;
- more than one internal user matches the identity;
- the internal user is inactive;
- the mapping belongs to an unexpected tenant;
- required identity information is missing or invalid.

Provisioning an internal user does not independently grant product permissions. The user must also receive explicitly approved product-role mappings.

The method used to create internal users may be selected during implementation design. Whether provisioning is manual, synchronised, or just in time, no protected access is permitted before a valid internal mapping exists.

### Product-role assignment and mapping

External application-role assignment and internal product-role mapping are separate responsibilities.

Microsoft Entra ID records approved application-role assignments. Aegis Access configuration defines how recognised external application roles map to product roles.

During a protected operation, the backend resolves the current product roles from:

- validated external application-role information;
- approved role-mapping configuration;
- the current internal-user record;
- any applicable role-assignment restrictions.

An external application role:

- grants no product permission when it has no approved mapping;
- cannot create or modify its own mapping;
- cannot override an inactive internal user;
- cannot override scope, relationship, lifecycle, or separation-of-duties restrictions;
- cannot independently authorise a protected operation.

An active user may hold multiple product roles. Explicitly permitted capabilities may be combined, but every mandatory restriction remains applicable.

No role inherits another role's authority unless an approved authorisation rule explicitly defines that relationship.

### Principal categories

| Principal category | Description | Protected-operation eligibility |
| --- | --- | --- |
| Unauthenticated principal | No valid identity token is available | Denied |
| Invalid principal | The supplied token fails required validation | Denied |
| Unmapped principal | The token is valid but no unique internal-user mapping exists | Denied |
| Inactive user | A mapping exists but the internal user is inactive | Denied |
| Active mapped user | A valid identity maps to exactly one active internal user | Eligible for further authorisation evaluation |
| System actor | A trusted non-human backend process performs a predefined operation | Eligible only for explicitly assigned system operations |

Eligibility for evaluation does not imply that an operation is permitted.

## Identity-lifecycle behaviour

| Event or condition | Required security behaviour |
| --- | --- |
| Internal-user provisioning | Creates an identity mapping but grants no operation unless approved product roles and policies permit it |
| External application-role assignment | Contributes to product-role resolution only through an approved mapping |
| External application-role removal | Prevents the removed role from authorising later operations after the applicable freshness boundary |
| Product-role mapping change | Affects later decisions only through the current approved configuration |
| Internal-user deactivation | Prevents every later protected user operation regardless of token validity |
| Internal-user reactivation | Requires the user's current mappings and policies to be evaluated again |
| Missing required claim | Prevents any identity or role mapping that depends on that claim |
| Unknown external role | Grants no product permission and may create diagnostic or audit evidence |
| Stale identity information | Cannot independently override current trusted backend state |
| Ambiguous identity mapping | Denies the operation until the mapping conflict is resolved |
| Tenant mismatch | Denies the operation before domain processing |
| System-actor deactivation | Prevents the actor from performing further automated operations |

Cached identity or authorisation information must not remain usable beyond its approved freshness boundary. The exact caching and invalidation mechanism will be selected during architecture and implementation design.

## Authorisation context

A complete authorisation decision may use the following trusted context:

| Context element | Purpose |
| --- | --- |
| Principal identity | Identifies the authenticated actor |
| Internal-user status | Confirms that the mapped user remains active |
| Product roles | Identifies explicitly mapped responsibility sets |
| Operation identifier | Identifies the exact protected action being attempted |
| Target subject | Identifies the user affected by the operation |
| Resource and permission | Identifies the governed access being requested or administered |
| Assignment | Confirms that a reviewer or administrator is responsible for the target |
| Organisational scope | Limits authority to approved organisational boundaries |
| Ownership relationship | Identifies whether the actor owns or manages the affected resource or subject |
| Lifecycle state | Confirms that the operation is valid in the entity's current state |
| Separation-of-duties context | Identifies self-action, conflicting roles, or prohibited relationships |
| Resource sensitivity | Determines whether additional restrictions or approvals apply |
| Current time | Enforces activation, expiration, and other time-dependent conditions |
| Applicable policy | Supplies the approved rules governing the operation |

A product role is necessary for many operations but is not always sufficient. Contextual conditions must be evaluated independently.

## Decision outcomes

An authorisation evaluation produces one of two authoritative outcomes:

- `Permit` when an explicit policy allows the operation and every mandatory condition succeeds;
- `Deny` in every other case.

A permitting rule cannot override:

- an inactive or unmapped internal user;
- a missing required role;
- an invalid assignment or organisational scope;
- a separation-of-duties or conflict-of-interest prohibition;
- an invalid lifecycle state;
- an inactive resource or permission;
- an expired or revoked grant;
- another mandatory security restriction.

When multiple applicable rules disagree, the most restrictive mandatory result applies. The system must never infer permission from the absence of a prohibition.

## Scope and relationship controls

Possession of a product role does not grant unrestricted authority over every entity of the corresponding type.

The following scope principles apply:

- an Employee acts only on their own requests, grants, and permitted catalogue views;
- a Manager reviews only assigned requests within the manager's authorised organisational scope;
- a Security reviewer acts only on assigned reviews and grants within the reviewer's authorised scope;
- an Auditor accesses only audit and reporting data within the auditor's authorised read-only scope;
- an Administrator performs only explicitly permitted administrative operations within the administrator's assigned scope;
- a System actor performs only predefined automated operations using its assigned service permissions.

Ownership, organisational relationships, assignments, and scope are loaded from trusted backend state. Client-supplied identifiers do not establish authority.

Changing a target identifier, request body, route value, or query parameter must never expand the actor's authorised scope.

## Multiple roles and separation of duties

A user may hold more than one product role, but effective authorisation is not calculated as an unrestricted union of role capabilities.

The following rules apply:

- every operation still requires an explicit permitting policy;
- restrictions attached to any applicable relationship, scope, lifecycle state, or security rule remain effective;
- one role cannot be used to bypass a restriction imposed on another role;
- a Manager or Security reviewer cannot review their own request;
- an actor cannot perform both sides of a decision when a separation-of-duties rule prohibits it;
- an Administrator role does not automatically override workflow, audit, or lifecycle restrictions;
- an Auditor role remains read-only even when the same user holds another operational role;
- a System actor cannot assume a human role or invent a human decision;
- the original authenticated actor remains attributable throughout the operation.

A prohibited conflict results in `Deny`, even when another product role would otherwise permit the operation.

## Lifecycle-aware authorisation

Authorisation must evaluate the current persisted lifecycle state of the affected entity.

For access requests:

- submission is permitted only when every submission guard succeeds;
- a manager decision is permitted only in `PendingManagerReview`;
- a security decision is permitted only in `PendingSecurityReview`;
- a rejected request cannot return to review or create a grant;
- an approved request creates no more than one grant;
- a request in `GrantCreated` permits no further request transition.

For access grants:

- `PendingActivation` grants provide no authorisation;
- only an `Active` grant within its valid time window may contribute to authorisation;
- a grant provides no authorisation at or after its expiration time;
- an `Expired` or `Revoked` grant provides no authorisation;
- a terminal grant cannot return to `Active`;
- revocation requires an authorised actor, valid scope, an active grant, and a recorded reason.

A stale client representation cannot authorise an operation against a newer backend state. The backend reloads or otherwise verifies the authoritative state before completing a protected lifecycle action.

## Backend enforcement

Every protected backend entry point must enforce the applicable authorisation policy.

This requirement applies to:

- HTTP API endpoints;
- background workers;
- scheduled processes;
- administrative processes;
- audit searches and exports;
- notification-triggering operations;
- internal commands that change protected domain state.

The backend must:

- derive the authenticated principal from validated identity information;
- resolve the current internal user and product roles;
- load required scope, relationship, and lifecycle context from trusted sources;
- identify the exact protected operation;
- evaluate every applicable mandatory condition;
- deny the operation when required context is missing or cannot be trusted;
- prevent unauthorised state changes and side effects;
- record required audit evidence.

An authorisation result applies only to the evaluated principal, operation, target, and context. It must not be reused for a different target or after a relevant security condition changes.

## Interface responsibilities

The Angular application may:

- hide navigation that is irrelevant to the current user;
- protect routes to improve navigation behaviour;
- disable unavailable actions;
- present role-appropriate dashboards;
- explain forbidden or invalid operations using safe messages;
- request authorised context from the backend.

The Angular application must not:

- decide that a backend operation is permitted;
- trust locally stored roles as authoritative;
- treat a hidden control as enforcement;
- modify client-side state to bypass backend validation;
- expose sensitive policy details, access tokens, or internal exceptions;
- imply that an operation succeeded before the backend confirms it.

Interface controls supplement backend enforcement but never replace it.

## Failure behaviour

| Failure condition | Required behaviour |
| --- | --- |
| Missing token | Reject the protected request before domain processing |
| Invalid or expired token | Reject the request without trusting any supplied identity information |
| Unexpected issuer, audience, or tenant | Reject the request before internal-user mapping |
| Missing required claim | Deny any operation requiring that claim |
| Missing or ambiguous internal mapping | Deny the operation |
| Inactive internal user | Deny the operation regardless of token validity |
| Missing product role | Deny the protected operation |
| Unknown operation identifier | Deny the operation and treat the condition as a configuration or implementation defect |
| Missing trusted context | Deny the operation rather than infer permission |
| Invalid scope or relationship | Deny the operation |
| Separation-of-duties conflict | Deny and audit the attempt when security-relevant |
| Invalid lifecycle state | Reject the operation without changing persisted state |
| Policy-evaluation failure | Fail securely and report no successful outcome |
| Required audit persistence failure | Do not report the protected operation as successfully completed |
| Stale or concurrent update | Reject the outdated operation and preserve the current valid state |

Responses must distinguish authentication failure from forbidden application behaviour where appropriate without revealing sensitive policy logic, internal identifiers, token contents, or exception details.

The exact protocol status codes and public error format will be defined by the API conventions.

## Audit expectations

Authorisation-related audit evidence must be produced for security-relevant events identified by policy.

Relevant events include:

- protected administrative operations;
- role-mapping configuration changes;
- internal-user activation and deactivation;
- prohibited self-review and conflict-of-interest attempts;
- approval and rejection decisions;
- grant creation, expiration, and revocation;
- audit searches and exports where required;
- repeated or suspicious forbidden-operation attempts;
- System-actor lifecycle operations;
- authorisation-policy or security-configuration changes.

Where applicable, audit evidence records:

- the attributable actor;
- the stable operation identifier;
- the affected entity or target;
- the decision outcome;
- a safe reason category;
- the timestamp;
- the relevant correlation identifier;
- the resulting lifecycle or administrative outcome.

Audit records must not contain credentials, complete access tokens, unnecessary claim values, internal exception details, or other prohibited sensitive information.

Routine denials need not all produce identical audit records. The audit policy must balance investigation requirements, privacy, operational volume, and resistance to log-flooding attacks.

## Traceability

| Model area | Principal references |
| --- | --- |
| Authentication and token validation | FR-001, FR-002; BR-001, BR-002 |
| Internal-user and product-role mapping | FR-003; BR-003 |
| Backend enforcement and deny by default | FR-004, FR-005; BR-004-BR-006 |
| Scope and multiple-role restrictions | FR-003-FR-005, FR-018; BR-006, BR-007, BR-022-BR-024 |
| Request lifecycle authorisation | FR-010-FR-021; BR-013-BR-030; RT-001-RT-009 |
| Grant lifecycle authorisation | FR-021-FR-026; BR-031-BR-040; GT-001-GT-006 |
| Audit and evidence | FR-027-FR-031; BR-041-BR-047 |
| Failure and concurrency behaviour | FR-020, FR-035; BR-048-BR-054; NFR-003, NFR-008, NFR-012 |
| Role-specific journeys | UJ-001-UJ-008 |
| Request invariants | INV-REQ-001-INV-REQ-010 |
| Grant invariants | INV-GRT-001-INV-GRT-010 |

The companion access-control matrix provides operation-level traceability for each product role and System actor.

## Assumptions and unresolved questions

The authorisation baseline currently assumes that:

- Aegis Access v1.0.0 serves one configured organisational Microsoft Entra tenant;
- workforce identities originate from the configured tenant;
- every protected human operation requires an active internal-user mapping;
- the requester and subject are the same user in v1.0.0;
- product roles represent responsibilities rather than resource-level access grants;
- resource permissions are provided only through valid access grants;
- the System actor represents trusted non-human backend processing;
- no undocumented administrative or emergency role bypass exists;
- current trusted backend state takes precedence over client-supplied state.

Architecture and implementation design must resolve:

- the exact Microsoft Entra application roles and required token claims;
- the internal-user provisioning and deprovisioning mechanism;
- the approved source of organisational relationships and reviewer assignments;
- the representation of organisational and administrative scope;
- the role-mapping configuration and change-approval process;
- token, mapping, and authorisation-context freshness boundaries;
- cache invalidation and role-removal propagation;
- whether resource or permission deactivation revokes affected active grants or only prevents new requests;
- the detailed audit policy for denied operations;
- whether any emergency-access process belongs in a later release.

Until an unresolved question is decided through an approved change, the most restrictive documented interpretation applies.

## Change control

A change to this authorisation model must:

- use a traceable Azure Boards work item;
- identify affected requirements, roles, business rules, lifecycle rules, operations, and tests;
- update the access-control matrix where operation permissions or conditions change;
- document any effect on Microsoft Entra configuration or identity mapping;
- preserve deny-by-default and least-privilege behaviour;
- receive security-focused review;
- complete the established pull-request workflow.
