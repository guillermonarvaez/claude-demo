---
name: frontend-test
description: Audit HTML/CSS/JS frontend files for antipatterns, accessibility, security, performance, and best practices. Scores each issue by severity (Critical, High, Medium, Low). Accepts a file path, glob pattern, or "all" to audit everything.
argument-hint: "[file-path|glob-pattern|all]"
---

# Frontend Audit

Audit `$ARGUMENTS` in the current project against frontend best practices.

## Procedure

### 1. Identify files to audit

Determine scope based on `$ARGUMENTS`:

- **File path** (e.g., `index.html`): that specific file
- **Glob pattern** (e.g., `src/**/*.html`): all matching files
- **`all`**: all `.html`, `.htm`, `.css`, and inline `<script>`/`<style>` blocks in the project
- **Empty**: recently changed frontend files via `git diff --name-only HEAD~5` filtered to `.html`, `.htm`, `.css`

Classify each file by type:

| Type       | Detection                                            |
| ---------- | ---------------------------------------------------- |
| Page       | Standalone `.html` with `<html>`, `<head>`, `<body>` |
| Component  | `.html` partial without full document structure      |
| Stylesheet | `.css` file or `<style>` blocks                      |
| Script     | `.js` file referenced by HTML or `<script>` blocks   |

### 2. Read and audit each file

Read every file completely. Then check against **all applicable rules** from the checklist below. For each violation found, record:

- Severity level
- Exact line number(s)
- Short title
- Description of the antipattern
- Specific fix recommendation (not generic advice)

#### Security

- Inline event handlers (`onclick`, `onload`, etc.) — must use external JS with `addEventListener`
- Missing `rel="noopener noreferrer"` on `target="_blank"` links
- Inline `<script>` with sensitive data, DOM manipulation, or business logic
- Missing CSP meta tags on pages that load external resources
- Use of `javascript:` in `href` attributes
- `innerHTML` assignments with user-controlled or dynamic content (XSS vector)
- Loading scripts/resources from HTTP on HTTPS pages (mixed content)

#### Accessibility (a11y)

- Images without `alt` attribute
- Missing `lang` attribute on `<html>`
- Missing semantic landmarks (`<header>`, `<main>`, `<nav>`, `<footer>`)
- Non-semantic structure (excessive `<div>` / `<span>` instead of proper elements)
- Missing form labels or `aria-label` on interactive elements
- Incorrect heading hierarchy (skipping levels, e.g., `<h1>` to `<h3>`)
- Missing `<title>` tag
- Low color contrast indicators (inline styles with light-on-light or dark-on-dark)
- Missing `role` attributes on custom interactive elements
- Non-interactive elements with click handlers (use `<button>` or `<a>`)
- Missing `tabindex` or keyboard support on custom controls

#### Performance

- Render-blocking `<script>` in `<head>` without `defer` or `async`
- Large inline styles instead of external CSS (>5 declarations)
- Missing `loading="lazy"` on images below the fold
- Unused or duplicated CSS/JS imports
- Missing `<meta charset>` (causes re-parsing)
- Aggressive `setInterval`/`setTimeout` with intervals <500ms
- Oversized images (width/height attributes >1000px without responsive handling)

#### SEO

- Missing `<meta name="description">`
- Missing `<meta name="viewport">` for responsive design
- Multiple `<h1>` tags on a single page
- Missing Open Graph / social meta tags
- Empty or missing `<title>`

#### HTML Best Practices

- Deprecated tags (`<center>`, `<font>`, `<marquee>`, `<blink>`, `<b>` instead of `<strong>`)
- Deprecated attributes (`bgcolor`, `align`, `valign`, `border` on tables for layout)
- Duplicate `id` attributes
- Missing `<!DOCTYPE html>` declaration
- Unclosed or incorrectly self-closing tags
- Inline styles instead of CSS classes (>3 inline style attributes per file = violation)
- Empty tags with no purpose
- Missing `<meta charset="UTF-8">`
- Tables used for page layout instead of data
- Hardcoded pixel widths >1200px (non-responsive)
- `href="#"` on anchor tags (use `<button>` for actions)

#### Maintainability

- Hardcoded absolute URLs or file paths
- Deeply nested DOM (more than 8 levels)
- Files exceeding 300 lines without componentization
- Comments with TODO/FIXME/HACK left in code
- Duplicated code blocks (same structure repeated >2 times)
- Functions with >10 parameters
- Loose comparisons (`==` instead of `===`) in inline/embedded scripts

