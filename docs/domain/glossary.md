# Domain Glossary

| Field | Value |
| --- | --- |
| Status | Approved |
| Document version | 1.0 |
| Owner | G. Konstantinos Fotopoulos (Costas Phot) |
| Milestone | M1 - Product and security design |
| Work item | AB#13 |
| Last updated | 28 October 2026 |

## Purpose

This glossary defines the canonical domain language for Aegis Access v1.0.0. It ensures that requirements, diagrams, interface labels, APIs, source code, tests, audit records, and documentation use the same terms consistently.

It must be read together with:

- the [product requirements](../product/requirements.md);
- the [user journeys](../product/user-journeys.md);
- the [roles and responsibilities](roles-and-responsibilities.md).

## Terminology principles

- Each term has one principal meaning within the product.
- Different concepts must not share an ambiguous name.
- Interface labels may use friendlier language, but they must preserve the underlying domain terminology.
- API and source-code names should use the canonical English term.
- Security-sensitive terms must not be simplified in ways that obscure who performed an action or what was authorised.
- New domain terms must be added here before or alongside implementation.

## Identity and actors

| Term | Definition | Important distinction |
| --- | --- | --- |
| Identity | The externally established representation of a person or service, authenticated through Microsoft Entra ID. | An identity originates outside Aegis Access; it is not the same as an internal user record. |
| Principal | An authenticated identity presented to the application through validated security claims. | A principal is the current security context used when evaluating authorisation. |
| User | The internal Aegis Access representation associated with an authenticated workforce identity. | A user stores product-specific mappings and state; it does not store the user's password. |
| Subject | The user for whom access is requested, granted, expired, or revoked. | The subject may differ from the actor performing a review or administrative operation. |
| Actor | The authenticated user or trusted system process that performs an action. | Audit records identify the actor independently from the affected subject. |
| Requester | The actor who submits an access request for a subject. | In v1.0.0, the requester and subject are the same Employee unless a later requirement introduces delegation. |
| Product role | A named set of product responsibilities and potential capabilities. | A role does not grant unrestricted authority; policy, scope, relationships, and business rules still apply. |
| Application role | A role claim configured in Microsoft Entra ID and used as an input to internal role mapping. | An Entra application role must be validated and mapped before it becomes an effective product role. |
| Role mapping | A configured relationship between a validated external role claim and an internal product role. | A mapping does not bypass backend policy evaluation. |
| Employee | A product role representing a workforce user who requests and monitors personal governed access. | Employee is a product role, not an employment-status record. |
| Manager | A product role authorised to evaluate business need within an assigned organisational scope. | Holding the Manager role does not permit reviewing every employee's request. |
| Security reviewer | A product role authorised to evaluate security-sensitive requests and perform permitted revocations. | Security-review authority remains limited by assignment, policy, and separation of duties. |
| Auditor | A read-only product role authorised to inspect access-governance evidence. | An Auditor does not participate in operational approval or grant management. |
| Administrator | A product role authorised to maintain explicitly permitted product configuration and operational data. | An Administrator does not automatically receive governed resource access or permission to alter audit history. |
| System actor | A trusted backend process that performs predefined automated operations. | It is not an interactive human role and must be distinguishable in audit records. |

## Scope and organisational structure

| Term | Definition | Important distinction |
| --- | --- | --- |
| Organisational relationship | A relationship such as manager-to-employee or owner-to-resource that helps determine operational scope. | It is not itself a product role. |
| Organisational scope | The set of users, requests, resources, or operations over which an actor may exercise a role. | A role without the required scope does not authorise an operation. |
| Authority boundary | A documented limit on the actions or data available to an actor. | Crossing a boundary requires an explicit authorised relationship or policy. |
| Resource owner | The user or organisational party recorded as responsible for a resource. | Resource ownership is metadata and a relationship, not a separate v1.0.0 product role. |
| Review assignment | The relationship that makes a specific pending review available to an authorised reviewer. | Possessing a reviewer role does not automatically assign every review. |

## Resources and permissions

| Term | Definition | Important distinction |
| --- | --- | --- |
| Resource | An organisational system, application, dataset, service, or other governed object represented in the catalogue. | A resource definition describes what may be requested; it is not the external system itself. |
| Resource catalogue | The collection of resource definitions and requestable permissions visible to authorised users. | Catalogue visibility does not guarantee that every visible permission may be granted. |
| Resource definition | The Aegis Access record containing a resource's identity, description, owner, sensitivity, status, and permissions. | It contains governance metadata rather than credentials for the external resource. |
| Resource status | The operational availability of a resource definition, such as active or inactive. | Resource status is separate from request and grant state. |
| Sensitivity classification | A label representing the security significance of a resource or permission. | Classification may determine required approval stages and policy constraints. |
| Permission | A defined capability associated with a resource that may be requested and governed. | A permission describes capability; it does not prove that a user currently holds access. |
| Requestable permission | An active permission that policy permits eligible users to request. | Requestable does not mean automatically approvable. |
| Governed access | A subject's ability to exercise a specific permission under the lifecycle and policy controls of Aegis Access. | It must be represented by a valid active grant. |

