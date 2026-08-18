# QA Investigation Harness Portfolio Makeover

## Goal

Make the repository present as a credible open-source engineering tool and portfolio project without changing the behavior of the QA investigation workflow or claiming capabilities that are not implemented.

## Current repository facts

- The repository is a documentation-and-prompt harness for investigating browser flows before Playwright test generation.
- The tracked implementation consists of Markdown instructions, Claude Code skills, GitHub Copilot prompt adapters, MCP configuration, and an MIT license.
- There is no application runtime, package manifest, build script, Docker configuration, database, committed test suite, or GitHub Actions workflow.
- The workflow is intentionally tool-agnostic at the methodology layer and currently has adapters for GitHub Copilot, Claude Code, and Codex guidance.
- The harness does not author Playwright tests; it produces observations, models, risk tables, and a plan artifact for a separate generator agent.

## Positioning

- **Project title:** QA Investigation Harness
- **One-line value proposition:** A risk-driven investigation layer that turns real browser observations and human-approved risks into a test-generation plan.
- **Audience:** QA and automation engineers, engineering managers, senior developers, technical reviewers, and contributors building Playwright-based workflows.
- **Strongest capabilities:** snapshot-based flow exploration, state/transition modeling, human-gated risk review, generator-compatible plan output, and post-generation oracle/mutation auditing.
- **Engineering differentiators:** one source of truth for rules, thin tool adapters, explicit evidence gaps, non-production safety constraints, and traceability from risk IDs to generated tests.

## Design and file changes

1. Replace the root `README.md` with a concise landing page grounded in the repository's actual files and behavior. It will include a hero block, restrained badges only where targets exist, capabilities, workflow, architecture Mermaid diagram, tool adapters, artifact contract, setup commands, rules, project structure, contribution guidance, and roadmap/limitations.
2. Add `docs/architecture.md` with a deeper description of the stage boundaries, artifact flow, human approval gate, and generator hand-off. The document will explicitly distinguish observed facts from later risk/model outputs.
3. Add `docs/assets/qa-investigation-harness-mark.svg` as a small reusable wordmark/mark and `docs/assets/qa-investigation-harness-social.svg` as a 1280x640 repository preview. These are abstract workflow visuals, not fabricated product screenshots.
4. Add `CONTRIBUTING.md`, `SECURITY.md`, and `.github/PULL_REQUEST_TEMPLATE.md` to make the repository's documentation/prompt contribution process clear and to provide a safe channel for reporting exposed credentials or security problems.
5. Update `.gitignore` to exclude the local `.codebase-memory/` index artifact produced by repository discovery, while preserving the existing rules for generated QA findings and local session state.
6. Do not add CI, dependency manifests, Docker instructions, screenshots of a nonexistent application, fake badges, generated test files, or GitHub metadata mutations that cannot be verified through the available connector.

## Content and safety rules

- Every README claim must be supported by a tracked file or by the observable workflow described in the stage instructions.
- Commands must match the repository's actual setup: Markdown/prompt changes require no package installation; Playwright MCP setup remains an optional tool-specific prerequisite.
- Credentials, URLs resembling production, and generated QA findings must not be added.
- Existing stage instructions and tool adapters remain behaviorally unchanged except for documentation links or clearly corrective presentation text.
- GitHub description, topics, social preview configuration, and demo URL are reported as manual recommendations because repository metadata write tools are unavailable.

## Verification

- Inspect the full diff for application-code or prompt-behavior changes.
- Check Markdown links and referenced paths against the repository tree.
- Search tracked files for obvious secret patterns without exposing values.
- Validate SVG files as well-formed XML and verify the social-preview dimensions.
- Confirm the working tree contains only intentional makeover files plus no generated index artifact.

