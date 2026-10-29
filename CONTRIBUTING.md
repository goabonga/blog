# Contributing to blog

Thanks for taking the time to contribute. This guide covers the setup, the
development loop, and the conventions used for branches and commits - both
of which drive the release pipeline.

## Setup

The blog is a [Zensical](https://zensical.org) site managed with
[uv](https://docs.astral.sh/uv/). The helper scripts of the CI are Python
3.12+.

```bash
# 1. install uv (one-time)
curl -LsSf https://astral.sh/uv/install.sh | sh

# 2. clone and sync
git clone https://github.com/goabonga/blog
cd blog
uv sync --group docs
```

`uv sync` installs the `dev` group (pytest, pytest-cov, ruff, pre-commit,
multicz) into `.venv/`. `--group docs` adds Zensical.

Install the pre-commit and commit-msg hooks once per clone. They run ruff,
the SPDX header checks and the Conventional Commits validation before each
commit lands:

```bash
uv run pre-commit install
uv run pre-commit run --all-files   # run every hook on the whole tree
```

## Writing

The content lives under `docs/`, the site configuration in `zensical.toml`.

```bash
uv run zensical serve            # http://localhost:8000, rebuilt on save
uv run zensical build --strict   # validate links and render, as the CI does
```

### Add a post

Create `docs/posts/<slug>.md`, starting with a front matter that holds
at least the date:

```markdown
---
date: 2026-01-31
categories:
  - Notes
---

# Title of the post

The excerpt shown on the blog index.

<!-- more -->

The rest of the post.
```

The blog lists the posts by date, category and year on its own.

### Add a page

Create `docs/<slug>.md` with a `# Title`, then list it under `nav` in
`zensical.toml`:

```toml
nav = [
  { "Home" = "index.md" },
  { "Blog" = "index.md" },
  { "About" = "about.md" },
]
```

## Development workflow

### Tests

The tests cover the CI helper scripts.

```bash
uv run pytest                              # full suite
uv run pytest tests/test_ci_automation.py  # one file
uv run pytest --cov=scripts                # with coverage
```

### Lint and format

[ruff](https://docs.astral.sh/ruff/) lints and formats `scripts/` and
`tests/`. Config is in `[tool.ruff]` (`pyproject.toml`).

```bash
uv run ruff check                # lint
uv run ruff format               # format
uv run ruff format --check       # CI-style check (no writes)
```

CI runs them next to the tests, the SPDX header check, the strict site
build and the security scans (bandit, pip-audit, osv-scanner), and all of
them gate the release. Each job writes a summary of its result on the run
page.

### License headers

Every Python and workflow file starts with the two-line SPDX header (after
the shebang, if any):

```
# SPDX-License-Identifier: MIT
# Copyright (c) 2026 Chris <goabonga@pm.me>
```

```bash
python3 scripts/add_license_header.py --path scripts --types py          # add
python3 scripts/add_license_header.py --path .github --types yml --check
```

## Branches and merging

### Branch names

A post, a page or a small change needs no issue: name the branch after what
it does.

| Work | Branch | Created from | Pull request into |
| --- | --- | --- | --- |
| A post | `post/<slug>` | `main` | `main` |
| A page | `page/<slug>` | `main` | `main` |
| Anything else without an issue | `<type>/<slug>`, with the type of the commit (`docs`, `fix`, `ci`, `chore`...) | `main` | `main` |
| An issue | `issue-<number>-<slug>` | `main` | `main` |
| A sub-issue of an issue | `issue-<number>-<slug>` (its own issue number) | the branch of the parent issue | the branch of the parent issue |

`<slug>` is a few lowercase words separated by hyphens, for example
`post/hello-world` or `issue-12-dark-theme`.

Work that has a GitHub issue uses `issue-<number>-<slug>`, named after the
issue it addresses. Keep it free of `/`: Git cannot hold `issue-12` and
`issue-12/...` at the same time, and the automation only recognises
`issue-*` as a base branch, so do not use that prefix without an issue.

An issue split into sub-issues gets one branch of its own, which collects
the pull requests of its sub-issues. Once they are all merged, the issue
branch is proposed to `main` in a single pull request. Dependabot branches
(`dependabot/...`) are created by Dependabot and need no naming.

The conventional commit type is carried by the commits and the pull request
title, not by the branch name.

### Signed commits and merging

`main` and the `issue-*` branches require signed commits, a linear history,
a pull request and the CI checks. A pull request is merged by adding the
`auto-merge` label, which needs write access: the workflow replays its
commits GPG-signed onto the base branch once the required checks passed,
then fast-forwards that branch. The commits of a branch pushed unsigned,
such as by GitHub's "Update branch", are re-signed in place.

Dependabot pull requests are rewritten as Conventional Commits and signed.
Minor and patch updates are queued for the same merge once CI passed; a
major update only gets a notice and waits for a maintainer to add the
label.

Each Dependabot pull request also gets an issue, assigned to the repository
owner, which the pull request closes when it is merged. If the pull request
is closed without a merge, the issue is closed as not planned.

The automation needs the `AUTO_MERGE_PAT`, `GPG_PRIVATE_KEY` and
`GPG_PASSPHRASE` secrets and the `GIT_USER_NAME`, `GIT_USER_EMAIL` and
`GIT_SIGNING_KEY` variables of the repository. The release job pushes its
commit and tags with the same token, because the `main` ruleset does not
let the workflow token do it.

## Commit messages

The blog uses [Conventional Commits](https://www.conventionalcommits.org/)
because its release pipeline reads them:

```
<type>(<optional-scope>): <imperative summary>

<optional body explaining the why>

<optional footer - BREAKING CHANGE: ..., refs, etc.>
```

Only the content of the site is released: a commit counts when it touches
`docs/` or `zensical.toml`.

| prefix                                      | bump  |
|---------------------------------------------|-------|
| `docs:`, `fix:`, `perf:`                    | patch |
| `feat:`                                     | minor |
| any commit with `BREAKING CHANGE:` in body, or `feat!:` / `fix!:` | major |
| `chore:`, `refactor:`, `test:`, `style:`, `ci:`, `build:` | none  |

A new post or page is a `docs:` commit, and the site is deployed when the
release it triggers is done.

Guidelines:

- **One logical change per commit.** A post and a fix of the theme are two
  commits.
- **Imperative mood** in the summary: "add", "fix", "remove" - not
  "added", "fixes", "removing".
- **No trailing period** on the summary, at most 72 characters, and a
  lowercase first word after the colon. The signed merge refuses any other
  subject, so the `commit-subject` pre-commit hook and the `commit-subjects`
  CI job check it first.
- **No `Co-Authored-By` footers** unless the work was actually
  pair-authored.
- **Reference issues in the body**, not the title: `Closes #42`.

## Pull requests

Before pushing, run the hooks (they mirror the CI checks) and the build:

```bash
uv run pre-commit run --all-files
uv run zensical build --clean --strict
uv run pytest
```

In your PR:

- Open it against `main`, or against the issue branch for a sub-issue.
- Title follows Conventional Commits.
- One PR per logical change.

## Code of Conduct

By participating, you agree to abide by the
[Code of Conduct](CODE_OF_CONDUCT.md). Report incidents to
<goabonga@pm.me>.

## Security

Found a vulnerability? Don't open a public issue. See
[`.github/SECURITY.md`](.github/SECURITY.md) for the disclosure process.

## License

By contributing, you agree that your contributions will be licensed
under the project's [MIT license](LICENSE).
