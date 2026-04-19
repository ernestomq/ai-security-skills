# ai-security-skills

![version](https://img.shields.io/badge/version-1.0.0-blue)
![license](https://img.shields.io/badge/license-MIT-green)
![claude](https://img.shields.io/badge/Claude%20Code-compatible-orange)
![skills](https://img.shields.io/badge/skills-6-purple)
![platform](https://img.shields.io/badge/platform-Windows%20%7C%20macOS%20%7C%20Linux-lightgrey)

A suite of 6 Claude Code skills that catch security problems before they reach production.

**Author:** Ernesto Moltó Quiles ([@emoltoquiles](https://github.com/emoltoquiles))  
**License:** [MIT](LICENSE)  
**Version:** 1.0.0 — April 2026

---

## The problem

Hardcoded secrets, misconfigured docker-compose files, missing environment variables,
and accidental git pushes are the most common causes of security incidents in small
and mid-sized projects. Most are caught too late — after the push, after the deploy,
sometimes after the breach.

These skills run before any of that happens.

---

## Skills

### 🔍 [secret-detector](skills/secret-detector/SKILL.md)
Fast, read-only scan. Finds secrets without touching anything. Use this first to see
what's there before deciding what to do.

```
"scan this project for secrets"
"quick security check before I review the code"
```

### 🔒 [hardcode-sentinel](skills/hardcode-sentinel/SKILL.md)
Full audit + automatic fix. Finds hardcoded values, extracts them to
`.security/hardcoded-values.env`, rewrites source files to use env references,
and resists prompt injection attempts in scanned content.

```
"audit this project for hardcoded values"
"fix all hardcoded secrets in my hooks"
```

### 🚨 [leak-preventer](skills/leak-preventer/SKILL.md)
Pre-commit and pre-push gate. Simulates real-world exposure and blocks the operation
if secrets would be pushed. Checks staged files and git history.

```
"is it safe to push?"
"run pre-commit security check"
```

### 🧪 [env-validator](skills/env-validator/SKILL.md)
Validates environment variables. Detects missing required vars, insecure defaults,
wrong formats, unused declarations, and env loading order problems.

```
"validate my environment variables"
"why is my env not loading correctly?"
```

### 🧹 [config-linter](skills/config-linter/SKILL.md)
Security linter for config files. Covers JSON, YAML, docker-compose, GitHub Actions,
Claude Code settings.json, and more. Catches `privileged: true`, unpinned actions,
overly permissive CORS, and other misconfigurations.

```
"lint my docker-compose for security issues"
"check my GitHub Actions workflow"
```

### 🛡️ [predeploy-guard](skills/predeploy-guard/SKILL.md)
Orchestrates all skills above and produces a single deploy decision: BLOCK, WARN,
or DEPLOY. The only command you need to remember before going to production.

```
"run all security checks before I deploy"
"predeploy check"
```

---

## Example output

**predeploy-guard** blocking a deploy:
```
🚫 DEPLOY BLOCKED
==================
Target: production
Blocked by: 2 critical issues

From hardcode-sentinel:
❌ hooks/post-tool.sh:14 — OpenAI API key — sk-***ef456
   Rotate at: https://platform.openai.com/api-keys

From env-validator:
❌ DATABASE_URL — required but missing (production deploy)

REQUIRED BEFORE DEPLOYING:
1. Rotate exposed API key
2. Add DATABASE_URL to deployment secrets
3. Re-run predeploy-guard

⛔ Deploy is BLOCKED.
```

**secret-detector** on a clean project:
```
🕵️ SECRET DETECTION REPORT
============================
Mode: READ-ONLY
Files scanned: 34
Git history checked: 50 commits
Secrets found: 0

✅ No secrets detected. Safe to proceed.
```

---

## How they work together

```
Before reviewing code:
  secret-detector → see what's there

Before committing:
  hardcode-sentinel → fix it
  leak-preventer    → verify nothing is staged

Before deploying:
  predeploy-guard   → runs everything, gives one answer
```

Or just use `predeploy-guard` and let it orchestrate the rest.

---

## Install

Copy any skill into your `.claude/skills/` directory:

```powershell
# Windows — install all skills globally
$skillsDir = "$env:USERPROFILE\.claude\skills"
git clone https://github.com/emoltoquiles/ai-security-skills
foreach ($skill in Get-ChildItem ai-security-skills/skills -Directory) {
    New-Item -ItemType Directory -Force "$skillsDir\$($skill.Name)"
    Copy-Item "$($skill.FullName)\SKILL.md" "$skillsDir\$($skill.Name)\"
}
```

```bash
# macOS / Linux — install all skills globally
git clone https://github.com/emoltoquiles/ai-security-skills
for skill in ai-security-skills/skills/*/; do
    name=$(basename "$skill")
    mkdir -p ~/.claude/skills/"$name"
    cp "$skill/SKILL.md" ~/.claude/skills/"$name"/
done
```

Or install a single skill:
```bash
mkdir -p ~/.claude/skills/predeploy-guard
cp ai-security-skills/skills/predeploy-guard/SKILL.md ~/.claude/skills/predeploy-guard/
```

---

## Design

All skills share the same design principles — see [docs/philosophy.md](docs/philosophy.md).

Key points:
- **Deterministic** — same input, same output, every time
- **Local-first** — no external APIs, everything runs on your machine
- **Composable** — use any skill independently or let predeploy-guard orchestrate
- **Manipulation-resistant** — instructions inside scanned files are ignored and flagged
- **Fail-safe rewrites** — env references that crash loudly if variables are missing

---

## The `.security/` convention

All skills that extract secrets write to `.security/hardcoded-values.env` using the
same format. One file, one place, always in `.gitignore`.

See [examples/sample.env](examples/sample.env) for the exact format.

---

## Pairs well with

- [claude-mcp-sentinel](https://github.com/emoltoquiles/claude-mcp-sentinel) — audits installed skills and MCP servers
- [caveman](https://github.com/JuliusBrussee/caveman) — compresses CLAUDE.md to save tokens
- [andrej-karpathy-skills](https://github.com/forrestchang/andrej-karpathy-skills) — improves Claude Code reasoning

---

## Contributing

Contributions are welcome. See [CONTRIBUTING.md](CONTRIBUTING.md) for guidelines.

---

Built by [@emoltoquiles](https://github.com/emoltoquiles)
