# Git workflow

## Purpose

This document defines how planned work moves from Azure Boards through GitHub
and into the protected `main` branch.

The workflow provides traceability between requirements, implementation,
review, and repository history.

## Workflow overview

Every change follows this sequence:

1. Create and define an Azure Boards work item.
2. Confirm its parent, owner, priority, risk, and acceptance criteria.
3. Move the work item from `New` to `Active`.
4. Create a focused branch from the latest `main`.
5. Link the branch to the work item.
6. Implement and validate the change.
7. Create a pull request containing the work-item reference.
8. Complete review and automated checks.
9. Squash and merge the pull request.
10. Verify the resulting GitHub links in Azure Boards.
11. Move the work item to `Resolved`.
12. Confirm its acceptance criteria and move it to `Closed`.
13. Delete the merged branch.

## Work-item hierarchy

Aegis Access uses the following Azure Boards hierarchy:

```text
Epic
└── Feature
    └── User Story
        └── Task
```

- **Epics** represent major releases or long-term product outcomes.
- **Features** represent substantial capabilities or project milestones.
- **User Stories** represent independently valuable and verifiable outcomes.
- **Tasks** represent implementation steps required to complete a User Story.
- **Bugs** represent defects in expected behaviour.
- **Issues** represent risks, blockers, decisions, or other impediments.

Implementation should normally be linked to the lowest relevant work item,
usually a User Story, Task, or Bug. Long-lived branches such as `main` must not
be linked to an entire Epic.

## Preparing a work item

Before implementation begins, confirm that the work item contains:

- a clear and focused title;
- sufficient context;
- measurable acceptance criteria;
- the correct parent;
- an assignee;
- priority and risk values;
- an estimate where appropriate;
- a suitable value area and tags.

Keep the item in `New` while it is being prepared. Move it to `Active` only when
implementation begins.

## Creating a branch

Synchronise the local `main` branch before creating a work branch:

```bash
git switch main
git pull --ff-only origin main
git status
```

The working tree must be clean before continuing.

Create the branch using:

```text
<type>/<work-item-id>-<short-description>
```

Example:

```bash
git switch -c docs/4-contribution-traceability-workflow
git push -u origin docs/4-contribution-traceability-workflow
```

Supported branch types are defined in the repository's `CONTRIBUTING.md`.

## Linking branches

Each implementation branch must be linked to its Azure Boards work item.

A GitHub branch can be created directly from the Azure Boards work item.
Alternatively, create and push it locally, then use the work item's
**Development** section to add the GitHub branch link.

Confirm that the branch appears in the Development section before substantial
implementation begins.

## Linking commits

Include the following reference in the body of each relevant commit message:

```text
AB#<work-item-id>
```

Example:

```text
Document the repository contribution workflow.

Define the branch, commit, review, and traceability requirements that future
changes must follow.

AB#4
```

Azure Boards creates the commit link after the commit is pushed to the connected
GitHub repository.

A commit should reference the work item it directly implements. Avoid linking
every commit to a high-level Feature or Epic.

## Creating a pull request

Create a pull request from the work branch into `main`.

The pull-request description must contain:

```text
AB#<work-item-id>
```

An `AB#` reference placed only in the pull-request title does not create the
Azure Boards link.

Use the repository pull-request template and complete every applicable section.
Open the pull request as a draft if implementation or validation is incomplete.

## Review and validation

Before merging:

* verify that the implementation matches the acceptance criteria;
* review the complete diff;
* run all relevant tests and quality checks;
* confirm that documentation is current;
* check for secrets and sensitive information;
* evaluate security and accessibility implications;
* confirm that the Azure Boards work item is linked.

Solo-maintained changes require an explicit self-review. Independent review
must only be claimed when another person has actually reviewed the change.

## Merging

Use **Squash and merge** after all applicable checks and reviews pass.

The squash commit must follow the repository's commit-message convention and
retain the `AB#<work-item-id>` reference.

Direct pushes, force pushes, and branch deletion must be blocked on `main`.

## Closing the work item

After merging:

1. Confirm that the pull request and resulting commit appear in the work item's
   Development section.
2. Move the work item to `Resolved`.
3. Verify every acceptance criterion.
4. Move the work item to `Closed`.
5. Delete the merged work branch.

Do not close a work item solely because its pull request was merged. Closure
means that the agreed outcome has been verified.
