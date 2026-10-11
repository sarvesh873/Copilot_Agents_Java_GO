---
name: 'commit-message'
description: 'Generate terse, standardized Conventional Commit messages based on staged git changes or diffs.'
argument-hint: '[staged | diff | commit]'
agent: 'agent'
---

# ✍️ Conventional Commit Message Generator (/commit-message)

Generate a high-density, professional Git commit message following the **Conventional Commits 1.0.0** specification based on the provided diff or staged changes.

---

## 📐 Commit Message Structure

```text
<type>(<scope>): <terse imperative description>

[optional body: explain WHY and impact, not just line-by-line WHAT]

[optional footer(s): BREAKING CHANGE or issue references]
```

---

## 🏷️ Commit Types

Select the single most accurate type for the primary change:

| Type | When to Use |
|---|---|
| `feat` | A new user-facing or API feature or capability |
| `fix` | A bug fix or defect correction |
| `refactor` | Code restructuring that neither fixes a bug nor adds a feature |
| `perf` | A code change that improves execution performance or memory usage |
| `test` | Adding missing tests, refactoring test suites, or fixing tests |
| `chore` | Maintenance tasks, dependency updates, build tooling, or internal changes |
| `docs` | Documentation changes only (README, docstrings, markdown files) |
| `ci` | Changes to CI/CD workflows, build scripts, or pipelines |
| `security` | Remediation of security vulnerabilities, CVEs, or dependency patches |

---

## 🎯 Formatting & Tone Rules

1. **Imperative Mood**: Use the imperative present tense in the subject line:
   - ✅ `add payment retry logic`
   - ❌ `added payment retry logic` / `adds payment retry logic`
2. **Subject Line Constraints**:
   - Keep the subject line under **72 characters** (strict hard limit: 50–72 chars).
   - Use lowercase for the description (unless starting with a proper noun or acronym).
   - **No trailing period/punctuation** at the end of the subject line.
3. **Scope Selection**:
   - Optional but strongly encouraged when changes are localized to a specific module or feature.
   - Examples: `feat(order):`, `fix(auth):`, `refactor(db):`, `test(cart):`, `docs(readme):`.
4. **Breaking Changes**:
   - Append `!` after the type/scope: `feat(api)!: change user response payload`.
   - Include a `BREAKING CHANGE: <explanation>` footer explaining the break and migration path.
5. **Body Guidelines (when needed)**:
   - Separate the subject from the body with a blank line.
   - Focus on **WHY** the change was made and the technical context/rationale, not an echo of the diff.
   - Wrap body lines at ~72 characters.
   - Use concise bullet points for multi-part changes.
6. **Issue Linking**:
   - Reference issue or ticket IDs in the footer (e.g., `Closes #123`, `Fixes PROJ-456`).

---

## 🚫 What to Avoid

- Do **NOT** write vague messages like `fix bug`, `update code`, `WIP`, or `various fixes`.
- Do **NOT** list every modified file in the subject line.
- Do **NOT** include conversational filler ("Here is your commit message:", "Sure, I generated...").
- Output the raw commit message directly so it can be pasted straight into `git commit -m` or picked up by automated tooling.

---

## 💡 Examples

### Single-line fix:
```text
fix(auth): handle expired jwt tokens without throwing 500 error
```

### Feature with rationale body:
```text
feat(order): add idempotency key check to checkout endpoint

Prevent duplicate payment processing on network retry by validating
incoming Idempotency-Key headers against Redis cache with 24h TTL.

Closes #142
```

### Breaking change:
```text
feat(api)!: migrate customer id format from int64 to uuid string

BREAKING CHANGE: Customer ID fields across all REST endpoints now accept
and return UUID v4 strings instead of incremental integers.
```

### Security patch:
```text
security(deps): bump spring-core to 6.1.14 to resolve CVE-2024-38816
```

