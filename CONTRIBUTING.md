# Contributing to ai-security-skills

Thanks for your interest. This is a focused project — contributions that improve
detection, coverage, or reliability are welcome. Contributions that add complexity
without clear security benefit are not.

---

## What we're looking for

**Good contributions:**
- New detection patterns for credential types not currently covered
- False positive fixes — cases where the skill flags something it shouldn't
- False negative fixes — cases where a real secret slips through
- Improved rewrite patterns for languages or frameworks not covered
- Additional config file types for config-linter
- Better rotation guidance for credential types

**Not a good fit:**
- New skills outside the security auditing scope
- Style changes with no functional impact
- Dependency additions (these skills have zero dependencies by design)

---

## How to contribute

### 1. Open an issue first

Before writing anything, open an issue describing:
- What detection gap or bug you found
- A minimal example that reproduces it
- What the correct behavior should be

This avoids duplicate work and lets us agree on the approach before you invest time.

### 2. Fork and edit the relevant SKILL.md

Each skill is a single `SKILL.md` file. No build process, no compilation.
Edit the file, test it manually in Claude Code, and open a PR.

### 3. Test your change

Before submitting, verify:
- The pattern you added detects what it should
- It does NOT flag things it shouldn't (test with a clean project)
- The output format matches the existing report structure exactly
- The anti-manipulation rules still hold

### 4. PR description

Include in your PR:
- What credential type or pattern you added/fixed
- A before/after example showing the detection
- Confirmation that existing behavior is unaffected

---

## Reporting a false positive or false negative

Open an issue with:
- Which skill produced the incorrect result
- The file content (with any real secrets replaced by `EXAMPLE_VALUE`)
- What the skill reported vs what it should have reported

---

## Design constraints — read before contributing

These are non-negotiable. PRs that violate them will not be merged.

**No external dependencies.** All detection runs locally using Claude's built-in tools.
No npm packages, no pip installs, no API calls to external scanning services.

**Consistent output format.** The report structure for each skill must stay identical
across versions. Users build workflows on top of these outputs.

**Anti-manipulation rules are permanent.** No contribution can weaken the prompt
injection resistance in any skill. If anything, contributions should strengthen it.

**`.security/hardcoded-values.env` format is fixed.** All skills that extract secrets
must use this exact format. No exceptions.

**Fail-safe rewrites only.** Any rewrite pattern added must fail loudly if the
environment variable is missing — never silently with an empty value.

---

## Questions

Open an issue with the `question` label.
