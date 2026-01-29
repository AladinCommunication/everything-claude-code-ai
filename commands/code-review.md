# Code Review

Comprehensive security and quality review of staged changes (before commit):

1. Get staged files: git diff --cached --name-only

2. For each changed file, check for:

**Security Issues (CRITICAL):**
- Hardcoded credentials, API keys, tokens
- SQL injection vulnerabilities
- XSS vulnerabilities  
- Missing input validation
- Insecure dependencies
- Path traversal risks

**Code Quality (HIGH):**
- Functions > 50 lines
- Files > 800 lines
- Nesting depth > 4 levels
- Missing error handling
- console.log statements
- TODO/FIXME comments
- Missing JSDoc for public APIs

**Best Practices (MEDIUM):**
- Mutation patterns (use immutable instead)
- Emoji usage in code/comments
- Missing tests for new code
- Accessibility issues (a11y)

3. Generate report with:
   - Severity: CRITICAL, HIGH, MEDIUM, LOW
   - File location and line numbers
   - Issue description
   - Suggested fix

4. Block commit if CRITICAL or HIGH issues found

Never approve code with security vulnerabilities!

## Output Format (MANDATORY)

You MUST:

1. Produce ONLY valid JSON.
2. Match exactly the schema below.
3. Do not include markdown.
4. Do not include explanations outside JSON.
5. Save the file to:

.ai/reports/code-review.json

If the directory does not exist, create it.

### JSON Schema
{
  "name": "code_review_report",
  "description": "Return code review findings with severities and counts.",
  "input_schema": {
    "type": "object",
    "additionalProperties": false,
    "properties": {
      "meta": {
        "type": "object",
        "additionalProperties": false,
        "properties": {
          "commit": {
            "type": "string",
            "pattern": "^[0-9a-f]{7,40}$",
            "description": "Git commit SHA (HEAD) that this review report was generated for."
          },
          "generated_at": {
            "type": "string",
            "description": "ISO-8601 timestamp when the report was generated. e.g., 2026-01-29T16:20:00+09:00"
          },
          "schema_version": {
            "type": "string",
            "description": "Schema version for report compatibility. e.g., 1.0.0"
          }
        },
        "required": ["commit", "generated_at", "schema_version"]
      },
      "summary": {
        "type": "object",
        "additionalProperties": false,
        "properties": {
          "critical": { "type": "integer" },
          "high": { "type": "integer" },
          "medium": { "type": "integer" },
          "low": { "type": "integer" }
        },
        "required": ["critical", "high", "medium", "low"]
      },
      "findings": {
        "type": "array",
        "items": {
          "type": "object",
          "additionalProperties": false,
          "properties": {
            "severity": { "type": "string", "enum": ["CRITICAL", "HIGH", "MEDIUM", "LOW"] },
            "file": { "type": "string" },
            "line": { "type": "integer" },
            "issue": { "type": "string" },
            "fix": { "type": "string" }
          },
          "required": ["severity", "file", "line", "issue", "fix"]
        }
      }
    },
    "required": ["meta", "summary", "findings"]
  },
  "strict": true
}


