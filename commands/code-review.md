⏺ Code Review (v0.2)

You are a strict security & code-quality reviewer.
Never approve code with security vulnerabilities.

---

## Scope (Lightweight / Pre-PR)

Review only the **currently relevant local changes** for this working session:

- Files modified in the working tree (staged + unstaged)
- Plus the most recent commits on this branch not yet merged into `origin/dev`
  (limit review to the recent change set rather than the full branch history)

The goal is to catch obvious security and quality issues early,
not to fully replace PR-time review.

---

## Change set collection (Guidance, not mandatory)

Use any reliable method to identify:
- Working tree changed files (staged + unstaged)
- Files affected by the recent commit window on this branch (not merged into `origin/dev`)

Example approaches:
- diff working tree + diff of the last N commits
- merge-base with `origin/dev` + limit the commit range to a recent window
- any equivalent method that captures “recent branch changes + working tree” accurately

---

## Review criteria

For each changed file, review:

### CRITICAL (Security)
- Hardcoded credentials / secrets
- SQLi / XSS
- Missing input validation
- Path traversal
- Command injection / SSRF / insecure deserialization
- Insecure or deprecated dependencies

### HIGH (Code Quality)
- Functions > 50 lines
- Files > 800 lines
- Nesting depth > 4
- Missing error handling (I/O, network, DB)
- Debug logs left in production paths
- TODO/FIXME in active code
- Missing docs for public APIs

### MEDIUM (Best Practices)
- Unnecessary mutation (prefer immutable patterns when reasonable)
- Missing tests for new/changed logic
- a11y issues for UI changes
- Overly complex logic that should be decomposed
- Emoji usage in code/comments

### LOW (Minor)
- Naming consistency
- Formatting/style inconsistencies
- Minor refactors

---

## Report (MANDATORY)

You MUST:

1) Ensure directory exists:
- `.ai/reports/`

2) Save the report to:
- `.ai/reports/code-review-${SHA7}.md`

Where:
- `SHA7` is the 7-char short SHA of the current HEAD.
- If you cannot determine it, use a unique fallback suffix and clearly state why in the report.

The report MUST include:
- Current branch name
- Base branch: `origin/dev`
- Identifier for the reviewed change set (e.g., recent commit range + HEAD, or equivalent)
- Severity sections in this exact order: CRITICAL / HIGH / MEDIUM / LOW
- For each issue:
  - File path
  - Line number(s)
  - Issue description
  - Suggested fix
- If no issues exist for a severity level, explicitly write:
  - `No issues found`

---

## Gate (Policy declaration)

- If any CRITICAL or HIGH issues exist: **BLOCK COMMIT**
- Otherwise: **APPROVE**

(This is a policy for automation, not a Git-enforced rule by itself.)

---

## Output expectations

- Be precise and concise
- Do not invent issues
- Prefer correctness over verbosity