### 3. Scan for dead code

For each HTML file, verify:

- CSS classes referenced in `class=""` attributes actually exist in linked stylesheets or `<style>` blocks
- JS functions called in event handlers or scripts are defined somewhere accessible
- `<link>` and `<script src="">` references point to files that exist
- IDs referenced by `getElementById`, `querySelector`, or `<label for="">` exist in the DOM
- Unused `<meta>` tags or duplicate declarations

For CSS files, check:

- Selectors that don't match any element in linked HTML files
- Duplicate property declarations within the same rule
- `!important` overuse (>3 per file = violation)

### 4. Output the report

**CRITICAL: Output the ENTIRE report as direct text — NEVER use the Bash tool.** Bash output gets collapsed in Claude Code's terminal. Direct text always renders fully visible.

**Severity labels and weight:**

| Severity | Label | Weight  | Criteria                                                                                       |
| -------- | ----- | ------- | ---------------------------------------------------------------------------------------------- |
| CRITICAL | 🔴    | -15 pts | Security vulnerabilities, broken functionality, data exposure, infinite loops, DOM destruction |
| HIGH     | 🟠    | -10 pts | Accessibility barriers, SEO blockers, major performance issues, missing meta essentials        |
| MEDIUM   | 🟡    | -5 pts  | Deprecated tags/attributes, best practice violations, maintainability concerns                 |
| LOW      | 🟢    | -2 pts  | Style preferences, minor optimizations, nice-to-haves                                          |

**Report structure:**

```
=======================================================
  SEIDOR | Frontend Audit Report
=======================================================
  Scope:          <$ARGUMENTS or "recently changed files">
  Files audited:  <count>
  Date:           <YYYY-MM-DD>
=======================================================

For the logo, use the Read tool to read `.claude/seidor-logo.txt`, then include its content inside a markdown code block (triple backticks) at the top of your text output.

-------------------------------------------------------
📁 <file-path> (<File Type>)
-------------------------------------------------------

  🔴 CRITICAL
  ──────────
  [C-01] <Short title>
         Line: <line number>
         Issue: <description of the antipattern>
         Fix:   <specific recommendation>

  🟠 HIGH
  ──────
  [H-01] <Short title>
         Line: <line number>
         Issue: <description>
         Fix:   <recommendation>

  🟡 MEDIUM
  ────────
  [M-01] <Short title>
         Line: <line number>
         Issue: <description>
         Fix:   <recommendation>

  🟢 LOW
  ─────
  [L-01] <Short title>
         Line: <line number>
         Issue: <description>
         Fix:   <recommendation>

  🪦 DEAD CODE
  ───────────
  [D-01] <Short title>
         Line: <line number>
         Issue: <description>

(Repeat for each file with violations)

=======================================================
  📊 Summary
=======================================================

| Severity | Count |
|----------|-------|
| Critical | <n>   |
| High     | <n>   |
| Medium   | <n>   |
| Low      | <n>   |
| Dead Code| <n>   |
| **Total**| <n>   |

=======================================================
  📋 Top 5 Fixes (ordered by severity)
=======================================================

  1. <Most critical fix — what, where, and why>
  2. ...
  3. ...
  4. ...
  5. ...

=======================================================
  🧮 Score
=======================================================

  Base score:       100
  Critical (×<n>):  -<total>
  High     (×<n>):  -<total>
  Medium   (×<n>):  -<total>
  Low      (×<n>):  -<total>
  ─────────────────────────
  Final score: <X>/100 (minimum 0)
```

- Only show files that have violations
- If a severity category has no issues for a file, omit that section
- Order files by number of issues (most issues first)
- If zero violations: output **"No violations found. Frontend code is compliant. Score: 100/100"**

### 5. Offer to fix

If violations exist, prompt:

```
Would you like me to fix these violations?

  [A] Fix CRITICAL + HIGH only
  [B] Fix MEDIUM + LOW only
  [C] Fix all violations
  [N] No, just the report
```

Wait for the user's response. If they choose a fix option:

1. Apply fixes file by file, prioritizing by severity (CRITICAL first)
2. After all fixes are applied, **re-run the full audit** to confirm resolution
3. Show a before/after score comparison
