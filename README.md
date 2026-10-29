<h1 align="center">
  <img src="https://github.com/goabonga/blog/raw/main/docs/blog.svg" alt="blog" width="120" /><br/>
  blog
</h1>

<p align="center">
  <em>Personal blog of goabonga, built with <a href="https://zensical.org">Zensical</a>.</em>
</p>

<p align="center">
  <a href="https://github.com/goabonga/blog/actions/workflows/ci.yml"><img src="https://github.com/goabonga/blog/actions/workflows/ci.yml/badge.svg?branch=main" alt="CI"/></a>
  <a href="https://github.com/goabonga/blog/blob/main/LICENSE"><img src="https://img.shields.io/badge/License-MIT-blue.svg" alt="License: MIT"/></a>
  <a href="https://github.com/astral-sh/uv"><img src="https://img.shields.io/endpoint?url=https://raw.githubusercontent.com/astral-sh/uv/main/assets/badge/v0.json" alt="uv"/></a>
</p>

## Documentation

The site is built from `docs/` and published from `main` to GitHub Pages:
<https://goabonga.github.io/blog/>.

## Requirements

- [uv](https://docs.astral.sh/uv/), which also provides Python 3.12+

## Getting started

```bash
git clone https://github.com/goabonga/blog.git
cd blog
uv sync --group docs
uv run pre-commit install
uv run zensical serve         # http://localhost:8000
```

Writing a post or a page is described in [CONTRIBUTING.md](CONTRIBUTING.md).

## Versioning and release

Versions are bumped from
[Conventional Commits](https://www.conventionalcommits.org/) by
[multicz](https://github.com/goabonga/multicz). On every push to `main`, CI
computes the bump, writes the changelog, tags, creates the GitHub Release with
an archive of the built site, and deploys the site when its content changed.
Maintainers do not bump versions or edit the changelog by hand.

## Contributing

See [CONTRIBUTING.md](CONTRIBUTING.md) for the workflow, the commit-message
convention, and the test/lint expectations. By participating you agree to the
[Code of Conduct](CODE_OF_CONDUCT.md).

Security issues: please follow the disclosure process in
[SECURITY.md](.github/SECURITY.md).

## License

Distributed under the [MIT License](LICENSE).
