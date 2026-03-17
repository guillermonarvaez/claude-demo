---
name: audit-backend
description: Audit pascal-app-backend code against architecture rules in CLAUDE.md. Use when reviewing PRs, validating new features, or checking that code follows the layered architecture (route → service → repository). Accepts a module name, file path, or "all" to audit everything.
argument-hint: "[module-name|file-path|all]"
---

# Backend Architecture Audit

Audit `$ARGUMENTS` in pascal-app-backend against the Code Rules defined in `pascal-app-backend/CLAUDE.md`.

## Procedure

### 1. Load rules and identify files

Read `pascal-app-backend/CLAUDE.md` — it is the single source of truth for all rules. Then identify files to audit:

- **Module name** (e.g., `nurturing`): all files in `src/{module}/` + related plugins
- **File path**: that specific file
- **`all`**: all files in `src/` and `plugins/`
- **Empty**: recently changed files via `git diff --name-only HEAD~5`

Classify each file by layer (route, service, repository, helper, plugin, type) based on its directory.

### 2. Audit each file

Check every rule from CLAUDE.md that applies to the file's layer. Pay special attention to:

- **Routes**: handler ≤15 lines, zero repo imports (must use service wrappers), no business logic
- **Services**: function ≤80 lines, zero raw DB queries (`.from()`/`.select()`), no fastify import
- **Repositories**: pure data access, no business logic, no cross-repo calls, throw on error
- **Plugins**: ≤30 lines, single service call in try block

### 3. Scan for dead code

For every exported function, interface, and type alias, use Grep to verify it is imported by at least one other file. For namespace imports (`import * as foo`), verify each function is called via `foo.functionName`. Also check within each file for:

- Unused imports
- Private functions never called within the file
- `@deprecated` markers (must be deleted, not annotated)
- Commented-out code or `// removed` placeholders

### 4. Output the report

**CRITICAL: Output the ENTIRE report as direct text — NEVER use the Bash tool.** Bash output gets collapsed in Claude Code's terminal. Direct text always renders fully visible.

For the logo, use the Read tool to read `.claude/pascal-logo.txt`, then include its content inside a markdown code block (triple backticks) at the top of your text output.

**Severity labels:**

- **CRITICAL** — Layer boundary violation (route imports repo, service has raw queries, plugin has logic)
- **HIGH** — Line limit exceeded, dead code (unused export, @deprecated)
- **MEDIUM** — Missing schema validation, inconsistent error handling
- **LOW** — Naming inconsistency, unnecessary complexity

**Report structure:**

```
(logo in code block)

# Architecture Audit Report
Module: {name} | Files audited: {count}

---

### src/{module}/routes/example.route.ts (Route)

- **CRITICAL**  L10  Route imports repository directly — must use service wrapper
- **HIGH**      L45  Handler is 20 lines — max is 15

---

| Severity | Count |
|----------|-------|
| Critical | 1     |
| High     | 1     |
| Medium   | 0     |
| Low      | 0     |

**Top 3 fixes:**
1. ...
2. ...
3. ...
```

- Only show files that have violations
- If zero violations: output **"No violations found. Code is compliant."**

### 5. Offer to fix

If violations exist, prompt:

```
Would you like me to fix these violations?

  [A] Fix CRITICAL + HIGH
  [B] Fix MEDIUM
  [C] Fix all
  [N] No, just the report
```

Wait for the user's response. If they choose a fix option, apply fixes and re-run the audit to confirm resolution.
