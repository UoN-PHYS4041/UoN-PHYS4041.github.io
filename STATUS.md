# 2026–27 update: status

Working notes for the 2026–27 overhaul of the course site. This file is not part of the website (it is excluded in `myst.yml`). Last updated: 24 Nov 2026.

All work so far is on the **`dev`** branch. `main` (and the live site) still has the old Jupyter Book 1 version.

## Done (on `dev`)

- **Jupyter Book 2** (MyST): the book is at the repo root and configured by `myst.yml`. The old committed `_build/` is removed, and PDF figures are converted to SVG.
- **GitHub Actions** (`.github/workflows/deploy.yml`): builds on every push to `dev` and on pull requests (test only), and builds and deploys on push to `main`. Test builds on `dev` are passing.
- **uv** replaces conda: `pyproject.toml` + `uv.lock`. Students run `uv sync`; the maintainer runs `uv sync --all-groups`. See `README.md`.
- **Qiskit only** (PennyLane removed):
  - five course notebooks in `notebooks/` (Code 1–5), rendered as site pages with download buttons;
  - the MNIST notebook uses a batched parameter-shift gradient on the Aer simulator.
- **New pages:** "Running on real quantum computers" (IBM Classroom/Open Plan, Quantum Inspire) and a rewritten "Setting up Python and Qiskit" (uv and HPC).
- **Notes:**
  - all errors and typos found in the review are fixed (e.g. the parameter-shift sign, Lecture 6 encoding formulas, the Kronecker matrix);
  - Lecture 2's and Lecture 6's code sections are rewritten for Qiskit;
  - lectures are numbered in the navigation, and headings, figures and equations are numbered only within lectures.
- **Projects** (`content/outline/projects.md`):
  - four projects: VQE, QML classification, QNN architectures (quanvolution, with a QGAN or QCNN as extensions), and noise and error mitigation (aimed at QST students);
  - main tasks are standardised around four core elements: circuit design, measurement and uncertainty, optimisation and gradients, noise;
  - real hardware is a bonus, not a requirement;
  - a "How the paper is assessed" section is added; there are no per-group assigned settings.
- **Project starter notebooks** (`notebooks/projects/`): the infrastructure is provided, while the gradient methods and project-specific pieces are left as stubs with checks. All four were tested end-to-end against reference implementations.

## Outstanding

- [ ] **Dates:** update the 2026–27 lecture day, time, room, start date and paper deadline in `content/outline/courseStructure.md` and `content/outline/projects.md`. They currently say "Tuesday 10:00–12:00 … starting Friday 30th January" and "May 12th, 2026".
- [ ] **IBM Classroom account:** applied for (24 Nov 2026), awaiting approval.
  - Once approved, check that the steps under "Option A" in `content/outline/hardwareAccess.md` match the real invitation flow.
  - If it isn't approved in time, reword Option A and recommend the Open Plan instead.
- [ ] **Projects:** final review by the convenor. They aren't fully settled yet; open questions are whether the trainable filter should stay a main task in Project 3, and the overall workload for 10 credits.
- [ ] **Publish:**
  - In the repo settings, set Settings → Pages → Source to **GitHub Actions**.
  - Merge `dev` into `main` and push, then check the Actions run and the live site.
  - After that, the old `gh-pages` branch can be deleted.
- [ ] Optional: regenerate figures made with the old PennyLane code using the new notebooks: `BlochSphereRotations.svg` and `RotationOptimisationExample.png` (Lecture 3), and `GradientComparison.png` and `SPSA.png` (Lecture 5). `VQE.svg` (Lecture 4) contains real-device data from a previous year, so it is probably worth keeping.
- [ ] Optional: check the R = 15 pm row of `notebooks/data/HamiltonianParameters.txt` against Peruzzo et al. (2014) SI. It is the only row whose coefficients aren't symmetric, and probably a typo. The notebooks use all 9 columns, so they are correct either way.
- [ ] Optional: test the Quantum Inspire code on the hardware page with a real account. It was written from their documentation but hasn't been run.

## Notes

- IQM Resonance is mentioned on the hardware page but not supported in the code, because its Qiskit plugin didn't install alongside Qiskit 2.x.
- Students have HPC access. Notebooks are kept laptop-sized (a few minutes each) and point to the HPC for bigger runs.
- To re-run a notebook after editing: `uv run jupyter nbconvert --to notebook --execute --inplace <notebook>`. Code 5 (MNIST) takes about 6 minutes.
