# Aegis Access Project Charter

| Field | Value |
|---|---|
| Project | Aegis Access |
| Document status | Draft |
| Document version | 0.1 |
| Project owner | Costas |
| Current phase | Product definition and repository foundation |
| Created | 3 September 2026 |
| Last updated | 4 September 2026 |

## 1. Executive summary

Aegis Access is an enterprise access-governance platform for requesting, reviewing, granting, monitoring, expiring, and revoking time-bound access to organisational resources.

The platform will provide structured approval workflows, policy-based authorisation, separation-of-duties controls, and an append-only audit history. Microsoft Entra ID will provide workforce authentication and application roles, while the ASP.NET Core backend will remain responsible for enforcing all authorisation and business rules.

The project is being developed as an educational and portfolio application using a professional software-development process. It may later provide the implementation and experimental foundation for an academic thesis, subject to supervisor approval and the definition of a suitable research contribution.

## 2. Background

Organisations need to give employees access to applications, infrastructure, data, and administrative capabilities without granting unnecessary or permanent privileges.

Access is often requested and approved through disconnected systems such as email, chat messages, spreadsheets, and service-desk tickets. These processes can make it difficult to establish:

- Who requested access,
- Which resource and permission were requested,
- Why the access was required,
- Who reviewed and approved the request,
- Which policy governed the decision,
- When the resulting access should expire,
- Whether the access was revoked,
- Whether conflicting responsibilities were properly separated, or
- Whether a complete audit record exists.

Aegis Access will explore how these decisions can be represented and managed through a cohesive, secure, and auditable application.

## 3. Problem statement

Employees require timely access to organisational resources, while organisations must limit excessive privilege and retain evidence of every access decision.

The absence of a structured access-governance process can lead to:

- Delayed access for legitimate work,
- Excessive or permanent permissions,
- Self-approval and conflicts of interest,
- Inconsistent approval decisions,
- Unclear ownership of sensitive resources,
- Missing expiration and revocation actions,
- Incomplete audit evidence,
- Difficulty demonstrating compliance, or
- Increased impact from compromised identities.

Aegis Access must balance operational usability with strong authorisation, traceability, and least-privilege controls.

## 4. Product vision

Aegis Access will provide organisations with a clear and dependable way to manage time-bound access decisions from request to revocation.

The platform should make the secure action the natural action by:

- Guiding employees through complete access requests,
- Routing requests to the correct reviewers,
- Applying consistent policies,
- Preventing conflicting or unauthorised decisions,
- Limiting grants to an approved duration,
- Making active privilege visible,
- Preserving a complete history of security-relevant actions, and
- Providing administrators and auditors with trustworthy evidence.

## 5. Project objectives

### 5.1. Product objectives

- Provide a complete access-request lifecycle.
- Support managerial and security approval stages.
- Represent organisational resources and permission levels.
- Support time-bound access grants.
- Expire grants automatically when their approved duration ends.
- Support explicit revocation before expiration.
- Preserve security-relevant actions in an append-only audit history.
- Provide appropriate dashboards for each application role.
- Provide searchable and exportable audit information.
- Communicate request and grant status clearly to affected users.

### 5.2. Security objectives

- Authenticate workforce users through Microsoft Entra ID.
- Deny protected operations by default.
- Enforce authorisation in the ASP.NET Core backend.
- Apply least-privilege access throughout the application.
- Prevent requesters from approving their own requests.
- Enforce organisational and resource ownership boundaries.
- Require additional review for sensitive resources.
- Validate the complete authorisation context for every protected operation.
- Avoid storing secrets or credentials in source control.
- Record authentication-independent audit event for important business actions.
- Test both allowed and forbidden authorisation behaviour.
- Threat-model sensitive workflows before implementing them.

### 5.3. Engineering objectives

- Use a modular monolith unless evidence justifies a different architecture.
- Develop the product through small, end-to-end vertical slides.
- Keep the default branch releasable.
- Require automated quality checks before changes are merged.
- Maintain traceability between requirements, issues, decisions, code, and tests.
- Use reproducible local and cloud environments.
- Manage database changes through reviewed migrations.
- Implement structured logging, metrics, tracing, and health checks.
- Document important architectural decisions and their consequences.
- Produce versioned and reproducible releases.

### 5.4. Educational and portfolio objectives

- Develop practical experience with Angular and TypeScript.
- Develop secure ASP.NET Core APIs using C#.
- Gain applied experience with Microsoft Entra ID.
- Strengthen knowledge of authentication and authorisation.
- Apply relational database design using PostgreSQL.
- Practice professional requirements and architecture documentation.
- Apply threat modelling and secure development practices.
- Build automated unit, integration, authorisation, and end-to-end tests.
- Design and operate a professional CI/CD workflow.
- Demonstrate the reasoning behind important engineering decisions.
- Produce a credible public case study for employment applications.

### 5.5. Potential academic objectives

If approved as a thesis project, Aegis Access may serve as a platform for research into risk-adaptive, policy-based, or just-in-time access governance.

Any academic work must introduce a separate research question, methodology, evaluation process, and measurable contribution. The implementation of the product alone will not be treated as sufficient academic research.

