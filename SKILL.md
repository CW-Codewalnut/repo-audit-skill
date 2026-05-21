---
name: repo-audit
description: Full-repository code quality and security audits. Use when Codex is asked to scan, review, audit, or report issues across a repository, especially Java, Spring Boot, JavaScript, TypeScript, React, config files, secrets, architecture, dependency hygiene, and test coverage. Generates an HTML audit report with findings tied to rule source files and line numbers.
---

# Repository Audit

Use this skill to perform a complete repository code-quality and security audit and save the result as `report.html` in the repository root.

The language and framework rules are bundled as rule assets under `assets/rules/`. They are not standalone skills. Read only the assets that apply to the repository, then apply every loaded asset as an opinionated default alongside repository-local guidance and general best practices.

## Workflow

1. Discover the repository before scanning files.
2. Read repository-local guidance before scanning files.
3. Load every applicable rule asset before scanning any file.
4. Read every in-scope file fully; do not skip or truncate files.
5. Apply loaded asset rules, repository-local guidance, general best practices, and this orchestrator's special scans.
6. Record one finding per violation with source file and source line.
7. Save the final interactive HTML report as `report.html` in the repository root.

## Discovery

Build a full inventory of the repository before loading rule assets.

- List files recursively.
- Ignore `.git/`, `node_modules/`, `build/`, `dist/`, `target/`, `.gradle/`, coverage output, and generated dependency folders.
- Group files by type:
  - `*.java`: Java rules.
  - `*.ts` and `*.tsx`: JavaScript plus TypeScript rules.
  - `*.js` and `*.jsx`: JavaScript rules.
  - `*.jsx`, `*.tsx`, React component files, or files using JSX/hooks: React rules.
  - `*.test.*`, `*.spec.*`, `*Test.java`, and `*Spec.*`: test files. Audit them too.
  - `application.yml`, `application.yaml`, `application.properties`, `.env`, `*.json`: config and secrets audit.
  - `pom.xml`, `build.gradle`, `build.gradle.kts`, `package.json`, lockfiles: dependency audit.
- Detect project types from file contents, not filenames alone:
  - Plain Java: Java source without Spring Boot markers.
  - Spring Boot REST/API: `@SpringBootApplication`, `@RestController`, Spring Boot dependencies, `application.yml`, or `application.properties`.
  - JavaScript: `.js` or `.jsx` source.
  - TypeScript: `.ts` or `.tsx` source.
  - React: JSX, TSX, hooks, component files, or React dependencies.

## Repository Guidance

Before applying opinionated rule assets, search for and read repository-local guidance files such as `AGENTS.md`, `CLAUDE.md`, `.cursorrules`, `.cursor/rules/*`, `.github/copilot-instructions.md`, `CONTRIBUTING.md`, project README guidance, architecture decision records, lint configs, formatter configs, and framework-specific style docs.

- Treat repository-local guidance as the project's source of truth for team conventions.
- If a loaded asset rule conflicts with repository-local guidance, follow the repository-local guidance and do not report the asset rule as a violation.
- If repository-local guidance is stricter than the assets, apply the stricter repository-local rule.
- If repository-local guidance is silent, use the loaded assets and general best practices.
- Include the repository guidance source path and line number in the finding when a finding depends on that guidance.

## Rule Assets

Load applicable assets from this table. A single file can trigger multiple assets.

| Rule asset | When to load |
| --- | --- |
| `assets/rules/universal.md` | Always. Applies to every source and test file. |
| `assets/rules/javascript.md` | Any `.js`, `.jsx`, `.ts`, or `.tsx` file. |
| `assets/rules/typescript.md` | Any `.ts` or `.tsx` file. |
| `assets/rules/react.md` | Any `.jsx`, `.tsx`, React component, hook, or JSX-using file. |
| `assets/rules/java.md` | Any `.java` file. |
| `assets/rules/java-spring-boot.md` | Spring Boot REST/API projects and Spring Boot Java files. |

Examples:

- A `.tsx` React component loads `universal.md`, `javascript.md`, `typescript.md`, and `react.md`.
- A Spring Boot controller loads `universal.md`, `java.md`, and `java-spring-boot.md`.
- A plain Java class loads `universal.md` and `java.md`.

## Per-File Scan

For each in-scope file:

- Read the full file content.
- Apply every checklist item from every loaded rule asset for that file.
- Also apply broadly accepted code quality, security, maintainability, testing, accessibility, and performance best practices that are relevant to the repository.
- Record every violation separately.
- Use the rule ID and severity from the matching rule asset when the finding comes from an asset.
- For best-practice findings not covered by a loaded asset or repository-local rule, use a clear rule ID prefix such as `BP-SEC`, `BP-QUAL`, `BP-TEST`, `BP-PERF`, or `BP-A11Y`, and cite `SKILL.md:<line>` as the Skill Source.
- Do not report findings that cannot be tied to an actual code location and an explicit asset rule, repository-local rule, or best-practice rule.

