# Security policy

## Reporting a vulnerability

**Please do not open a public issue for security vulnerabilities.**

Use one of these private channels:

- **Preferred:** GitHub's private vulnerability reporting -
  <https://github.com/goabonga/blog/security/advisories/new>
- Email: <goabonga@pm.me>

Include:

- A description of the vulnerability and its impact
- Steps to reproduce or a proof-of-concept
- The affected page, or the affected workflow or script

You should receive an acknowledgment within 72 hours. The aim is a fix
within 14 days for high-severity issues, coordinated with you before any
public disclosure.

## Supported versions

Only the site published on GitHub Pages from `main` is supported.

## Scope

The blog is a static site, so the exposure is limited to the published
pages and to the CI automation that builds and releases them. That
automation signs commits and pushes to `main`: a flaw that lets a pull
request reach its secrets, or its signing key, is in scope.