## 6. Target users

### Employee

Requests access to resources, provides a business justification, selects the required duration, and monitors the resulting decision and grant status.

### Manager

Reviews requests submitted by members of an authorised organisational scope and decides whether the requested access is operationally justified.

### Security reviewer

Reviews requests involving sensitive resources, privileged permissions, policy exceptions, or elevated organisational risk.

### Auditor

Examines historical requests, decisions, grants, expirations, revocations, and other security-relevant events without modifying operational records.

### Administrator

Configures resources, permission levels, approval policies, organisational mappings, and application-level settings.

## 7. Stakeholders

| Stakeholder | Interest |
|---|---|
| Project owner and developer | Product design, implementation, learning, and portfolio value |
| Employees | Timely and understandable access-request processing |
| Managers | Reliable review of operational access requirements |
| Security personnel | Least privilege, policy enforcement, and risk reduction |
| Auditors | Complete and trustworthy evidence |
| Administrators | Maintainable configuration and operational visibility |
| Potential academic supervisors | Research relevance, methodology, and academic contribution |
| Prospective employers | Evidence of engineering, security, cloud, and documentation skills |

## 8. Initial scope

The initial product scope includes:

- Single-organisation workforce authentication through Microsoft Entra ID,
- Application roles for the supported user categories,
- Backend token validation,
- Backend role- and policy-based authorisation,
- User and organisational identity mappings,
- An organisational resource catalogue,
- Resource owners and sensitivity classifications,
- Permission levels associated with resources,
- Access-request creation and validation,
- Business justifications and requested durations,
- Managerial approval and rejection,
- Additional security review for sensitive requests,
- Separation-of-duties enforcement,
- Approved access-grant records,
- Automatic expiration,
- Manual revocation,
- Request, decision, grant, and revocation history,
- Append-only application audit events,
- Role-specific dashboards,
- Search, filtering, sorting, and pagination,
- Notifications within the application,
- Audit-report export,
- Automated testing,
- Containerised local development,
- CI/CD,
- Infrastructure as code,
- Staging and demonstration environments,
- Logging, metrics, tracing, and health monitoring, and
- Product, architecture, security, quality, and operations documentation.

## 9. Out of scope for the initial release

The following capabilities are excluded from the initial release:

- Replacement of Microsoft Entra Privileged Identity Management,
- Direct management of production Entra tenants,
- Automatic assignment of real privileged cloud permissions,
- Automatic membership changes in external security groups,
- Provisioning access to third-party applications,
- Multi-tenant software-as-a-service operation,
- Customer identity management,
- Native mobile or desktop applications,
- Billing and subscription management,
- Machine-learning-based authorisation decisions,
- Fully dynamic risk-adaptive authorisation,
- Microservice decomposition,
- Custom identity-provider implementation,
- Password storage or credential-vault functionality, and
- Autonomous approval or rejection by artificial intelligence.

Out-of-scope capabilities may be reconsidered through future requirements and architecture decisions.

## 10. Key deliverables

- Product charter,
- Product vision and requirements,
- User personas and domain glossary,
- Business-rule catalogue,
- Authorisation matrix,
- Architecture diagrams,
- Architecture decision records,
- Entity-relationship model,
- API specification,
- Threat model,
- Security requirements and policies,
- Angular frontend,
- ASP.NET Core backend,
- PostgreSQL database and migrations,
- Automated test suites,
- Docker-based development environment,
- CI/CD workflows,
- Infrastructure-as-code definitions,
- Staging and demonstration deployments,
- Observability configuration,
- Operations and incident-response runbooks,
- Versioned releases and changelog,
- Demonstration video,
- Public portfolio case study, and
- Academic report and evaluation, if the thesis direction is approved.

## 11. Success criteria

### Functional success

- A user can submit a valid access request.
- The request is routed through the required approval stages.
- Unauthorised users cannot review or modify the request.
- Approved requests produce time-bound access-grant records.
- Grants expire automatically at the correct time.
- Authorised administrators can revoke active grants.
- Users can determine the current and historical status of their requests.
- Auditors can inspect the complete history without modifying it.

### Security success

- Every protected backend operation requires an authenticated identity.
- Every protected backend operation evaluates the required authorisation policy.
- Every role and policy has automated permitted and forbidden test cases.
- Self-approval is prevented.
- Organisational and resource boundaries are enforced.
- Sensitive operations produce audit events.
- Secrets are excluded from the repository and build output.
- Identified high-risk threats have documented mitigations before release.
- Security scans complete successfully within CI.

### Engineering success

- The application can be built from a clean checkout using documented commands.
- The local environment can be reproduced using the documented container setup.
- All required CI checks pass before changes reach the default branch.
- Database migrations are repeatable and reviewed.
- Releases are versioned and reproducible.
- Staging deployment and smoke tests are automated.
- Important architecture decisions are documented.
- Operational failures produce useful and correlated diagnostic information.

### Documentation success

