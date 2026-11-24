# Setting up Python and Qiskit

In this course we program quantum computers in Python using [Qiskit](https://www.ibm.com/quantum/qiskit), the most widely used quantum computing framework. With Qiskit, the same code runs on a simulator on your laptop and on real quantum computers from IBM and Quantum Inspire (see [Running on real quantum computers](hardwareAccess.md)).

We manage Python and all the packages with **[uv](https://docs.astral.sh/uv/)**. uv is a fast, modern Python package manager. With a single command it installs the right version of Python and *exactly* the package versions used to write the course notebooks, so everyone's code behaves the same.

## 1. Install uv

::::{tab-set}
:::{tab-item} macOS / Linux
Open a terminal and run
```bash
curl -LsSf https://astral.sh/uv/install.sh | sh
```
:::
:::{tab-item} Windows
Open PowerShell and run
```powershell
powershell -ExecutionPolicy ByPass -c "irm https://astral.sh/uv/install.ps1 | iex"
```
:::
::::

Close and re-open your terminal, then check it worked with `uv --version`. You don't need to install Python separately, because uv does it for you.

## 2. Download the course code

Download the course repository as a zip file, which contains all the notebooks and the environment definition:

**[⬇ Download the course code (zip)](https://github.com/UoN-PHYS4041/UoN-PHYS4041.github.io/archive/refs/heads/main.zip)**

Unzip it somewhere sensible. If you use git, you can clone it instead:

```bash
git clone https://github.com/UoN-PHYS4041/UoN-PHYS4041.github.io.git
```

The two files that matter for setup are `pyproject.toml`, which lists the packages we need, and `uv.lock`, which pins their exact versions. The notebooks are in the `notebooks/` folder.

## 3. Create the environment

In a terminal, go into the unzipped folder and run

```bash
uv sync
```

The first time, this downloads Python 3.12 and all the packages into a folder called `.venv` inside the course folder. This takes a few minutes, mainly for PyTorch. After that it's instant.

## 4. Run the notebooks

**JupyterLab**: from the course folder, run
```bash
uv run jupyter lab
```
and open the `notebooks/` folder in the browser window that appears.

**VS Code**: open the course folder in VS Code (with the Python and Jupyter extensions installed), open a notebook, click *Select Kernel* (top right) and choose the `.venv` environment.

`uv run <command>` runs any command inside the course environment. For example, `uv run python my_script.py`.

## Adding packages for your project

If your project needs an extra package, add it with
```bash
uv add <package-name>
```
This installs it and records it in `pyproject.toml` and `uv.lock`. Commit both files so the rest of your group gets the same environment by running `uv sync`.

## Running on the HPC

The course notebooks are sized to run in a few minutes on a laptop. For your project you'll probably want bigger simulations: more qubits, more training data, more repeats for error bars. Run those on the university's HPC rather than your laptop.

uv works on Linux clusters without admin rights, and `uv.lock` means you get *exactly* the same package versions as on your laptop:

```bash
# on an HPC login node, once:
curl -LsSf https://astral.sh/uv/install.sh | sh
git clone https://github.com/UoN-PHYS4041/UoN-PHYS4041.github.io.git phys4041   # or your group's project repository
cd phys4041
uv sync
```

Then, inside a batch job, run your script or notebook with `uv run`:

```bash
uv run python my_simulation.py
uv run jupyter nbconvert --to notebook --execute my_notebook.ipynb
```

Long training runs are better as plain Python scripts than as notebooks. Save results to files (e.g. with `np.save`) and make plots afterwards. Qiskit Aer automatically uses all the CPU cores your job is given.

## Troubleshooting

- **`uv: command not found`**: close and re-open the terminal after installing uv.
- **The notebook can't find `qiskit`**: you're using the wrong kernel. Select the `.venv` kernel of the course folder.
- **Something is broken**: delete the `.venv` folder and run `uv sync` again.

```{note}
You're free to use another setup, such as conda or plain pip. The list of packages is in `pyproject.toml`. We can only offer support for the uv setup described here, though.
```
