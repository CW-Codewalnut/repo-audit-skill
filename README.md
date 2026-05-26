# repo-audit-skill

`repo-audit-skill` is a Codex skill package for repository auditing. It now includes two audit skills:

- `repo-audit`: the original repository audit skill with language and framework rule packs.
- `architect-playbook-audit`: a consolidated Ben's Architect Playbook audit skill that loads playbook subskills from assets and emits one interactive dashboard.

## Repository Structure

- `skills/repo-audit/SKILL.md`: Entry point and operating instructions for the original skill.
- `skills/repo-audit/assets/rules/`: Language and framework-specific audit rule files.
- `skills/repo-audit/assets/report-template.html`: Leadership-facing report template for audit output.
- `skills/architect-playbook-audit/SKILL.md`: Single orchestrator for the consolidated Architect Playbook audit.
- `skills/architect-playbook-audit/assets/subskills/`: Ben's individual Architect Playbook skill bodies stored as assets.
- `skills/architect-playbook-audit/assets/audit-manifest.json`: Audit inventory, applicability rules, and source paths.
- `skills/architect-playbook-audit/assets/dashboard-template.html`: Consolidated Architect Playbook dashboard template.
- `agents/openai.yaml`: Agent configuration metadata.

## How To Use

1. Install the skill:
   `npx skills add CW-Codewalnut/repo-audit-skill`
2. Use `$repo-audit` for the original broad repository audit, or `$architect-playbook-audit` for the consolidated Architect Playbook suite.
3. Follow the workflow described in the relevant `SKILL.md`.
4. For `repo-audit`, select the appropriate rule set(s) under `skills/repo-audit/assets/rules/`.
5. For `architect-playbook-audit`, load `assets/audit-manifest.json`, then load only the applicable subskills under `assets/subskills/`.
6. Populate the matching HTML template with audit results and render the final report.

## Report Template Notes

The original `repo-audit` report template is designed for both technical and non-technical stakeholders and includes:

- Executive brief and leadership posture.
- Severity distribution and category visualization.
- Filterable detailed findings with expand/collapse behavior.
- Remediation ordering and missing test coverage sections.
- Clean files summary (`Files With No Findings`).

The `architect-playbook-audit` dashboard adds:

- Audit matrix across Ship, Risk, Experience, Code, and Knowledge categories.
- Consolidated finding filters by audit, category, status, and severity.
- Skipped and blocked audit visibility for missing prerequisites.
- Diagnostic snapshots and cross-audit remediation order.

Local sample: `samples/architect-playbook-audit-sample.html`

Sample Report: https://codewalnut-repo-audit-skill-demo.netlify.app/

