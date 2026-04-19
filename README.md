# backpressure

This package is under active development and **not yet ready for use**.

It exists on PyPI as a minimal placeholder to reserve the project name while the real implementation is prepared. Please do not depend on it in production.

## Development

This repo uses [uv](https://docs.astral.sh/uv/) and [hatchling](https://hatch.pypa.io/latest/config/build/) as the build backend. Local Python is pinned in `.python-version` (currently 3.14); `pyproject.toml` declares **`requires-python = ">=3.11"`** so CI can exercise 3.11 through 3.14.

- Create a venv and install the project in editable mode: `uv sync`
- Install dev tools (ruff, basedpyright, pytest): `uv sync --all-extras --dev`
- Build sdist and wheel into `dist/`: `uv build`
- Upload to PyPI (after `uv build`): `uv publish` — set `UV_PUBLISH_TOKEN` or pass `--token`

### Continuous integration

On every push and pull request to `main`, GitHub Actions runs `uv sync --locked --all-extras --dev`, **ruff**, **basedpyright**, and **pytest** on **Python 3.11–3.14**. The same workflow is also exposed as **`workflow_call`** for reuse from other workflows. Releases are not automated; publishing stays on your machine (or any environment where you set credentials).

PyPI ownership is independent of where the Git repository is hosted. After the name is claimed, you can move the project to another org or URL and update `Homepage` / `Repository` links in `pyproject.toml` (or on the PyPI project page) whenever you like.
