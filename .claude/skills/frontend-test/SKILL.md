---
name: frontend-test
description: Validate HTML files for antipatterns, accessibility issues, and best practices. Scores each issue by severity (Critical, High, Medium, Low). Use when the user wants to audit, review, or validate HTML quality.
---

When running a frontend HTML audit, follow these steps:

1. Find all `.html` files in the project using glob patterns (`**/*.html`)
2. Read each HTML file completely
3. Analyze every file against the antipattern checklist below
4. Output the report using the exact format specified

## Antipattern Checklist

Evaluate each HTML file against these categories:

### Security
- Inline event handlers (`onclick`, `onload`, etc.) — prefer external JS
- Missing `rel="noopener noreferrer"` on `target="_blank"` links
- Inline `<script>` with sensitive data or logic
- Missing CSP meta tags on pages that load external resources
- Use of `javascript:` in href attributes

### Accessibility (a11y)
- Images without `alt` attribute
- Missing `lang` attribute on `<html>`
- Missing semantic landmarks (`<header>`, `<main>`, `<nav>`, `<footer>`)
- Non-semantic structure (excessive `<div>` / `<span>` instead of proper elements)
- Missing form labels or `aria-label` on interactive elements
- Incorrect heading hierarchy (skipping levels, e.g., `<h1>` to `<h3>`)
- Missing `<title>` tag
- Low color contrast indicators (inline styles with light colors)
- Missing `role` attributes on custom interactive elements

### Performance
- Render-blocking `<script>` without `defer` or `async`
- Large inline styles instead of external CSS
- Missing `loading="lazy"` on images below the fold
- Unused or duplicated CSS/JS imports
- Missing `<meta charset>` (causes re-parsing)

### SEO
- Missing `<meta name="description">`
- Missing `<meta name="viewport">` for responsive design
- Multiple `<h1>` tags on a single page
- Missing Open Graph / social meta tags
- Empty or missing `<title>`

### HTML Best Practices
- Deprecated tags (`<center>`, `<font>`, `<marquee>`, `<b>` instead of `<strong>`)
- Duplicate `id` attributes
- Missing `DOCTYPE` declaration
- Unclosed or self-closing tags used incorrectly
- Inline styles instead of CSS classes
- Empty tags with no purpose
- Missing `<meta charset="UTF-8">`
- Tables used for layout instead of data
- Missing `type` attribute on `<script>` or `<style>` when needed

### Maintainability
- Hardcoded URLs or paths
- Mixed content (HTTP resources in HTTPS pages)
- Deeply nested DOM (more than 8 levels)
- Files exceeding 500 lines without partials/components
- Comments with TODO/FIXME/HACK left in production code

## Severity Scoring

Assign each issue a severity based on this criteria:

| Severity     | Score | Criteria |
|-------------|-------|----------|
| 🔴 Critical | 4     | Security vulnerabilities, broken functionality, data exposure |
| 🟠 High     | 3     | Accessibility barriers, SEO blockers, major performance issues |
| 🟡 Medium   | 2     | Best practice violations, maintainability concerns, minor a11y |
| 🟢 Low      | 1     | Style preferences, minor optimizations, nice-to-haves |

## Output Format

Use this exact format for the report:

```
=======================================================
  FRONTEND HTML AUDIT REPORT
=======================================================
  Files analyzed: <count>
  Date:           <YYYY-MM-DD>
  Total issues:   <count>
  Overall score:  <X>/100
=======================================================

📊 SUMMARY BY SEVERITY

  🔴 Critical: <count>
  🟠 High:     <count>
  🟡 Medium:   <count>
  🟢 Low:      <count>

-------------------------------------------------------
📁 FILE: <file-path>
-------------------------------------------------------

  🔴 CRITICAL
  ──────────
  [C-01] <Short title>
         Line: <line number>
         Issue: <description of the antipattern found>
         Fix:   <specific recommendation to fix it>

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

(Repeat for each file)

=======================================================
  📋 TOP RECOMMENDATIONS
=======================================================

  1. <Most critical fix — what and where>
  2. <Second most critical fix>
  3. <Third most critical fix>
  (up to 5 recommendations, ordered by severity)

=======================================================
  SCORE CALCULATION
=======================================================

  Base score: 100
  Critical issues: -<n> × 15 = -<total>
  High issues:     -<n> × 10 = -<total>
  Medium issues:   -<n> × 5  = -<total>
  Low issues:      -<n> × 2  = -<total>
  ─────────────────────────────
  Final score: <X>/100 (minimum 0)
```

## Rules

- Always read the full content of each HTML file before analyzing
- Report line numbers for every issue found
- If a severity category has no issues for a file, omit that section
- Order files by number of issues (most issues first)
- The overall score starts at 100 and deducts points per issue
- Minimum score is 0 (do not go negative)
- Provide actionable, specific fix recommendations (not generic advice)
- If no HTML files are found, inform the user clearly
