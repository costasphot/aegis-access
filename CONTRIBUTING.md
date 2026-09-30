# Contributing to Aegis Access

Thank you for your interest in Aegis Access. This document defines the contribution, review, and traceability workflow used throughout the project.

## Sources of Truth

| Concern | Source of truth |
| --- | --- |
| Source code and pull requests | GitHub |
| Backlog, roadmap, and work tracking | Azure Boards |
| Interface design and prototypes | Figma |
| Architecture and technical decisions | Repository documentation |
| Secondary repository mirror | GitLab |

GitHub Issues are intentionally disabled to avoid maintaining two competing work-tracking systems.

## Before starting work

Every change must have a corresponding Azure Boards work item before implementation begins.

Before writing code or documentation:

1. Confirm that the work item has a clear description and acceptance criteria.
2. Assign the work item to its responsible contributor.
3. Estimate the work where appropriate.
4. Move the work item to `Active`.
5. Create a branch from the latest version of `main`.
6. Link the branch to the work item.

## Branches

Never commit directly to `main`.

Create branches using the following format:

```text
<type>/<work-item-id>-<short-description>
```

Allowed branch types are:

- `feature` for new product functionality
- `fix` for detect corrections
- `docs` for documentation-only changes
- `refactor` for behaviour-preserving code improvements
- `test` for test-only changes
- `security` for security-focused changes
- `chore` for maintenance and tooling changes

Example:

```text
feature/21-submit-access-request
docs/4-contribution-traceability-workflow
fix/38-prevent-expired-grant-use
```

Use lowercase words separated by hyphens. Keep each branch focused on one work item.

## Commits

Write human-readable commit messages that explain the completed change.

The subject must:

- use the imperative mood;
- use sentence case;
- be approximately 50-72 characters;
- end with a period;
- describe one coherent change.

When additional context is useful, add a body after a blank line. Explain why the change was needed, the approach taken, and any important trade-offs. Wrap body lines at approximately 78 characters.

Include the Azure Boards reference in the commit body:

```text
AB#<work-item-id>
```

Example:

```text
Document the repository contribution workflow.

Define the branch, commit, review, and traceability requirements that future changes must follow.

AB#4
```

An `AB#` reference in a pushed commit message links the commit to the corresponding Azure Boards work item.

## Pull requests

All changes to `main` must be submitted through a pull request.

Each pull request must:

- address one primary Azure Boards work item;
- use a clear, human-readable title;
- explain what changed and why;
- include `AB#<work-item-id>` in its description;
- identify testing and validation performed;
- document security or architectural implications;
- update affected documentation;
- satisfy the work item's acceptance criteria.

Place the `AB#` reference in the pull-request description. A reference included only in the pull-request title does not create the Azure Boards link.

Use a draft pull request while implementation or validation remains incomplete.

## Review

Before requesting review, the author must perform a complete self-review.

The review must confirm that:

- the change matches the work item's scope and acceptance criteria;
- unrelated changes are excluded;
- tests pass;
- documentation is accurate;
- no credentials, secrets, or sensitive data are committed;
- security and accessibility implications are considered;
- automated checks pass when applicable.

When another reviewer is available, independent approval should be obtained before merging. A solo-maintained change must never be presented as having received independent review.

## Merging

Use squash merging to keep `main` focused and readable.

After a pull request is merged:

1. Delete the merged branch.
2. Verify that the pull request and resulting commit appear on the work item.
3. Move the work item to `Resolved`.
4. Confirm that its acceptance criteria are satisfied.
5. Move the work item to `Closed`.

## Documentation

Documentation is part of the product.

Update documentation whenever a change affects:

- product behaviour;
- architecture or security decisions;
- development workflows;
- configuration;
- deployment or operations;
- user or administrator procedures.

Significant architectural decisions must be recorded as Architecture Decision Records before or alongside implementation.

## Definition of done

Work is complete only when:

- all acceptance criteria are satisfied;
- implementation and relevant tests are complete;
- documentation is current;
- required checks and reviews are complete;
- the pull request is merged;
- the Azure Boards work item is closed;
- the temporary branch is deleted.

## Reference

See the [Azure Boards and GitHub integration documentation](https://learn.microsoft.com/en-us/azure/devops/boards/github/link-to-from-github?view=azure-devops) for supported work-item linking syntax.
