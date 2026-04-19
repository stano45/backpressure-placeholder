# backpressure

This package is under active development and **not yet ready for use**.

It exists on PyPI as a minimal placeholder to reserve the project name while the real implementation is prepared. Please do not depend on it in production.

## Development

This repo uses [uv](https://docs.astral.sh/uv/) and [hatchling](https://hatch.pypa.io/latest/config/build/) as the build backend. Local Python is pinned in `.python-version` (currently 3.14); `pyproject.toml` declares **`requires-python = ">=3.11"`** so CI can exercise 3.11 through 3.14.

- Create a venv and install the project in editable mode: `uv sync`
- Install dev tools (ruff, basedpyright, pytest): `uv sync --all-extras --dev`
- Build sdist and wheel into `dist/`: `uv build`

### Continuous integration

On every push and pull request to `main`, GitHub Actions runs `uv sync --locked --all-extras --dev`, **ruff**, **basedpyright**, and **pytest** on **Python 3.11–3.14**. The same workflow is exposed as **`workflow_call`** so **`.github/workflows/publish.yml`** can run checks before publishing.

### Publishing (PyPI trusted publisher, no API token)

Publishing runs only when you **publish a GitHub Release** (not on draft releases). The **`publish`** job uses OpenID Connect to PyPI—no `UV_PUBLISH_TOKEN` in GitHub secrets.

1. **PyPI — add a pending trusted publisher** (Account settings → Publishing → *Trusted publisher management* → *Add a new pending publisher* → GitHub):
   - **PyPI project name:** `backpressure` (must match `[project].name` in `pyproject.toml`).
   - **Owner:** your GitHub username or organization (the `github.com/<owner>/...` segment).
   - **Repository name:** this repo’s name (e.g. `backpressure-placeholder`).
   - **Workflow name:** `publish.yml`.
   - **Environment name:** if you use **`pypi`** here, it must match the GitHub Environment below. If you leave it **empty** on PyPI, remove the `environment:` block from the `publish` job in `.github/workflows/publish.yml` so the two match.

2. **GitHub — environment (only if you set “pypi” on PyPI)**  
   In the repo: **Settings → Environments → New environment** → name **`pypi`**. You do **not** need to add a PyPI token; optional protection rules (e.g. required reviewers) control who can publish.

3. **Repo — ship the workflows**  
   Ensure `main` contains `.github/workflows/ci.yml` and `.github/workflows/publish.yml` as in this repository.

4. **Version — align `pyproject.toml` and the release**  
   Set **`version`** in `pyproject.toml` to the version you are about to ship (e.g. `0.0.1`). Commit and push to `main`. PyPI will reject a version that already exists for that project.

5. **Tag and release**  
   Create a **git tag** on that commit (convention: `v0.0.1` matching the version). In GitHub: **Releases → Draft a new release**, choose the tag, set title/notes, then **Publish release**. That fires **`release: types: [published]`**, runs **`ci`** via `workflow_call`, then **`publish`** (`uv build --clear` and `uv publish --trusted-publishing always`).

6. **Verify**  
   Open `https://pypi.org/project/backpressure/` and confirm the new version appears.

**Optional — publish from your laptop instead:** after `uv build --clear`, use `uv publish` with a PyPI API token (`UV_PUBLISH_TOKEN` or `--token`). That path does not use the trusted publisher setup above.

PyPI ownership is independent of where the Git repository is hosted. After the name is claimed, you can move the project to another org or URL and update `Homepage` / `Repository` links in `pyproject.toml` (or on the PyPI project page) whenever you like.
