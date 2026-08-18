# Security policy

## Scope

The QA Investigation Harness contains workflow rules, prompt adapters, MCP configuration, and documentation. It is not an application runtime and does not store test accounts or investigation findings.

## Safe handling requirements

- Use non-production environments for exploration.
- Provide test credentials interactively when a stage requires them.
- Never write passwords, tokens, API keys, private URLs, session data, or private certificates into this repository or its generated artifacts.
- Do not include real investigation output in issues, pull requests, examples, or screenshots.
- Treat a credential that was committed in the past as exposed even if it has since been deleted; rotate it and review repository history.

## Reporting a vulnerability or exposed secret

Do not open a public issue with sensitive details. Use a private security-reporting channel available on the GitHub repository, or contact the repository owner privately through GitHub. Include the affected path, a short description of the exposure, and safe reproduction context without copying the secret itself.

If a credential may be active, rotate or revoke it first when you are authorized to do so. Then report the incident so the repository history and documentation can be reviewed.

## Dependency and tool safety

The repository currently has no application dependencies. Tool setup commands such as Playwright MCP installation should be reviewed before execution and should target only non-production environments during exploration.