Every finding must include:

- File path relative to the repository root.
- Line number or tight line range.
- Rule ID, such as `UNI-NAME-001`.
- Severity: `CRITICAL`, `HIGH`, `MEDIUM`, `LOW`, or `NITPICK`.
- Short title.
- Offending code snippet.
- Issue explanation.
- One-sentence fix guidance.
- Skill Source in the form `assets/rules/<asset>.md:<line>` or `SKILL.md:<line>` for orchestrator-only rules.

## Special Scans

Run these after the per-file scan.

### Secrets And Credentials

Search the entire repository for hardcoded secrets, credentials, and tokens.

- Search for `password=`, `api_key=`, `apiKey`, `secret=`, `token=`, `Bearer `, `jdbc:`, private keys, connection strings, and suspicious base64-like literals in config or source.
- Scan `.properties`, `.yml`, `.yaml`, `.env`, `.json`, `.java`, `.ts`, `.tsx`, `.js`, and `.jsx`.
- Mark hardcoded secrets as `CRITICAL`.

### Java And Spring Architecture

Run these only for Java or Spring Boot repositories.

- Detect controller-to-repository direct calls that skip the service layer.
- Detect service-to-controller imports or dependencies.
- Detect cross-service database joins, such as `@JoinColumn` referencing another service's tables.
- Detect missing global exception handling with `@RestControllerAdvice` in Spring Boot REST/API projects.

### Test Coverage Gaps

Check coverage by file inventory and code contents.

- For every service class, verify a corresponding `*Test.java`, `*.spec.ts`, or `*.test.ts` exists.
- For every REST controller, verify a MockMVC, supertest, or React Testing Library test exists as appropriate.
- Flag missing tests as `HIGH` severity.

### Config And Dependency Audit

Audit config and dependency manifests.

- In `application.yml`, `application.yaml`, and `application.properties`, flag hardcoded URLs, credentials, and missing timeout configuration.
- In `pom.xml`, `build.gradle`, `build.gradle.kts`, `package.json`, and lockfiles, flag known-vulnerable dependency versions only when identifiable from the manifest or available project tooling.

## Severity Definitions

| Severity | Meaning |
| --- | --- |
| `CRITICAL` | Security vulnerability, data leak, hardcoded secret, or auth bypass. |
| `HIGH` | Error swallowing, missing validation, broken error handling, missing critical tests, or N+1 query. |
| `MEDIUM` | Naming violations, single-responsibility breach, deep nesting, weak typing, or maintainability risk. |
| `LOW` | Performance hint, pagination/caching suggestion, minor production-readiness gap. |
| `NITPICK` | Style, import order, consistency, or small naming preference. |

## HTML Report

Generate a polished, color-coded, interactive HTML report at `report.html` in the repository root. Use charts, severity badges, summary cards, and expandable `<details>` sections where useful.

The report must contain:

1. Header dashboard:
   - Code Audit Report.
   - Repository name.
   - Scan date.
   - Files scanned.
   - Total findings.
2. Executive summary:
   - Three to five sentences summarizing the most important issues.
3. Findings by severity:
   - Counts for `CRITICAL`, `HIGH`, `MEDIUM`, `LOW`, and `NITPICK`.
   - Visual chart or progress-bar representation.
4. Detailed findings:
   - Expandable section per finding.
   - Summary header format: `[SEVERITY] RULE-ID - Short Title`.
   - File, line, formatted code snippet, issue, fix, and mandatory Skill Source.
5. Files with no findings:
   - List every scanned file that produced no finding.
6. Missing test coverage:
   - List untested services, controllers, and relevant frontend units.

If no issues are found, still generate `report.html` with zero counts and a complete list of clean files.

## Audit Rules

- Never skip an in-scope file.
- Never infer findings from filenames alone; read the actual content.
- Keep one finding per violation.
- Do not hallucinate findings. Report only evidence present in the repository.
- Treat secrets as `CRITICAL`.
- Apply all loaded rule assets.
- Apply repository-local guidance and general best practices in addition to loaded rule assets.
- Let repository-local guidance override conflicting asset rules because the assets are opinionated defaults.
- Treat config files as code.
- Prefer exact file and line citations over broad summaries.
- Cite the loaded asset path and line for every asset-derived rule, or cite the repository guidance path and line for every repo-guidance-derived rule.
