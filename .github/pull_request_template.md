## Description

<!-- Describe what this PR does and why. -->

## Type

<!-- Check the one that applies: -->

- [ ] `docs` - A post, a page or other content
- [ ] `feat` - A new capability of the site
- [ ] `fix` - A fix of the site
- [ ] `ci` - Workflows or scripts
- [ ] `chore` - Maintenance

## Changes

<!-- List the main changes introduced by this PR: -->

-

## Related Issues

<!-- Link related issues: Closes #123, Fixes #456 -->

## Checklist

- [ ] Branch follows the naming convention (`post/<slug>`, `issue-<number>-<slug>`...)
- [ ] Commits follow [Conventional Commits](https://www.conventionalcommits.org/)
- [ ] `uv run pre-commit run --all-files` passes
- [ ] `uv run --only-group docs zensical build --clean --strict` passes
- [ ] `uv run pytest` passes if scripts changed
- [ ] `uv run multicz validate --strict` passes
