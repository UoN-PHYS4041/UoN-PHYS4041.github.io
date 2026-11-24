# PHYS4041: Introduction to Practical Quantum Computing

Source for the course website at <https://uon-phys4041.github.io>. It's built with [Jupyter Book 2](https://jupyterbook.org) (which is powered by [MyST](https://mystmd.org)), and Python is managed with [uv](https://docs.astral.sh/uv/).

## Layout

| Path | What it is |
|---|---|
| `myst.yml` | Book config: title, table of contents, bibliography, theme |
| `intro.md`, `content/outline/`, `content/lectures/` | Site pages (MyST markdown) |
| `content/images/` | Figures used in the notes |
| `content/downloads/` | Static downloads (LaTeX template) |
| `notebooks/` | Course code, developed here. Rendered as site pages **and** downloadable |
| `notebooks/projects/` | Starter notebooks for the four projects |
| `notebooks/data/` | Data files the notebooks need |
| `pyproject.toml`, `uv.lock` | Python environment. Plain `uv sync` installs the course packages; the `dev` group (`--all-groups`) adds the build tools |
| `.github/workflows/deploy.yml` | CI: builds on every push and deploys on `main` |

## One-off setup

```bash
# install uv (macOS/Linux; see the uv docs for Windows)
curl -LsSf https://astral.sh/uv/install.sh | sh
# Node.js >= 18 is also needed by Jupyter Book 2 (e.g. `brew install node`)

uv sync --all-groups   # creates .venv with course packages + website build tools, from uv.lock
```

## Everyday workflow

Work on the `dev` branch. Pushing `dev` runs a test build on GitHub but **doesn't** publish anything.

```bash
git switch dev
uv run jupyter book start          # live preview at http://localhost:3000 (auto-reloads)
uv run jupyter book build --html   # full static build into _build/html (what CI runs)
```

To publish, merge into `main` and push. GitHub Actions builds the site and deploys it:

```bash
git switch main
git merge dev
git push            # watch progress in the repo's "Actions" tab
git switch dev
```

The repo's Settings → Pages → Source must be set to **GitHub Actions**. This is a one-off setting.

### Manual fallback (no Actions)

If Actions isn't available, set Settings → Pages → Source back to *Deploy from a branch: `gh-pages`*, then run:

```bash
uv run jupyter book build --html
uv run ghp-import -n -p -f _build/html
```

## Notebooks

- Notebooks are committed **with outputs**, and the site shows those saved outputs. They are **not** re-run at build time, so the build stays fast and needs no quantum packages.
- After editing a notebook, re-run it top to bottom before committing:
  ```bash
  uv run jupyter nbconvert --to notebook --execute --inplace notebooks/04-vqe.ipynb
  ```
  You can also use **Restart & Run All** in Jupyter or VS Code. Select the `.venv` kernel.
- To add a notebook or page, create the file, then add it to the `toc:` in `myst.yml`.

## Python packages

```bash
uv add qiskit-something     # add a course package (updates pyproject.toml + uv.lock)
uv add --dev something      # add a build-only tool
uv lock --upgrade           # upgrade everything within the constraints, then re-run notebooks
```

Students set up the same environment from `pyproject.toml` + `uv.lock`. See `content/outline/pythonEnvironment.md`.

## Other tasks

- **Citations**: add BibTeX entries to `references.bib` and cite with `` {cite}`key` ``. MyST lists references at the bottom of each page automatically.
- **Figures**: use PNG or SVG, not PDF, because browsers can't show PDF figures. To convert a PDF: `pdftocairo -svg in.pdf out.svg`.
- **Clean build**: `uv run jupyter book clean --all`.
