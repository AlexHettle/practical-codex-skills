# Practical Codex Skills

A growing collection of practical Codex skills for repository analysis, accessibility auditing, codebase hygiene, leak detection, and test improvement.

These skills are designed to be small, focused, and easy to drop into a Codex setup. I will keep adding more skills over time.

## Skills

| Skill | Use it when you want Codex to... | Best for |
| --- | --- | --- |
| [`accessibility-report`](./accessibility-report/SKILL.md) | Audit a project and produce an evidence-based accessibility report. | WCAG-focused accessibility reviews, keyboard and screen reader risk assessment, semantic HTML/ARIA checks, and remediation planning. |
| [`get-architecture`](./get-architecture/SKILL.md) | Inspect a repository and produce a polished architecture report. | Onboarding to unfamiliar codebases, mapping entrypoints, understanding data flow, and explaining build/test/deployment structure. |
| [`improve-hygiene`](./improve-hygiene/SKILL.md) | Find and implement one low-risk codebase hygiene improvement. | Removing stale or confusing maintenance surface such as dead code, unused dependencies, obsolete docs, or misleading comments. |
| [`improve-testing`](./improve-testing/SKILL.md) | Improve the quality of existing tests. | Strengthening weak assertions, reducing brittleness, clarifying fixtures, improving async cleanup, and making existing tests more meaningful. |
| [`increase-testing-coverage`](./increase-testing-coverage/SKILL.md) | Add a focused test coverage improvement. | Covering meaningful untested behavior, edge cases, public helpers, branches, regressions, or small integration paths. |
| [`leak-search`](./leak-search/SKILL.md) | Scan a project tree for secrets and sensitive values before they are committed or shared. | Finding API keys, private keys, credentials, unsafe env files, gitignore gaps, and other tracked or unignored leaks. |

## Quick Start

Codex does not load skills directly from GitHub just because a repo exists. Users need to install or copy the skill folders into their local Codex skills directory, then restart Codex so the new skill metadata is picked up.

If you already use Codex, the easiest option is to ask Codex to install the skills from this GitHub repository:

```text
Use $skill-installer to install skills from AlexHettle/practical-codex-skills:
accessibility-report
get-architecture
improve-hygiene
improve-testing
increase-testing-coverage
leak-search
```

You can also install them manually by copying the folders from a cloned or downloaded copy of this repo.

Default locations:

- macOS/Linux: `~/.codex/skills`
- Windows: `%USERPROFILE%\.codex\skills`

Install all skills on macOS/Linux:

```bash
mkdir -p ~/.codex/skills
cp -R accessibility-report get-architecture improve-hygiene improve-testing increase-testing-coverage leak-search ~/.codex/skills/
```

Install all skills on Windows PowerShell:

```powershell
New-Item -ItemType Directory -Force "$env:USERPROFILE\.codex\skills"
Copy-Item -Recurse -Force .\accessibility-report,.\get-architecture,.\improve-hygiene,.\improve-testing,.\increase-testing-coverage,.\leak-search "$env:USERPROFILE\.codex\skills\"
```

Install only one skill:

```bash
cp -R improve-testing ~/.codex/skills/
```

After installing, restart Codex so the skill metadata is loaded.

## How To Use

You can ask Codex naturally, or invoke a skill directly by name.

Examples:

```text
Use $accessibility-report to audit this project for accessibility issues.
Use $get-architecture to explain this repository.
Use $improve-hygiene to find and fix a quick codebase hygiene win.
Use $improve-testing to improve one existing test in this project.
Use $increase-testing-coverage to add a meaningful coverage improvement.
Use $leak-search to scan this repository for secrets before I commit.
```

## Skill Details

### accessibility-report

`accessibility-report` is a read-only audit skill for producing detailed accessibility reports. It guides Codex to inspect project structure, accessibility tooling, source patterns, and runtime evidence when available, then organize findings by severity, user impact, WCAG references, evidence, and remediation guidance.

Use it when you need a project-specific accessibility review, WCAG-oriented report, keyboard and screen reader risk assessment, or prioritized accessibility remediation plan.

### get-architecture

