# Repository Strategy

| Field | Value |
|---|---|
| Status | Accepted |
| Version | 1.0 |
| Owner | Project maintainer |
| Created | 30 September 2026 |
| Last updated | 30 September 2026 |
| Related work item | AB#6 |
| Related documents | [Repository governance](repository-governance.md), [Git workflow](git-workflow.md) |

## Purpose

This documents defines the responsibilities of GitHub, GitLab, and Azure Boards within the Aegis Access development process.

Its purpose is to prevent divergent repository histories, duplicate review workflows, unclear ownership, and conflicting sources of truth.

## Decision summary

| Responsibility | Canonical System |
|---|---|
| Source code and Git history | GitHub |
| Branches and pull requests | GitHub|
| Code review and merging | GitHub |
| Work items and delivery tracking | Azure Boards |
| Secondary repository copy | GitLab |
| Interface design | Figma |

Only the canonical system for a responsibility may be used to make authoritative changes.

## GitHub responsibilities

GitHub is the canonical source-code repository for Aegis Access.

All repository changes must:

1. Begin with an Azure Boards work item.
2. Be developed on a dedicated GitHub branch.
3. Reference the work item using `AB#<id>`.
4. Be submitted through a GitHub pull request.
5. Satisfy the applicable acceptance criteria and review requirements.
6. Be merged into the protected `main` branch using squash merging.

GitHub contains the authoritative:

- Commit history.
- Branches and tags.
- Pull requests and review conversations.
- Repository rulesets.
- GitHub Actions workflows.
- Release source state.

If GitHub and GitLab ever disagree, GitHub is authoritative.

## GitLab responsibilities

GitLab will provide a secondary public mirror of the GitHub repository.

The mirror exists to:

- Demonstrate familiarity with both repository-hosting platforms.
- Provide an independently hosted copy of the Git history.
- Improve project visibility.
- Support recovery if temporary access to GitHub is unavailable.

GitLab is not an independent development repository.

The following activities are prohibited on the GitLab mirror:

- Direct human pushes.
- Independent commits or branches.
- Merge requests.
- Code reviews.
- Issue tracking.
- Releases that do not originate from GitHub.
- Changes that are not already present in the canonical GitHub repository.

The GitLab project description and README must clearly identify the project as a mirror and link to the canonical GitHub repository.

Where GitLab permits it, issue tracking and merge requests should be disabled to prevent contributors from using the wrong workflow.

## Synchronisation direction

Synchronisation is strictly one-way:

1. A change is reviewed and accepted through GitHub.
2. GitHub updates the canonical repository state.
3. Automation copies the relevant Git references to GitLab.
4. GitLab passively reflects the resulting GitHub state.

GitLab must never synchronise changes back into GitHub.

Bidirectional mirroring is prohibited because it introduces multiple write paths, race conditions, and conflicting repository histories.

## Mirrored content

The mirror should reproduce:

- Commit history.
- Maintained branches.
- Tags.
- Repository files.

The following platform-specific data is outside the Git mirror and is not expected to be synchronised:

- Azure Boards work items.
- GitHub pull requests and review conversations.
- GitHub repository rulesets.
- GitHub Actions execution history.
- GitHub Issues.
- GitLab issues and merge requests.
- Figma files.
- Platform-specific release metadata.

## Planned automation

Automatic mirroring is deferred until the repository automation and continuous-integration milestone.

The planned implementation will use a GitHub Actions workflow to push the canonical Git references to the GitLab mirror after relevant GitHub updates.

This approach avoids requiring bidirectional synchronisation and does not depend on GitLab pull mirroring, whose availability depends on the selected GitLab subscription tier.

The mirroring workflow must:

- Run only from trusted repository events.
- Treat GitHub as the source and GitLab as the destination.
- Avoid executing untrusted pull-request code with mirroring credentials.
- Report synchronisation failures visibly.
- Use a dedicated, least-privilege automation credential.
- Never expose credentials in workflow output.
- Be introduced through a separate Azure Boards work item and pull request.
- Be tested against a non-critical branch before synchronising `main`.

No automated mirroring is implemented by this document.

## Credentials and secrets

Mirroring credentials must never be committed to the repository.

The automation must use a dedicated GitLab credential scoped only to the Aegis Access mirror. Depending on the available GitLab plan, this should be either:

- A project-scoped credential with only the repository-write permission required for mirroring.
- A dedicated write-enabled deploy key restricted to the mirror project.

The credential must:

- Be stored as a GitHub Actions secret.
- Have no unrelated API, administrative, group, or account permissions.
- Be accessible only to the mirroring workflow.
- Be rotated periodically and immediately after suspected exposure.
- Be revoked when mirroring is disabled or replaced.
- Use an expiration date when the seelected credential type supports one.

Workflow logs, documentation, examples, screenshots, and pull requests must not contain the credential value.

## Synchronisation failures

A mirroring failure does not change which repository is authoritative.

When synchronisation fails:

1. GitHub development continues normally.
2. The failed workflow and its error are reviewed.
3. Authentication, connectivity, permissions, and refernce divergence are checked.
4. The automation is retried only after the cause is understood.
5. GitLab is reconciled to GitHub; GitHub is never reconciled from GitLab.
6. Any credential suspected of exposure is revoked and replaced.

No manual development may occur on GitLab while synchronisation is unavailable.

Unexpected GitLab-only commits, branches, or tags are treated as repository divergence. They must be investigated before the mirror is restored to the canonical GitHub state.

## Recovery expectations

The GitLab mirror is a secondary copy, not a complete backup of every project system.

If GitHub becomes temporarily unavailable, the GitLab repository may be used to inspect or clone the most recently synchronised Git history. Development must not switch permanently to GitLab without a separately reviewed migration decision.

Azure Boards, Figma, repository settings, secrets, pull-request discussions, and other platform-specific data require their own continuity arrangements.

## Review triggers

This strategy must be reviewed when:

- Automated mirroring is implemented or replaced.
- GitHub or GitLab changes its relevant capabilities or subscription terms.
- Repository ownership changes.
- A secondary canonical development platform is proposed.
- Synchronisation fails repeatedly.
- A mirroring credential is exposed.
- The project migrates away from GitHub.
- The contribution or release workflow changes.

## References

- [GitLab repository mirroring](https://docs.gitlab.com/user/project/repository/mirror/)
- [GitLab deploy keys](https://docs.gitlab.com/user/project/deploy_keys/)
- [GitLab access-token scopes](https://docs.gitlab.com/security/tokens/access_token_scopes/)
- [GitHub Actions secrets](https://docs.github.com/en/actions/reference/security/secrets)
- [GitHub Actions secure-use reference](https://docs.github.com/en/actions/reference/security/secure-use)
