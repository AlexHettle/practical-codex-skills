---
name: leak-search
description: Search a project tree for secrets, credentials, tokens, private keys, environment leaks, unsafe committed files, and other sensitive values that should not be committed. Use when Codex needs to scan for leaks, secrets, API keys, credentials, exposed env vars, unsafe committed files, gitignore gaps, or anything sensitive before committing, sharing, or publishing a repository.
---

# Leak Search

Find sensitive data in files that are tracked by git or not excluded by git ignore rules. Treat all findings as sensitive and never print complete secret values.

## Core Workflow

1. Locate the git repository root:

   ```bash
   git rev-parse --show-toplevel
   ```

2. Build the candidate file list from tracked files and untracked files that are not ignored:

   ```bash
   git ls-files -z --cached --others --exclude-standard
   ```

   This includes tracked files and untracked files that are not ignored by `.gitignore`, `.git/info/exclude`, or global git excludes.

3. Search only those candidate files. Do not scan ignored files unless the user explicitly asks.

4. Prefer fast local tools such as `rg`. If null-delimited candidate handling or shell quoting is awkward on the current platform, write a short temporary script that reads the `git ls-files -z` output and inspects text files safely.

5. For each likely leak, verify whether the file is tracked or unignored, decide whether the value looks real or a placeholder, and mark uncertain values as possible leaks instead of overstating confidence.

6. Report each finding with file path, line number, leak type, redacted evidence, and a concrete recommended fix.

## What To Look For

Search for high-risk patterns including:

- API keys and bearer tokens
- Cloud credentials for AWS, Azure, Google Cloud, Firebase, Supabase, Vercel, Netlify, Stripe, Twilio, SendGrid, GitHub, OpenAI, Anthropic, Slack, Discord, and similar services
- Private keys and certificates
- Passwords, database URLs, connection strings, and basic-auth URLs
- `.env` files, local config files, credential exports, shell history, copied deployment secrets, and generated logs
- Hardcoded secrets in source, tests, notebooks, JSON, YAML, TOML, XML, shell scripts, Docker files, CI configs, and docs
- Files that should usually be ignored, such as `.env`, `.env.local`, `*.pem`, `*.key`, service account JSON, local SQLite databases, credential dumps, and logs

Use patterns like these as a starting point, then add repo-specific patterns when the project suggests them:

```bash
rg -n --hidden --no-ignore-vcs --binary=false "AKIA[0-9A-Z]{16}|-----BEGIN (RSA |EC |OPENSSH |DSA |PRIVATE )?PRIVATE KEY-----|xox[baprs]-[A-Za-z0-9-]+|gh[pousr]_[A-Za-z0-9_]{20,}|sk-[A-Za-z0-9_-]{20,}|Bearer [A-Za-z0-9._~+/=-]{20,}|password\s*[:=]|api[_-]?key\s*[:=]|secret\s*[:=]|token\s*[:=]|DATABASE_URL|PRIVATE_KEY"
```

Run searches against the `git ls-files` candidate set rather than the whole directory.

## Redaction Rules

Never reveal a complete secret. Redact most of each value and show only enough context to identify the issue.

Use redaction like:

- `sk-proj-abc...xyz`
- `AKIA1234...7890`
- `postgres://user:***@host/db`

For private keys, report only the marker and location, not the body.

## Verification And Remediation

For each likely leak:

- Check whether the file is tracked or unignored.
- Decide whether the value is probably real or a placeholder.
- Recommend moving the value to an environment variable or secret manager.
- Recommend adding the file pattern to `.gitignore` when appropriate.
- Recommend rotating the credential if it may have been committed or shared.
- Recommend removing the secret from git history when necessary.

## Output Format

Return findings grouped by severity:

```text
High
- path/to/file:12 - OpenAI API key - sk-proj-abc...xyz
  Fix: Remove from source, rotate the key, and load it from an environment variable.

Medium
- path/to/config.yml:8 - Possible password field - p***d
  Fix: Confirm whether this is a real credential; move it out of config if so.

Gitignore Recommendations
- Add `.env.local`
- Add `*.pem`

Summary
- Scanned 142 tracked/unignored files.
- Found 1 high-confidence leak and 1 possible leak.
```

If there are no findings, use:

```text
No obvious leaks found in tracked or unignored files.

Scanned 142 files. This was a heuristic scan, so it may not catch every secret format.
```