`get-architecture` is a read-only repo analysis skill. It instructs Codex to inspect the project structure, identify the stack, map entrypoints, explain data and control flow, call out configuration and deployment signals, and produce a structured architecture report grounded in file evidence.

Use it when you are joining a new codebase, handing a repo to someone else, preparing technical documentation, or trying to understand where important behavior lives.

### improve-hygiene

`improve-hygiene` guides Codex to make one small, evidence-backed cleanup. It is intentionally conservative: inspect first, confirm the cleanup is real, edit narrowly, verify with the most relevant command, and report an accurate commit message.

Use it when a repo needs a quick maintenance win without a broad refactor.

### improve-testing

`improve-testing` focuses on the quality of tests that already exist. It pushes Codex toward better assertions, clearer test names, less brittle snapshots, improved fixtures, and more trustworthy behavior checks.

Use it when tests exist but feel weak, noisy, hard to maintain, or too tied to implementation details.

### increase-testing-coverage

`increase-testing-coverage` focuses on adding meaningful coverage where behavior is currently unprotected. It emphasizes practical coverage wins such as edge cases, public APIs, fallback logic, input validation, error handling, and regression risks.

Use it when you want more behavior protected by tests, not just a higher number.

### leak-search

`leak-search` scans tracked and unignored project files for secrets, credentials, private keys, unsafe environment files, and other sensitive values that should not be committed or shared. It guides Codex to redact evidence, distinguish likely placeholders from real leaks, and recommend concrete remediation steps.

Reports should include only redacted evidence, not complete secret values.

Use it before committing, publishing, sharing a repository, or handing off code that may contain local credentials or generated secret-bearing files.

## Repository Structure

Each skill lives in its own top-level folder:

```text
.
+-- README.md
+-- LICENSE
+-- .gitignore
+-- accessibility-report/
|   +-- SKILL.md
|   +-- agents/
|       +-- openai.yaml
+-- get-architecture/
|   +-- SKILL.md
|   +-- agents/
|       +-- openai.yaml
+-- improve-hygiene/
|   +-- SKILL.md
|   +-- agents/
|       +-- openai.yaml
+-- improve-testing/
|   +-- SKILL.md
|   +-- agents/
|       +-- openai.yaml
+-- increase-testing-coverage/
|   +-- SKILL.md
|   +-- agents/
|       +-- openai.yaml
+-- leak-search/
    +-- SKILL.md
    +-- agents/
        +-- openai.yaml
```

The important files are:

- `SKILL.md`: The required skill definition. Codex uses the YAML frontmatter to decide when the skill applies, then reads the body for workflow guidance.
- `agents/openai.yaml`: UI metadata for display names, short descriptions, and default prompts.

This repo keeps documentation at the repository root so each skill folder stays lightweight and easy for Codex to load.

## Updating Skills

To update your installed copy after pulling changes from GitHub, copy the changed skill folders back into your Codex skills directory.

macOS/Linux:

```bash
cp -R accessibility-report get-architecture improve-hygiene improve-testing increase-testing-coverage leak-search ~/.codex/skills/
```

Windows PowerShell:

```powershell
Copy-Item -Recurse -Force .\accessibility-report,.\get-architecture,.\improve-hygiene,.\improve-testing,.\increase-testing-coverage,.\leak-search "$env:USERPROFILE\.codex\skills\"
```

Restart Codex after updating so Codex sees the latest skill metadata.

## Notes For Contributors

New skills should follow the same pattern:

1. Use a lowercase, hyphenated folder name.
2. Put the required skill definition in `SKILL.md`.
3. Keep the `name` in `SKILL.md` identical to the folder name.
4. Include a clear `description` that explains both what the skill does and when Codex should use it.
5. Add `agents/openai.yaml` with a display name, short description, and default prompt.
6. Keep extra resources in standard folders only when needed: `scripts/`, `references/`, or `assets/`.
7. Avoid extra documentation inside individual skill folders; keep human-facing docs in this root README.

## Roadmap

This repository will grow over time. Expect new skills to appear as more repeatable Codex workflows become useful enough to package.

## License

MIT. See [LICENSE](./LICENSE).
