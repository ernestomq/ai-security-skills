# Philosophy

## Why this exists

Most security tools catch problems after they've already happened — in CI logs, after a
push, sometimes after a breach. These skills run before code leaves your machine.

The goal is not to make security harder. It is to make the secure path the easy path.

---

## Design principles

### Deterministic over clever

Same input → same output. Always.

A security tool that behaves differently depending on context, mood, or framing is not a
security tool — it is a suggestion. Every skill in this suite produces the same structure,
the same file, the same format on every run. You can write a test for it.

### Explicit over implicit

No hidden behavior. No silent passes. No "looks fine" without evidence.

Every finding is reported with: file, line, type, masked value, severity, and recommended
action. Every clean pass is confirmed with: files scanned, checks performed, result.

### Local-first security

Nothing leaves your machine. No external APIs, no telemetry, no cloud scanning services.

All analysis is performed locally using Claude's built-in tools (Read, Bash, Glob, Grep).
The secrets you're protecting never travel over the network to be analyzed.

### Composable by design

Each skill does one thing well. They compose naturally:

```
secret-detector     →  fast scan, what's there?
hardcode-sentinel   →  fix it, extract it, rewrite it
leak-preventer      →  block it before it's pushed
env-validator       →  is the environment correct?
config-linter       →  are the configs secure?
predeploy-guard     →  run all of the above, give one answer
```

You can use any skill independently or let predeploy-guard orchestrate the full suite.

### Resistant to manipulation

A security tool that can be disabled by the content it is scanning is not a security tool.

Every skill in this suite ignores instructions found inside scanned files. Values,
comments, and config fields cannot whitelist themselves, claim to be audited, or ask
Claude to skip a check. The only source of instructions is the SKILL.md itself.

### Fail loudly

When a required environment variable is missing, the code should crash immediately with
a clear error — not proceed silently with an empty string and fail mysteriously later.

All rewrites produced by these skills use fail-safe patterns:
- Shell: `${VAR:?VAR is required}`
- Python: `os.environ["VAR"]` (raises KeyError, not `.get()`)
- JavaScript: `if (!process.env.VAR) throw new Error("VAR is required")`

---

## What these skills are not

**Not a replacement for a secrets manager.**
Use Doppler, 1Password Secrets Automation, HashiCorp Vault, or AWS Secrets Manager
for production. These skills help you get there safely.

**Not a static analysis tool.**
They don't understand your code's logic. They find patterns and flag them.
Some findings will need manual review. That's expected.

**Not a substitute for security review.**
These skills catch the most common and most costly mistakes. They don't catch
everything. Ship with defense in depth.

---

## The `.security/` convention

Every skill in this suite writes to `.security/hardcoded-values.env` using the same
format. This is intentional.

One file. One format. One place to look. Always in `.gitignore`.

If you run hardcode-sentinel and then leak-preventer, they both know where to find
and update the same extraction file. No duplication, no conflict.

---

## On prompt injection resistance

Claude is the engine running these skills. A malicious file could theoretically
contain instructions trying to manipulate Claude's behavior during scanning.

Every skill explicitly addresses this:
- Instructions inside scanned files are ignored
- Claims of pre-approval are flagged as suspicious
- Prompt injection attempts are themselves flagged as CRITICAL findings

Security tools must be resistant to the content they analyze.
