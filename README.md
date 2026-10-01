# Aegis Access

Aegis Access is an enterprise access-governance platform for requesting, reviewing, granting, monitoring, and revoking time-bound access to organisational resources.

The project is designed as a production-oriented full-stack application using Angular, ASP.NET Core, PostgreSQL, and Microsoft Entra ID. It focuses on secure identity and access management (IAM), traceable approval workflows, least-previlege authorisation, and auditable access decisions.

> [!IMPORTANT]
> Aegis Access is currently in the planning and design stage. It is not yet
> suitable for production use.

## The problem

Organisations must provide employees with the access required to perform their work without granting excessive or permanent privileges.

Informal access processes based on messages, spreadsheets, or undocumented administrator actions make it difficult to determine:

- Who requested access,
- Why access was required,
- Who approved the request,
- Which permissions were granted,
- When access should expire,
- Whether access was revoked, or
- Which policy governed the decision.

Aegis Access aims to provide a structured, secure, and auditable process for managing these decisions.

## Planned capabilities

- Authentication through Microsoft Entra ID,
- Application roles for employees, managers, security reviewers, auditors, and administrators,
- An organisational resource catalogue,
- Time-bound access requests with business justifications,
- Multi-stage approval workflows,
- Additional security approval for sensitive resources,
- Separation-of-duties enforcement,
- Active access-grant monitoring,
- Automatic expiration and manual revocation,
- Append-only audit history,
- Searchable dashboards and audit reports,
- Policy-based server-side authorisation,
- Notifications for requests, decisions, expirations, and revocations,
- Automated testing of authentication and authorisation boundaries,
- Containerised local development, and
- Reproducible cloud deployment.

## Planned technology stack

| Area | Technology |
|---|---|
| Frontend | Angular and TypeScript |
| Backend | ASP.NET Core and C# |
| Identity | Microsoft Entra ID |
| Database | PostgreSQL |
| Data access | Entity Framework Core |
| API security | Microsoft.Identity.Web |
| Local development | Docker and Docker Compose |
| Cloud platform | Microsoft Azure |
| Infrastructure | Bicep |
| Observability | OpenTelemetry and Azure Monitor |
| Version control | Git |
| Primary repository | GitHub |
| Repository mirror | GitLab |
| Continuous integration | GitHub Actions and GitLab CI/CD |

Specific framework and dependency versions will be recorded when the application foundation is created.

## Security principles

Aegis Access is being designed around the following principles:

- Deny access by default,
- Apply least privilege,
- Enforce authorisation within the backend,
- Treat frontend route guards as navigation controls, rather thna security boundaries,
- Separate authentication from authorisation,
- Prevent requesters from approving their own requests,
- Require additional approval for sensitive access,
- Validate resource ownership and organisational boundaries,
- Grant access for the minimum necessary duration,
- Preserve security-relevant actions in an append-only audit history,
- Avoid storing credentials in source control,
- Test both permitted and forbidden behaviour, and
- Record significant security and architecture decisions.

## Development approach

The project is developed incrementally through small, reviewable vertical slices.

Each implemented capability should be traceable through:

1. A product or security requirement,
2. A planned issue with acceptance criteria,
3. An architecture or design decisions where required,
4. A short-lived implementation branch,
5. A reviewed pull request,
6. Automated tests,
7. Updated documentation, and
8. A versioned release when the increment is complete.

The project intentionally begins with requirements, architecture, security analysis, and repository governance before application code is introduced.

## Current status

**Phase**: Product definition and repository foundation

Current work includes:

- Defining the project charter,
- Establishing repository standards,
- Defining the initial product scope,
- Designing the authorisation model,
- Preparing the initial architecture,
- Preparing the threat model, and
- Planning the first working vertical slice.

## Documentation

- [Project charter](PROJECT_CHARTER.md)
- [Documentation index](docs/README.md)

Additional product, architecture, security, quality, development, and operations documentation will be added as the related decisions are made.

## Project goals

Aegis Access is intended to demonstrate professional experience with:

- Enterprise frontend development,
- Secure ASP.NET Core API design,
- Identity and access management,
- Role- and policy-based authorisation,
- Relational data modelling,
- Security threat modelling,
- Automated testing,
- CI/CD and software supply-chain controls,
- Cloud infrastructure and observability, and
- Technical documentation and architecture governance.

## Project ownership and licensing

Aegis Access is independently designed and developed by G. Konstantinos Fotopoulos (Costas Phot) as an educational, portfolio, and potential academic project.

Aegis Access is proprietary and source-available. Its source code and documentation are publicly accessible solely for inspection, study, and evaluation. The project is not open-source.

Except when permitted by GitHub's Terms of Service or applicable law, reuse, modification, redistribution, sublicensing, sale, commercial exploitation, deployment, and derivative works require prior written permission.

The Aegis Access name, logo, product identity, and original visual assets remain proprietary. Third-party materials retain their respective licenses.

See the [source-available notice](LICENSE) for the complete terms.
