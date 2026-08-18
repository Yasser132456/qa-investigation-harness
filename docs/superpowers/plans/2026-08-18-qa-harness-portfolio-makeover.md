# QA Investigation Harness Portfolio Makeover Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Present the QA Investigation Harness as a credible, technically grounded open-source project without changing its investigation workflow.

**Architecture:** Keep `qa-harness/RULES.md` and the stage files as the behavioral source of truth. Improve the surrounding presentation through a root README, a linked architecture document, reusable SVG assets, contributor/security guidance, and a focused PR template. The visual assets communicate the real stage-to-plan flow and are not screenshots of a nonexistent application.

**Tech Stack:** Markdown, Mermaid, SVG, GitHub repository conventions, PowerShell verification, Git.

**Spec:** `docs/superpowers/specs/2026-08-18-qa-harness-portfolio-makeover-design.md`

## Global Constraints

- Do not change QA stage behavior, prompts, Claude skills, MCP configuration, or the license.
- Do not invent application features, runtime dependencies, test coverage, CI status, screenshots, deployments, users, or performance results.
- Do not add Playwright test code, seed files, credentials, production URLs, or generated QA findings.
- Use only badges whose targets exist; omit badges when no trustworthy workflow or release target exists.
- Keep GitHub description, topics, social-preview configuration, and demo URL as recommendations only because metadata write tools are unavailable.

---

### Task 1: Rebuild the repository landing page

**Files:**
- Modify: `README.md`

**Interfaces:**
- Consumes: The tracked workflow described in `qa-harness/RULES.md`, `qa-harness/README.md`, all stage files, `.github/prompts/`, `.claude/skills/`, `.mcp.json`, and `.vscode/mcp.json`.
- Produces: A root README that explains the real project within the first screen and links to `docs/architecture.md`, contribution/security guidance, and the actual configuration files.

- [ ] **Step 1: Replace the opening section with the approved positioning.**

  Use the title “QA Investigation Harness,” the value proposition “A risk-driven investigation layer for Playwright test generation,” and a short statement that the harness records browser observations, models transitions, gates risks through human review, and hands a plan to a separate generator.

- [ ] **Step 2: Add a factual hero area and capability summary.**

  Link the social-preview SVG as the hero visual, use no fabricated product screenshot, and summarize the five real capabilities: snapshot exploration, state/transition modeling, human-approved risk review, generator-compatible plan output, and oracle/mutation auditing.

- [ ] **Step 3: Document the workflow and architecture.**

  Add a Mermaid flow from ticket/test case through `qa-explore`, `qa-model`, `qa-challenge Part A`, human approval, `qa-challenge Part B`, the external generator, and `qa-validate`. Link the detailed architecture document instead of duplicating every rule in the README.

- [ ] **Step 4: Rewrite onboarding around real commands.**

  Document cloning, optional Playwright MCP setup for Codex and Claude Code, the three adapter entry points, the human approval pause, generator hand-off, and validation. State clearly that there is no package installation, application server, Docker setup, or built-in test suite in this repository.

- [ ] **Step 5: Add the repository map, constraints, contributing links, license, and metadata recommendations.**

  Show only meaningful tracked directories/files, explain that the harness never authors Playwright code and only supports non-production exploration, link `CONTRIBUTING.md` and `SECURITY.md`, and include the final recommended GitHub description/topics as a manual note rather than claiming they are configured.

- [ ] **Step 6: Review README references against the tree.**

  Run:

  ```powershell
  rg -n "\.github/agents|package\.json|docker-compose|\.github/workflows|tests/|specs/|qa-artifacts/|docs/" README.md
  rg --files -uu | Sort-Object
  ```

  Expected: every file path presented as existing is tracked or explicitly labeled as generated/optional output, and no nonexistent agent path is presented as part of this repository.

- [ ] **Step 7: Commit the landing-page change.**

  ```powershell
  git add README.md
  git commit -m "docs: reposition qa harness repository"
  ```

### Task 2: Add architecture and visual identity assets

**Files:**
- Create: `docs/architecture.md`
- Create: `docs/assets/qa-investigation-harness-mark.svg`
- Create: `docs/assets/qa-investigation-harness-social.svg`

**Interfaces:**
- Consumes: The same stage and adapter files documented by the README.
- Produces: A durable architecture reference and two local SVG assets referenced by the README and suitable for a GitHub social-preview upload.

- [ ] **Step 1: Write the architecture document.**

  Describe the source-of-truth rule layer, tool adapters, Playwright MCP boundary, artifact chain, human approval gate, external generator boundary, and validation output. Include a Mermaid diagram with only real components and a table naming each artifact and owner.

- [ ] **Step 2: Create the reusable SVG mark.**

  Make a compact 512x512 SVG using a dark navy background, an electric-cyan path, and three connected nodes representing observe → model → challenge. Include accessible `<title>` and `<desc>` elements and avoid external fonts or image dependencies.

- [ ] **Step 3: Create the 1280x640 social-preview SVG.**

  Use the same palette and mark, add the project title and one-line value proposition, and show a simplified connected artifact path. Keep text legible at preview size, include accessible metadata, and do not imply a live UI or unsupported integration.

- [ ] **Step 4: Link the assets from the README and architecture document.**

  Reference assets with repository-relative Markdown paths and state that the social-preview file should be uploaded manually in GitHub repository settings.