## Requests and reviews

| Term | Definition | Important distinction |
| --- | --- | --- |
| Access request | A subject's recorded proposal to receive a specific permission for a resource for a limited duration. | A request is not access and does not authorise protected operations. |
| Business justification | The requester's explanation of why the permission is required. | It is evaluated during review and preserved as lifecycle evidence. |
| Requested duration | The period for which the requester asks to hold the permission. | Requests exceeding the applicable maximum duration must be rejected. |
| Request state | The current lifecycle position of an access request. | It determines which transitions and actions are currently valid. |
| Approval stage | A required step at which an authorised reviewer records a decision. | Completing one stage may not be sufficient for grant creation. |
| Review | The act of evaluating an eligible access request at a required approval stage. | A review is the process; a decision is its recorded outcome. |
| Manager review | A review focused on business need and proportionality within an organisational scope. | It does not replace a required security review. |
| Security review | A review focused on sensitivity, policy, risk, and separation-of-duties constraints. | It occurs only when required by the applicable workflow. |
| Reviewer | An actor authorised and assigned to decide a review. | A reviewer cannot act when self-review or another prohibited conflict exists. |
| Decision | The recorded, attributable outcome of a review. | A decision must identify its actor, outcome, time, and required comment. |
| Approval | A positive decision recorded at an approval stage. | An approval does not itself constitute an active grant. |
| Rejection | A negative decision that prevents the request from proceeding. | It is a terminal request outcome unless a future requirement explicitly defines resubmission. |
| Decision comment | The reviewer's recorded explanation supporting an approval or rejection. | It forms part of the protected lifecycle history. |
| Pending review | A non-terminal request condition in which a required reviewer decision is outstanding. | The request cannot proceed beyond that stage until a valid decision is recorded. |

## Grants and access lifecycle

| Term | Definition | Important distinction |
| --- | --- | --- |
| Access grant | The Aegis Access record representing a subject's approved permission for a resource during a defined period. | A grant is created only after all required approvals and validations succeed. |
| Grant state | The current lifecycle position of an access grant. | Only a valid active grant may contribute to authorisation. |
| Activation time | The earliest time at which a grant may become active. | Approval time and activation time may differ. |
| Expiration time | The time at or after which a grant must no longer authorise access. | Backend authorisation must enforce it even if background processing is delayed. |
| Active grant | A grant that has become effective, has not expired or been revoked, and satisfies current policy. | Stored grant data alone is insufficient if the lifecycle or policy makes it invalid. |
| Expiration | The automatic end of a grant because its approved time limit has been reached. | Expiration is time-driven and differs from manual revocation. |
| Expired grant | A grant whose expiration time has been reached. | It remains historical evidence but provides no authorisation. |
| Revocation | An authorised action that terminates an active grant before its scheduled expiration. | It requires an attributable actor and recorded reason. |
| Revoked grant | A grant terminated through revocation. | It remains available as historical evidence but provides no authorisation. |
| Revocation reason | The recorded explanation for terminating a grant early. | It is required lifecycle and audit evidence. |
| Lifecycle history | The ordered record of states, transitions, decisions, and events associated with a request or grant. | It presents domain history and may reference multiple audit records. |

## Policy and authorisation

| Term | Definition | Important distinction |
| --- | --- | --- |
| Authentication | The process of establishing the identity represented by a principal. | Authentication establishes identity; it does not determine permission to perform an operation. |
| Authorisation | The process of deciding whether a principal may perform a specific operation in the current context. | It considers roles, scope, relationships, state, policy, and business rules. |
| Authorisation policy | An enforceable backend rule that determines whether an operation is permitted. | Interface visibility is not an authorisation policy. |
| Business rule | A stable, testable statement governing valid domain behaviour. | It describes required behaviour independently of a particular interface or implementation. |
| Policy | A rule or collection of rules governing security or operational decisions. | A policy may implement one or more business rules. |
| Policy conflict | A condition in which a proposed operation violates an applicable policy. | A mandatory conflict prevents the operation. |
| Separation of duties | A security principle that prevents one actor or conflicting roles from controlling incompatible stages of a sensitive process. | It includes but is not limited to prohibiting self-approval. |
| Conflict of interest | A relationship or condition that makes an actor ineligible to perform a decision. | The system must reject the operation even if the actor otherwise holds the required role. |
| Least privilege | The principle that identities receive only the minimum authority required for their responsibilities. | Broad convenience access is not permitted. |
| Deny by default | The rule that an operation is forbidden unless an explicit valid rule permits it. | Missing or ambiguous policy never implies permission. |
| Self-approval | An actor approving a request for which they are the requester or subject. | It is prohibited. |
| Invariant | A condition that must remain true throughout the valid lifetime of a domain object or operation. | A transition that would violate an invariant must be rejected. |

