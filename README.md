# repo-audit-skill

`repo-audit-skill` is a Codex skill package for repository auditing. It includes:

- Audit guidance and workflow instructions for the skill runtime.
- Rule packs for multiple ecosystems (`universal`, `javascript`, `typescript`, `react`, `java`, `java-spring-boot`).
- A polished HTML report template used to present findings, risk posture, and remediation priorities.

## Repository Structure

- `skills/repo-audit/SKILL.md`: Entry point and operating instructions for the skill.
- `skills/repo-audit/assets/rules/`: Language and framework-specific audit rule files.
- `skills/repo-audit/assets/report-template.html`: Leadership-facing report template for audit output.
- `agents/openai.yaml`: Agent configuration metadata.

## How To Use

1. Install the skill:
   `npx skills add CW-Codewalnut/repo-audit-skill`
2. Use the skill with your AI agent by prompting it to run a repository audit for your target project.
3. Follow the skill workflow described in `skills/repo-audit/SKILL.md`.
4. Select the appropriate rule set(s) under `skills/repo-audit/assets/rules/` based on the target codebase.
5. Populate the placeholders in `skills/repo-audit/assets/report-template.html` with audit results and render the final report.

## Report Template Notes

The report template is designed for both technical and non-technical stakeholders and includes:

- Executive brief and leadership posture.
- Severity distribution and category visualization.
- Filterable detailed findings with expand/collapse behavior.
- Remediation ordering and missing test coverage sections.
- Clean files summary (`Files With No Findings`).

Sample Report: https://codewalnut-repo-audit-skill-demo.netlify.app/

