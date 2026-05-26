---
name: architect-playbook-audit
description: Consolidated Architect Playbook repository audits using Ben's TypeScript and React audit baselines. Use when Codex is asked to run the Architect Playbook, audit a repository across architecture, security, dependencies, performance, accessibility, testing, React, TypeScript, linting, build health, documentation, quality gates, error handling, or agentic instruction files, and produce one interactive HTML dashboard instead of separate terminal reports.
---

# Architect Playbook Audit

Run Ben's Architect Playbook as one consolidated repository audit. The individual playbook skills live under `assets/subskills/` as bundled assets; this skill is the single orchestrator that chooses the relevant assets, applies their checks, and writes one dashboard.

Default output:

- `.architect-playbook-audit/consolidated-findings.json`
- `.architect-playbook-audit/consolidated-findings.md`
- `architect-playbook-dashboard.html` in the audited repository root

## Core Rules

- Stay read-only unless the user explicitly asks for a follow-up fix. This audit must not change target source files.
- Prefer static analysis. Run optional commands only when the user explicitly requests enrichment such as `--with-run`, `--with-network`, `--with-scan`, `--with-stats`, `--with-link-check`, or `--with-lighthouse-results`.
- Do not install dependencies during the audit. If a tool is missing, record the related audit as `blocked` or the check as `missing`, then continue.
- Do not dump all findings into chat. Print a concise summary and point to the dashboard and JSON/Markdown artifacts.
- Always generate the dashboard, even when no findings are present or some audits are skipped.
- Treat the copied subskill files as the source of truth for each audit's baseline. Cite the asset path and line number for every playbook-derived finding.

## Resource Map

- `assets/audit-manifest.json`: audit inventory, categories, applicability, and asset paths.
- `assets/subskills/<audit-name>/SKILL.md`: copied Architect Playbook subskill content.
- `assets/playbook-docs/`: copied playbook README, architecture notes, and project rules.
- `assets/dashboard-template.html`: self-contained dashboard shell.

Read `assets/audit-manifest.json` first. Then load only the subskill files that apply to the target repository or that the user explicitly requested.

## Workflow

1. Discover the repository.
   - Inventory files recursively.
   - Ignore `.git/`, `node_modules/`, dependency caches, generated build output, coverage output, `.next/`, `dist/`, `build/`, `target/`, and vendored folders unless the user explicitly includes them.
   - Detect package manager, framework, TypeScript, React, test runner, linter, bundler, CI, documentation, agentic instruction files, dependency manifests, security headers/configuration, and existing `.architect-audits/` or `graphify-out/` artifacts.

2. Select audits.
   - If the user asks for "all", "full", "complete", or "Architect Playbook", run every applicable audit from the manifest.
   - If the repository is not TypeScript/React, still run general audits that apply: `quality-gates-audit`, `dependency-audit` when Node manifests exist, `documentation-audit`, `agentic-audit`, and any explicitly requested audit.
   - Mark an audit `skipped` when its required project shape is absent.
   - Mark an audit `blocked` when a required artifact is absent but the audit should apply, for example `architecture-audit` when Graphify output is required by the source subskill and no graph exists.

3. Load selected subskills.
   - Read each selected `assets/subskills/<audit-name>/SKILL.md`.
   - Apply its layer model, status taxonomy, detection signals, thresholds, "what this skill does", idempotency rules, and explicit exclusions.
   - Preserve the playbook distinction between `present`, `partial`, `missing`, and `violation`.
   - Use `blocked` and `skipped` only at the consolidated orchestration layer.

4. Run the checks.
   - Resolve Layer 0 diagnostic snapshots where the source audit defines them.
   - Resolve each layer/check from the selected subskills.
   - Record representative evidence with tight file and line references.
   - Avoid hallucinated findings. A finding needs an observed file/configuration/tooling signal and a cited subskill source line.
   - Deduplicate overlapping findings by canonical root cause while preserving all affected audits in `alsoImpacts`.

5. Score and prioritize.
   - Map `violation` to `HIGH` by default, or `CRITICAL` for security exposure, hardcoded secrets, auth/session failure, dependency vulnerabilities with known high impact, or broken release gates.
   - Map `missing` to `HIGH` when a required safety net is absent, otherwise `MEDIUM`.
   - Map `partial` to `MEDIUM` or `LOW` based on blast radius.
   - Map `present` to no finding.
   - Generate the Top 5 Highest-Leverage Recommendations by risk reduction, not by audit order.