- [ ] **Step 5: Validate SVG structure and dimensions.**

  Run:

  ```powershell
  [xml](Get-Content -Raw docs/assets/qa-investigation-harness-mark.svg) | Out-Null
  [xml](Get-Content -Raw docs/assets/qa-investigation-harness-social.svg) | Out-Null
  rg -n 'viewBox="0 0 512 512"|viewBox="0 0 1280 640"|<title>|<desc>' docs/assets/*.svg
  ```

  Expected: both files parse as XML, contain title/description metadata, and expose the declared dimensions/viewBox values.

- [ ] **Step 6: Commit the architecture and assets.**

  ```powershell
  git add docs/architecture.md docs/assets
  git commit -m "docs: add harness architecture and visual identity"
  ```

### Task 3: Add contributor, security, and review guidance

**Files:**
- Create: `CONTRIBUTING.md`
- Create: `SECURITY.md`
- Create: `.github/PULL_REQUEST_TEMPLATE.md`

**Interfaces:**
- Consumes: The repository's rule that the harness never writes Playwright tests and the existing stage/adapter layout.
- Produces: Practical guidance for documentation, prompt, stage-rule, and adapter changes without boilerplate promises.

- [ ] **Step 1: Write `CONTRIBUTING.md`.**

  Explain the source-of-truth hierarchy, how to change shared rules versus adapters, how to preserve hard constraints, what to inspect before opening a pull request, and the exact Markdown/link/secret checks contributors should run.

- [ ] **Step 2: Write `SECURITY.md`.**

  State that credentials must never be written to artifacts or documentation, exploration is non-production only, suspected exposed credentials should be rotated and reported privately, and security reports should not be filed publicly with sensitive details. Do not include an email address that is not already a verified project contact.

- [ ] **Step 3: Write the pull-request template.**

  Include checkboxes for preserving stage behavior, avoiding test-code generation, validating links/assets, checking secrets, and documenting any intentionally unsupported claim. Add a compact summary and verification section.

- [ ] **Step 4: Link guidance from the README.**

  Add contribution and security links in the appropriate sections and do not add a Code of Conduct file unless the repository later establishes a community policy.

- [ ] **Step 5: Commit the community files.**

  ```powershell
  git add CONTRIBUTING.md SECURITY.md .github/PULL_REQUEST_TEMPLATE.md
  git commit -m "docs: add contribution and security guidance"
  ```

### Task 4: Tighten repository hygiene and verify the makeover

**Files:**
- Modify: `.gitignore`
- Review: all changed files and `git diff`

**Interfaces:**
- Consumes: The local `.codebase-memory/` artifact generated during repository discovery and the final documentation/assets.
- Produces: A clean working tree with local graph artifacts excluded and evidence-backed verification output.

- [ ] **Step 1: Add the local graph artifact to `.gitignore`.**

  Add `.codebase-memory/` under a clearly labeled local/tooling section, preserving existing generated QA-output and editor-state rules.

- [ ] **Step 2: Run repository-wide factual and secret checks.**

  Run:

  ```powershell
  rg -n -i --hidden --glob '!**/.git/**' --glob '!**/.codebase-memory/**' '(api[_-]?key|secret|password|token|private[_-]?key|BEGIN [A-Z ]+PRIVATE KEY|postgres://|mongodb://)' .
  rg -n 'TBD|TODO|fake|placeholder|coming soon|100%|coverage|users|downloads|performance' README.md docs CONTRIBUTING.md SECURITY.md
  ```

  Expected: only intentionally generic examples such as `<password>` in setup documentation appear; no real credential or unsupported metric is present.

- [ ] **Step 3: Validate links and referenced paths.**

  Run:

  ```powershell
  rg -o '\[[^]]+\]\([^)]*\)' README.md docs CONTRIBUTING.md SECURITY.md
  rg --files -uu | Sort-Object
  git diff --check
  ```

  Manually compare each local Markdown link with the file tree and confirm external links are intentional GitHub/Playwright references.

- [ ] **Step 4: Review the complete diff for scope and claims.**

  Run:

  ```powershell
  git diff --stat HEAD~4..HEAD
  git diff --name-status HEAD~4..HEAD
  git status --short --ignored
  ```

  Expected: only README/docs/community/hygiene files changed; QA rules, prompts, skills, MCP configuration, and license remain behaviorally untouched.

- [ ] **Step 5: Remove the discovery artifact if it is still present as an untracked directory.**

  After confirming `.codebase-memory/` contains only the local index created during this task and is ignored by `.gitignore`, remove that exact directory from the working tree so the repository hand-off is clean.

- [ ] **Step 6: Commit the hygiene change and report final metadata recommendations.**

  ```powershell
  git add .gitignore
  git commit -m "chore: ignore local graph artifacts"
  git status --short
  ```

  Report the recommended GitHub description, 8–15 accurate topics, the social-preview asset path, and the absence of a verified demo URL. Do not claim that GitHub settings were updated.

## Self-review checklist

- [x] All approved design requirements map to a task above.
- [x] No package, runtime, Docker, CI, database, or test-suite work is introduced.
- [x] No task requires authoring Playwright test code.
- [x] All verification commands are concrete and applicable to this documentation-only repository.
- [x] The README, architecture document, and community files have distinct responsibilities.