- Implemented behaviour is traceable to documented requirements.
- Significant decisions include their context, alternatives, and consequences.
- Setup, testing, deployment, and recovery procedures are reproducible.
- Public documentation accurately distinguishes implemented and planned work.
- The repository contains no misleading claims about unfinished capabilities.

### Portfolio success

- A prospective employer can understand the problem and architecture quickly.
- The repository demonstrates consistent planning and incremental development.
- The project contains meaningful security and authorisation logic.
- The implementation can be demonstrated through a deployed environment.
- The developer can explain and efend the principal decisions and trade-offs.

## 12. Constraints

- The project is developed and maintained by one developer.
- Development time must remain compatible with university and employment responsibilities.
- Cloud expenditure must remain controlled.
- Some Microsoft Entra governance capabilities may require paid licenses.
- Development and demonstration must not depend on access to a real employer's production environment.
- The project must not process real confidential organisational data.
- The architecture must remain understandable and maintainable by one developer.
- Potential thesis use may introduce academic rules concerning publication,
- GitHub will be the canonical repository if GitLab mirroring is introduced.

## 13. Assumptions

- A dedicated development Microsoft Entra tenant will be available.
- Demonstration users and resources will use synthetic data.
- PostgreSQL can represent all application-owned operational and audit data.
- Azure can host the planned demonstration environment.
- Microsoft Entra ID will remain the authoritative identity provider.
- External resource provisioning can initially be represented without modifying real external permissions.
- Product requirements and architecture will evolve through reviewed changes.
- The initial modular-monolith architecture will be sufficient for the expected scale.

## 14. Principal risks

| Risk | Potential impact | Planned response |
|---|---|---|
| Uncontrolled scope growth | Delayed or incomplete releases | Maintain explicit scope, non-goals, milestones, and deferred issues |
| Over-engineering | Complexity without user value | Require documented justification for major abstractions and infrastructure |
| Entra misconfiguration | Authentication or authorization weakness | Use a dedicated tenant, least privilege, threat modelling, and authorization tests |
| Secret exposure | Credential compromise | Use ignored local configuration, secret scanning, managed identities, and secret rotation procedures |
| Incorrect authorization | Unauthorised access or decisions | Centralise policies and test every permitted and forbidden role combination |
| Incomplete audit history | Unreliable security evidence | Define append-only audit rules and test all security-relevant workflows |
| Concurrent decisions | Duplicate or contradictory approvals | Define and test an explicit concurrency strategy |
| External-service dependency | Development or demonstration disruption | Isolate integrations and document degraded behaviour |
| Cloud cost growth | Unsustainable operation | Use budgets, alerts, modest resources, and removable environments |
| Repository divergence | Conflicting GitHub and GitLab history | Maintain GitHub as the sole writable canonical repository |
| Misleading portfolio claims | Loss of credibility | Clearly distinguish planned, implemented, tested, and deployed capabilities |
| Academic misalignment | Project cannot be accepted as a thesis | Obtain supervisor approval before defining it as academic work |
| Premature public disclosure | Academic or intellectual-property concerns | Keep the repository private until publication requirements are clarified |
| Solo-maintainer dependency | Slow review and knowledge concentration | Use documentation, automation, checklists, and reproducible environments |

## 15. Guiding principles

- Security by design
- Deny by default
- Least privilege
- Defence in depth
- Separation of duties
- Explicit trust boundaries
- Backend authorisation
- Complete auditability
- Privacy by design
- Small and reviewable changes
- Documented decisions
- Automated verification
- Reproducible environments
- Operational readiness
- Honest project status
- Simplicity before distribution

## 16. Governance

The project owner is responsible for product, architecture, security, and release decisions during the initial solo-development stage.

Significant decisions must be recorded through one or more of the following:

- Product requirements,
- Business rules,
- GitHub issues,
- Pull requests,
- Architecture Decision Records (ADRs),
- Thread-model updates, and/or
- Release notes.

Changes to the agreed scope, security model, canonical repository, primary architecture, or potential academic direction require explicit documentation.

## 17. Initial milestones

| Milestone | Outcome |
|---|---|
| M0 — Repository foundation | Repository safeguards, governance, and initial documentation |
| M1 — Product and security design | Requirements, domain model, authorization matrix, architecture, and threat model |
| M2 — Authenticated walking skeleton | Angular client calls a protected ASP.NET Core API through Entra ID |
| M3 — Resource and request management | Resources can be configured and valid access requests can be submitted |
| M4 — Approval workflow | Managerial and security approval rules are enforced |
| M5 — Grant lifecycle | Approved grants can become active, expire, and be revoked |
| M6 — Audit and reporting | Security-relevant activity can be searched and reported |
| M7 — Operational hardening | CI/CD, observability, deployment, recovery, and security controls are complete |
| M8 — Initial production-quality release | Version 1.0.0 is documented, tested, deployed, and demonstrable |

Milestone dates will be defined after the initial requirements and architecture have been reviewed.

## 18. Charter approval

This charter remains in draft status until the initial product scope, constraints, and success criteria have been reviewed and accepted by the product owner.

Acceptance of the charter establishes the baseline for subsequent requirements, architecture, and implementation work.
