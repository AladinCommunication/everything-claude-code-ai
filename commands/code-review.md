⏺ Code Review Complete ✅

Summary:
Review code changes on the current working branch by comparing against `origin/dev`.
Code Review (branch diff vs origin/dev)

You are a strict security and code-quality reviewer.
Never approve code with security vulnerabilities.

---

## Scope

- Review all code changes introduced in the **current branch**
- Base branch is fixed and always:
  - `origin/dev`
- Review includes:
  - Committed changes in this branch (diff vs origin/dev)
  - Uncommitted local changes (working tree)

---

## Step 0) Resolve base and identifiers

1) Base branch:
   - BASE=origin/dev

2) Find merge-base between base and HEAD:
   - MB=$(git merge-base $BASE HEAD)

3) Get short SHA for current HEAD:
   - SHA7=$(git rev-parse --short=7 HEAD)

---

## Step 1) Collect changed files

1) Files changed in this branch (committed):
   - git diff --name-only $MB..HEAD

2) Files changed but not yet committed:
   - git diff --name-only

3) Merge both lists, remove duplicates.
4) Ignore deleted files unless necessary for security review.

---

## Step 2) Review criteria

For each changed file, review the following.

### 🔴 Security Issues (CRITICAL)
- Hardcoded credentials, API keys, secrets, tokens
- SQL injection vulnerabilities
- XSS vulnerabilities
- Missing or improper input validation
- Path traversal vulnerabilities
- Command injection / SSRF / insecure deserialization
- Use of insecure or deprecated dependencies

---

### 🟠 Code Quality Issues (HIGH)
- Functions longer than 50 lines
- Files longer than 800 lines
- Nesting depth greater than 4
- Missing or insufficient error handling
- Debug logs (console.log / print) left in production paths
- TODO / FIXME comments in active code
- Missing documentation for public APIs (JSDoc / docstring)

---

### 🟡 Best Practice Issues (MEDIUM)
- Unnecessary mutation (prefer immutable patterns when reasonable)
- Missing tests for new or changed logic
- Accessibility (a11y) issues in UI code
- Overly complex logic that should be decomposed
- Emoji usage in code or comments

---

### 🔵 Minor Issues (LOW)
- Naming consistency
- Formatting or style inconsistencies
- Minor refactoring suggestions

---

## Step 3) Generate report (MANDATORY)

You MUST:

1) Create directory if it does not exist:
   - `.ai/reports/`

2) Save the report to:
   - `.ai/reports/code-review-${SHA7}.md`

3) The report MUST include:
   - Current branch name
   - Base branch: `origin/dev`
   - Merge-base commit SHA
   - Current HEAD SHA
   - Severity sections: CRITICAL / HIGH / MEDIUM / LOW
   - For each issue:
     - File path
     - Line number(s)
     - Description
     - Suggested fix

4) If no issues exist for a severity level, explicitly state:
   - “No issues found”

---

## Step 4) Review gate (policy declaration)

- If **any CRITICAL or HIGH issues** are found:
  - Mark the review result as: **BLOCK COMMIT**
  - Clearly explain why

- If **no CRITICAL or HIGH issues** are found:
  - Mark the review result as: **APPROVE**
  - Still list MEDIUM / LOW improvements if applicable

This gate is a **policy declaration** for automation (hooks / CI),
not a Git-enforced rule by itself.

---

## Output expectations

- Be precise and concise
- Do not invent issues
- Do not approve code with security vulnerabilities
- Prefer correctness over verbosity