6. Write artifacts.
   - Create `.architect-playbook-audit/` if needed.
   - Write `consolidated-findings.json` using the data contract below.
   - Write `consolidated-findings.md` with the snapshot, audit matrix, findings grouped by audit/layer, and remediation order.
   - Copy `assets/dashboard-template.html` to `architect-playbook-dashboard.html` and replace the `architectData` object with the real JSON data.

7. Reply concisely.
   - Include total audits run, skipped/blocked count, total findings, critical/high count, and the dashboard path.
   - Include only the Top 5 recommendations in chat.

## Data Contract

Populate the dashboard with this shape:

```json
{
  "repository": "repo-name",
  "scanDate": "YYYY-MM-DD",
  "generatedBy": "architect-playbook-audit",
  "mode": "static",
  "summary": ["Three to five executive summary sentences."],
  "totals": {
    "auditsRun": 0,
    "auditsSkipped": 0,
    "auditsBlocked": 0,
    "checks": 0,
    "present": 0,
    "partial": 0,
    "missing": 0,
    "violation": 0,
    "findings": 0,
    "criticalHigh": 0
  },
  "audits": [
    {
      "id": "security-audit",
      "name": "Security",
      "category": "Risk",
      "status": "violation",
      "source": "assets/subskills/security-audit/SKILL.md",
      "summary": "Short result.",
      "counts": { "present": 0, "partial": 0, "missing": 0, "violation": 0 },
      "layers": [
        { "name": "Layer 1 - Authentication", "status": "partial", "counts": { "present": 0, "partial": 0, "missing": 0, "violation": 0 } }
      ],
      "artifacts": []
    }
  ],
  "findings": [
    {
      "id": "SEC-001",
      "auditId": "security-audit",
      "auditName": "Security",
      "category": "Risk",
      "layer": "Layer 2 - Input handling and XSS prevention",
      "check": "Unsafe HTML injection",
      "status": "violation",
      "severity": "HIGH",
      "title": "Unsafe HTML rendering bypasses sanitization",
      "file": "src/App.tsx",
      "line": "42",
      "evidence": "Tight snippet or configuration signal.",
      "why": "Why this matters.",
      "fix": "Smallest useful fix.",
      "source": "assets/subskills/security-audit/SKILL.md:101",
      "alsoImpacts": ["react-audit", "testing-audit"]
    }
  ],
  "recommendations": [
    {
      "title": "Fix unsafe HTML rendering",
      "why": "Risk statement.",
      "smallestFix": "Immediate fix.",
      "audits": ["security-audit", "react-audit"],
      "severity": "HIGH"
    }
  ],
  "snapshots": [
    { "auditId": "typescript-audit", "title": "TypeScript snapshot", "items": ["strict: true"] }
  ]
}
```

## Audit Selection Guide

- Always consider: `quality-gates-audit`, `documentation-audit`, `agentic-audit`.
- Node manifests: `dependency-audit`, `bundle-build-audit`, `security-audit`.
- TypeScript files or `tsconfig.json`: `typescript-audit`, `linting-audit`, `testing-audit`, `error-handling-audit`.
- React dependency, JSX, TSX, hooks, or component files: `react-audit`, `accessibility-audit`, `performance-audit`.
- Graphify output or explicit architecture request: `architecture-audit`.
- Existing build stats, Lighthouse JSON, linter/test/tsc output, or explicit enrichment flags: load the matching audit and include enrichment in `mode`.

## Dashboard Requirements

Use `assets/dashboard-template.html` as the report shell.

- Keep the dashboard self-contained: inline CSS, inline JavaScript, no CDN, no remote fonts, no network assets.
- Preserve filtering by audit, category, status, and severity.
- Preserve expandable findings, audit matrix, status composition, recommendation list, snapshots, and skipped/blocked audit sections.
- Escape all inserted values before writing HTML/JavaScript.
- Keep source citations visible for every finding.

## Boundaries

- Do not run the original playbook installer subskills as part of a consolidated audit.
- Do not run `system-self-improve` from this skill. Mention it only when the user asks to evolve the playbook itself.
- Do not claim compliance certification. This produces engineering audit findings and recommendations.
- Do not replace human accessibility, security, or architecture review when the subskill explicitly says human judgment is required.
