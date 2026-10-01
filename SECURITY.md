# Security Policy

## Project status

Aegis Access is under active pre-release development. Security fixes are applied only to the latest version of the `main` branch.

| Version | Supported |
|---|---|
| Latest main branch | Yes |
| Earlier commits, branches, and tags | No |

## Reporting a vulnerability

Report suspected vulnerabilities through GitHub's private vulnerability-reporting feature:

1. Open the repository's **Security** tab.
2. Select **Advisories**.
3. Select **Report a vulnerability**.

Do not disclose suspected vulnerabilities through public pull requests, commit messages, discussions, or other public channels.

Include the following information where possible:

- a clear description of the vulnerability;
- the affected component, version, or commit;
- reproducible steps or a minimal proof of concept;
- the potential security impact;
- any suggested remediation;
- whether the issue has been disclosed somewhere.

Never include real credentials, access tokens, personal data, or other sensitive information in a report. Use synthetic test data whenever possible.

## Response process

The maintainer aims to acknowledge reports within five business days. Each report will be reviewed, validated, and prioritised according to its likelihood and impact.

Confirmed vulnerabilities will be addressed through the normal tracked development workflow. Remediation timelines may vary according to severity, complexity, and project availability.

## Responsible testing

Security research must:

- target only environments and accounts that the researcher owns or is explicitly authorised to test;
- avoid accessing, altering, or deleting another person's data;
- avoid denial-of-service testing, social engineering, phising, and physical attacks;
- minimise data collection and operational disruption;
- stop immediately if unintended access to sensitive information occurs.

This repository does not grant permission to test Microsoft Entra ID, Microsoft Azure, GitHub, GitLab, Figma, or any other third-party service.

## Coordinated disclosure

Allow reasonable time for investigation and remediation before public disclosure. Disclosure timing should be coordinated with the maintainer.

Credit may be provided to reporters who request it, subject to their consent.

## Bug bounty

Aegis Access does not currently operate a bug-bounty programme and does not promise financial compensation for vulnerability reports.
