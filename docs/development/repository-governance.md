# Repository Governance

## Purpose

This document records the repository controls that protect the integrity of the Aegis Access codebase and define how changes reach the default branch.

## Canonical systems

- GitHub is the canonical source-code repository.
- Azure Boards is the canonical work-tracking system.
- Every repository change must be associated with an Azure Boards work item.
- GitHub Issues are disabled to avoid maintaining duplicate backlogs.
- Pull-request creation is restricted to repository collaborators.

## Protected default branch

The `main` branch is protected by the active GitHub ruleset named `Protect main`.

The ruleset applies to the repository's default branch and has no configured bypass actors.

### Enforced rules

The following protections are active:

- Deletion of the protected branch is restricted.
- A linear commit history is required.
- Changes must be introduced through a pull request.
- Direct pushes to the protected branch are blocked.
- Pull-request conversations must be resolved before merging.
- Only squash merging is permitted.
- Force pushes are blocked.

## Pull-request and merge policy

Pull requests currently require zero approving reviews because Aegis Access is maintained by a single developer.

This exception does not remove the requirement to:

- Link the pull request to an Azure Boards work item.
- Complete a self-review.
- Satisfy the work item's acceptance criteria.
- Resolve all pull-request conversations.
- Complete the pull-request template.
- Merge through the protected branch workflow.

Merged branches are deleted automatically. GitHub may suggest updating a pull request branch when it falls behind `main`.

## Deferred protections

The following protections are intentionally deferred:

- Mandatory approval from another reviewer, until another regular collaborator joins the project.
- Required status checks, until the continuous-integration workflow exists and has been validated.
- Required code-scanning results, until code scanning is configured.
- Required code-quality results and coverage thresholds, until the relevant analysis tools and baselines are established.
- Required deployments, until deployment environments and the delivery pipeline exist.
- Required signed commits, until a documented signing policy and contributor onboarding process are established.
- Automatic Copiot code-review requests, until their value and usage policy are evaluated.

Deferred controls must be reconsidered when their supporting processed are introduced.

## Verification

The repository configuration was reviewed on 30 September 2026.

The review confirmed that:

- The `Protect main` ruleset is active.
- The ruleset targets the default branch.
- No bypass actors are configured.
- Pull requests are required.
- Direct pushes, force pushes, and branch deletion are restricted.
- Conversation resolution and linear history are required.
- Squash is the only permitted merge method.
- Merged branches are deleted automatically.

## Maintenance

This document must be updated whenever repository rules, merge settings, review requirements, or canonical project systems change.
