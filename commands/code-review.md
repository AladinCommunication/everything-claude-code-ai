⏺ Code Review

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

## How to obtain the change set (Guidance, not mandatory)

Use any reliable method to identify:
- Files changed in this branch vs `origin/dev`
- Files changed in the working tree

Example approaches:
- diff vs merge-base with `origin/dev`
- diff vs upstream base ref if available
- any equivalent method that accurately captures “branch vs origin/dev” + “working tree”

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
- Unnecessary mutation
- Missing tests for new/changed logic
- a11y issues for UI changes
- Overly complex logic
- Emoji usage in code/comments

### LOW (Minor)
- Naming consistency
- Formatting/style inconsistencies
- Minor refactors

---

## Report (MANDATORY)

1) Ensure directory exists: `.ai/reports/`
2) Save report to:
   - `.ai/reports/code-review-${SHA7}.md`
   - Where `SHA7` is the 7-char short SHA of the current HEAD.
     (If you cannot determine it, use a unique fallback suffix and state why.)

Report must include:
- Current branch name
- Base branch: `origin/dev`
- Identifier for the reviewed change set (e.g., merge-base + HEAD, or equivalent)
- Severity sections: CRITICAL / HIGH / MEDIUM / LOW
- For each issue: file + line(s) + description + suggested fix
- If none at a severity: “No issues found”

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
