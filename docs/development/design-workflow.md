# Design Workflow

## Purpose

This document defines how user-interface and user-experience design work is planned, created, reviewed, versioned, linked, and handed off for implementation in Aegis Access.

## Source of truth

The [Aegis Access - Product Design](https://www.figma.com/design/QhIGLHMc8cizMMOE5rpb3c/Aegis-Access---Product-Design) Figma file is the canonical source of truth for UI and UX decisions.

Azure Boards remains the canonical system for planning and work-item traceability. GitHub contains the implementation and its review history.

## Figma file structure

The figma file uses three pages:

### 00 - Foundations

- Cover
- Design Foundations
- Components
- Design Decisions

### 01 - Product Design

- User Flows
- Wireframes
- Screens
- Prototypes
- Handoff

### 99 - Archive

- Archived Explorations
- Retired Components
- Superseded Screens

New work must be placed in the appropriate section. Superseded work must be moved to the archive instead of being silently deleted when retaining it provides useful design history.

## Design workflow

1. Create or identify the relevant Azure Boards work item.
2. Define the design problem, scope, and acceptance criteria.
3. Create the required flow, wireframe, screen, component, or prototype in Figma.
4. Link the relevant Figma file, section, or frame from the Azure Boards work item.
5. Review the design for consistency, accessibility, security, responsiveness, and required interface states.
6. Mark the design as ready for development only when its relevant decisions are sufficiently defined.
7. Link the applicable Figma design from the implementation pull request.
8. Update the design if implementation introduces an approved change.
9. Save a named Figma version at meaningful project or design milestones.
10. Archive superseded designs when appropriate.

Design work that is intended for implementation must begin from an Azure Boards work item.

## Naming conventions

Use concise, descriptive names in title case.

Recommended patters include:

- User flow: `Flow / <Flow name>`
- Wireframe or screen: `<Feature> / <Screen> / <State> / <Viewport>`
- Component: `<Category> / <Component> / <Variant> / <State>`
- Prototype: `Prototype / <Flow name> / <Scenario>`
- Design decision: `Decision / <Subject>`

Avoid names such as `Frame 1`, `Copy`, `Final`, or `Final Final`.

## Design statuses

Use the following statuses consistently:

- `Exploration` - an early concept that is not ready for implementation.
- `In review` - a design undergoing review or awaiting a decision.
- `Ready for development` - sufficiently defined and approved for implementation.
- `Implemented` - represented by merged application code.
- `Archived` - retained for historical reference but no longer current.

Only designs marked `Ready for development` should normally be used as implementation references.

## Required interface states

When applicable, designs must account for:

- Default
- Hover
- Focus
- Active
- Disabled
- Loading
- Empty
- Success
- Validation
- Error
- Unauthorised
- Forbidden

Responsive behaviour and accessibility expectations must also be documented when they are relevant.

## Traceability requirements

Each implementation-related design must be traceable through Azure Boards, Figma, and GitHub.

- The Azure Boards work item must link to the relevant Figma resource.
- The implementation branch, commits, and pull request must reference the applicable Azure Boards ID.
- The pull request must link to the relevant Figma design in its User-interface evidence section.
- Material design decisions must be documented in Figma or the appropriate repository documentation.
- The implemented result must remain consistent with the approved design unless a deviation is reviewed and documented.

## Figma versioning

Create named versions for meaningful checkpoints, including:

- Initial workspace creation
- Approved foundations
- Approved user flows
- Development-ready screens
- Significant redesigns
- Release milestones

Use the following format:

`<Milestone> <checkpoint>`

Example:

`M0 design workspace`

The version description should reference the Azure Boards work item and summarise the checkpoint.

Example:

`AB#7 - Established the initial Aegis Access design-file structure and cover.`

Named versions are not required for minor alignment, spacing, or wording adjustments.

## Sharing and access

The Figma file may be shared publicly with view-only access for portfolio review.

Editing access must remain restricted to explicitly authorised collaborators. Public viewing does not grant permission to alter the canonical design file.

The public sharing configuration must be reviewed whenever collaborators, permissions, or project visibility change.

## Security and privacy

Figma must never contain:

- Credentials, secrets, tokens, or private keys
- Real production data
- Unnecessary personal or sensitive information
- Confidential tenant or customer information
- Internal URLs that expose protected resources

Mock data must be fictional and clearly non-sensitive.

## Local backups and exports

Local Figma backups and exported assets may be stored under the repository's ignored `private/` directory.

These files must not be committed. They are supporting backups only and do not replace Figma as the canonical design source.

Create a local backup at meaningful milestones and before significant restructuring. Maintain an additional backup outside the repository workspace when appropriate.

## Review expectations

Before a design is marked `Ready for development`, verify that:

- The design satisfies the linked work item.
- Required states and responsive behaviour are represented.
- Reusable components are used consistently.
- Accessibility considerations have been reviewed.
- Security-sensitive interactions do not expose unnecessary information.
- Relevant decisions and constraints are documented.
- The implementation team can understand the expected behaviour without relying on undocumented assumptions.