## Lifecycle and consistency

| Term | Definition | Important distinction |
| --- | --- | --- |
| Lifecycle state | A named condition representing the current position of a domain object in its lifecycle. | State determines the transitions and operations that are valid. |
| State transition | An authorised change from one lifecycle state to another. | A transition must have a valid source, target, trigger, and actor. |
| Initial state | The state in which a newly created domain object begins its lifecycle. | It has no preceding domain state. |
| Intermediate state | A non-terminal state from which further valid transitions are expected. | It does not represent final lifecycle completion. |
| Terminal state | A state from which no ordinary lifecycle transition is permitted. | Historical inspection remains possible. |
| Invalid transition | An attempted state change not permitted from the current state or context. | It must be rejected without corrupting persisted state. |
| Stale update | An attempted change based on an older state than the currently persisted state. | It must not overwrite a newer valid decision or transition. |
| Concurrency conflict | A conflict caused by multiple actors or processes attempting incompatible changes at the same time. | The system must preserve one consistent lifecycle result. |
| Idempotency | The property that safely repeating the same operation does not create additional contradictory effects. | It is especially important for retries and automated lifecycle processing. |

## Audit and evidence

| Term | Definition | Important distinction |
| --- | --- | --- |
| Audit event | A security-relevant occurrence that must be captured as evidence. | It is the domain occurrence that causes an audit record to be written. |
| Audit record | A stored representation of an audit event containing required evidence such as actor, action, entity, time, and outcome. | It is protected from ordinary application-level modification. |
| Audit trail | The ordered collection of audit records used to reconstruct security-relevant activity. | It may span multiple requests, grants, users, and resources. |
| Audit history | The audit evidence associated with a selected entity or investigation. | It is a filtered view of the broader audit trail. |
| Audit export | A generated representation of authorised filtered audit data. | Exporting evidence is itself a security-relevant action. |
| Outcome | The recorded result of an attempted action, such as success, rejection, or failure. | An outcome differs from a review decision and applies to broader audit events. |
| Correlation identifier | A value used to associate related operations, logs, or audit evidence across a workflow. | It must not contain credentials or sensitive token material. |

## Supporting concepts

| Term | Definition | Important distinction |
| --- | --- | --- |
| Notification | A product message informing a user about a relevant workflow or lifecycle event. | Notification failure must not reverse a completed security decision. |
| Dashboard | A role-appropriate interface summarising work, status, or evidence. | Displayed information remains constrained by backend authorisation. |
| Protected operation | An operation that requires successful authentication and authorisation. | Hiding its interface control is not sufficient protection. |
| External system | A system or service represented by a governed resource. | Aegis Access v1.0.0 does not provision real permissions to it. |
| Demonstration environment | The documented non-production environment used to validate and present the project. | It must not be described as a production access-governance deployment. |

## Term-usage rules

Use the following distinctions consistently:

- Use **identity** for the externally authenticated representation and **user** for the internal Aegis Access record.
- Use **actor** for whoever performs an action and **subject** for whoever is affected by the requested or granted access.
- Use **request** for a proposal and **grant** for approved time-bound access.
- Use **review** for the evaluation process and **decision** for its recorded outcome.
- Use **approval** only for a positive decision, not for the entire workflow.
- Use **expiration** for a time-driven end and **revocation** for an authorised early termination.
- Use **role** for product responsibilities and **scope** for the entities over which those responsibilities may be exercised.
- Use **audit event** for the occurrence and **audit record** for its stored evidence.
- Do not use the general word **access** when the precise concept is a permission, request, approval, or grant.
- Do not describe an interface restriction as an authorisation control unless the backend independently enforces it.

## Normative language

Within requirements and business rules:

- **must** and **shall** identify mandatory behaviour;
- **must not** and **shall not** identify prohibited behaviour;
- **should** identifies recommended behaviour that requires justification when omitted;
- **may** identifies permitted or optional behaviour.

## Glossary maintenance

A glossary change must:

- use a traceable Azure Boards work item;
- explain why the existing terminology is insufficient;
- update affected requirements, journeys, rules, diagrams, tests, and code;
- avoid silently changing the meaning of an established identifier;
- complete the established pull-request review workflow.
