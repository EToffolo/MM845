# Tutorial 1 — Setting Up Your Working Environment

**MM845 — Tópicos de Geometria III: AI for Geometry**
Paired with **Lecture 1: Coding Foundations — Python, Git & AI Agents**

---

## What this tutorial is for

By the end of this document you will have a working, *reproducible* computational
environment: a Python installation you control, an isolated environment for this
course, Jupyter for interactive experiments, VS Code as an editor, Git/GitHub for
version control, and at least one AI coding assistant.

**No prior programming experience is assumed.** Every command is given explicitly.
Where the three operating systems differ, all three are shown — read only your own.

> **Conventions used below**
> - Lines starting with `$` are typed into a *terminal* (do not type the `$`).
> - `<angle brackets>` mark something you must replace with your own value.
> - Anything marked **(optional)** can be skipped on a first pass.

---

## Contents

1. [Opening a terminal](#1-opening-a-terminal)
2. [Installing Python](#2-installing-python)
3. [Creating the course environment](#3-creating-the-course-environment)
4. [Jupyter notebooks](#4-jupyter-notebooks)
5. [Visual Studio Code](#5-visual-studio-code)
6. [Git and GitHub](#6-git-and-github)
7. [AI coding assistants](#7-ai-coding-assistants)
8. [Repository structure and reproducibility](#8-repository-structure-and-reproducibility)
9. [Troubleshooting](#9-troubleshooting)
10. [Final checklist](#10-final-checklist)
11. [(Optional) SageMath](#11-optional-sagemath)

---

## 1. Opening a terminal

The *terminal* (or *shell*, or *command line*) is a text interface to your computer.
You type a command, press <kbd>Enter</kbd>, and the computer executes it. Almost
everything below happens here.

| OS | How to open |
|---|---|
| **Linux** | <kbd>Ctrl</kbd>+<kbd>Alt</kbd>+<kbd>T</kbd>, or search "Terminal" in your applications menu. |
| **macOS** | <kbd>Cmd</kbd>+<kbd>Space</kbd>, type `Terminal`, press <kbd>Enter</kbd>. |
| **Windows** | Press <kbd>Win</kbd>, type `PowerShell`, press <kbd>Enter</kbd>. (After step 2 you will instead use the **Miniforge Prompt**.) |

Four commands are enough to navigate:

```bash
pwd                 # "print working directory" — where am I?
ls                  # list the files here   (Windows PowerShell: also `ls`)
cd <folder>         # enter a folder
cd ..               # go up one level
```

Try creating a folder for the course now — you will clone the repository into it later:

```bash
$ cd ~
$ mkdir -p MM845
$ cd MM845
```

(`~` means your home directory on all three systems.)

---

## 2. Installing Python

Python 3 may already exist on your machine, but you should **not** use the system
Python: modifying it can break your operating system's own tools. Instead we install
a self-contained distribution that also gives us the environment manager discussed
in Lecture 1.

We use **Miniforge**, a minimal installer for `conda` configured to use the
community `conda-forge` package repository. It is free, open-source, works
identically on all three operating systems, and — unlike full Anaconda — has no
commercial licensing restrictions for institutional use.

Download page (all installers): <https://github.com/conda-forge/miniforge#download>

### Linux

```bash
$ curl -L -O "https://github.com/conda-forge/miniforge/releases/latest/download/Miniforge3-$(uname)-$(uname -m).sh"
$ bash Miniforge3-$(uname)-$(uname -m).sh
```

Accept the licence, accept the default install location (`~/miniforge3`), and answer
**yes** when asked whether to initialise conda in your shell. Then close and reopen
the terminal.

### macOS

Identical to Linux (the `uname` calls pick the right installer for both Apple
Silicon and Intel machines):

```bash
$ curl -L -O "https://github.com/conda-forge/miniforge/releases/latest/download/Miniforge3-$(uname)-$(uname -m).sh"
$ bash Miniforge3-$(uname)-$(uname -m).sh
```

Close and reopen the terminal afterwards.

<details>
<summary>macOS alternative: Homebrew</summary>

If you already use [Homebrew](https://brew.sh):

```bash
$ brew install miniforge
$ conda init zsh
```
</details>

### Windows

1. Download `Miniforge3-Windows-x86_64.exe` from the
   [releases page](https://github.com/conda-forge/miniforge/releases/latest).
2. Run it. Choose **"Just Me"** and accept the default location.
3. When installation finishes, open **Miniforge Prompt** from the Start menu
   (search for "Miniforge"). **Use this prompt, not plain PowerShell,** for every
   `conda` command in this document.

### Verify

In a *newly opened* terminal:

```bash
$ conda --version
conda 24.x.x
```

If the command is not found, see [Troubleshooting](#9-troubleshooting).

---

## 3. Creating the course environment

An **environment** is an isolated Python installation with its own set of libraries
at pinned versions. Lecture 1's analogy: it is the computational counterpart of
fixing your conventions and axioms before writing a proof. Two projects needing
incompatible versions of a library can then coexist peacefully.

Create the environment for this course:

```bash
$ conda create -n aigeo python=3.12
$ conda activate aigeo
```

Your prompt should now be prefixed with `(aigeo)`. **You must run `conda activate
aigeo` in every new terminal** before working on the course.

### Install the scientific stack

```bash
$ conda install -c conda-forge numpy scipy matplotlib pandas scikit-learn sympy networkx jupyterlab ipykernel
```

Then install PyTorch (the deep-learning library used from Lecture 3 onwards). It is
distributed separately because it must match your hardware:

```bash
# CPU-only — correct for laptops, and sufficient for this entire course
$ pip install torch torchvision
```

<details>
<summary>If you have an NVIDIA GPU (Linux/Windows)</summary>

Use the selector at <https://pytorch.org/get-started/locally/> to obtain the exact
command for your CUDA version, e.g.

```bash
$ pip install torch torchvision --index-url https://download.pytorch.org/whl/cu124
```

On Apple Silicon, the ordinary `pip install torch` already supports GPU
acceleration through the `mps` backend — nothing extra is needed.
</details>

### Verify the installation

```bash
$ python -c "import numpy, torch; print(numpy.__version__, torch.__version__)"
```

You should see two version numbers and no error.

### Record the environment

Reproducibility requires that a colleague can recreate exactly what you ran. Export
the specification into a file that lives in your repository:

```bash
$ conda env export --from-history > environment.yml
```

Anyone can then rebuild it with `conda env create -f environment.yml`.

> **Useful conda commands**
> | Command | Effect |
> |---|---|
> | `conda env list` | list all environments |
> | `conda activate <name>` | switch into an environment |
> | `conda deactivate` | leave the current environment |
> | `conda list` | list packages installed in the active environment |
> | `conda env remove -n <name>` | delete an environment |

---

## 4. Jupyter notebooks

A **notebook** is a document mixing executable code cells, their outputs (numbers,
plots, tables), and prose in Markdown/LaTeX. It is the natural medium for
experimental mathematics: you compute, look, adjust, and the whole record of what
you did is preserved. All tutorials in this course are distributed as notebooks.

### Launching

With `(aigeo)` active, from your course folder:

```bash
$ jupyter lab
```

A browser tab opens at `http://localhost:8888`. The terminal must stay open while
you work — it is running the server. Stop it with <kbd>Ctrl</kbd>+<kbd>C</kbd>.

Create a new notebook: **File → New → Notebook**, and choose the **Python 3
(ipykernel)** kernel.

### The essentials

A notebook is a sequence of **cells**. Each cell is either *Code* or *Markdown*.

| Action | Shortcut |
|---|---|
| Run cell, stay | <kbd>Ctrl</kbd>+<kbd>Enter</kbd> |
| Run cell, move to next | <kbd>Shift</kbd>+<kbd>Enter</kbd> |
| New cell below | <kbd>Esc</kbd> then <kbd>B</kbd> |
| New cell above | <kbd>Esc</kbd> then <kbd>A</kbd> |
| Delete cell | <kbd>Esc</kbd> then <kbd>D</kbd> <kbd>D</kbd> |
| Convert to Markdown | <kbd>Esc</kbd> then <kbd>M</kbd> |
| Convert to Code | <kbd>Esc</kbd> then <kbd>Y</kbd> |

Test it. Paste this into a code cell and run it — it samples a point cloud on
$S^2$, the running example of Tutorial 1:

```python
import numpy as np
import matplotlib.pyplot as plt

rng = np.random.default_rng(seed=0)          # fixed seed => reproducible

def sample_sphere(n, rng):
    """Sample n points uniformly on the unit sphere S^2 in R^3."""
    x = rng.normal(size=(n, 3))              # isotropic Gaussian
    return x / np.linalg.norm(x, axis=1, keepdims=True)

X = sample_sphere(500, rng)
assert np.allclose(np.linalg.norm(X, axis=1), 1.0)   # sanity check

fig = plt.figure(figsize=(5, 5))
ax = fig.add_subplot(projection="3d")
ax.scatter(X[:, 0], X[:, 1], X[:, 2], s=6)
ax.set_box_aspect((1, 1, 1))
plt.show()
```

Note the two habits from Lecture 1 already present: an explicit **seed**, and an
**assertion** verifying a property you know must hold.

### The one trap to know about

A notebook's *cell order on screen* need not be its *execution order*. The numbers
in `In [7]:` tell you the true order. If results stop making sense, use
**Kernel → Restart Kernel and Run All Cells** to guarantee a clean linear run.
Always do this before committing a notebook or showing results to anyone.

> **Classic Jupyter Notebook.** `jupyter lab` is the modern interface and is
> recommended. If you prefer the older, simpler one, `pip install notebook` and run
> `jupyter notebook`. The file format (`.ipynb`) is identical.

---

## 5. Visual Studio Code

VS Code is a free editor from Microsoft. It gives you file browsing, an integrated
terminal, a debugger, native notebook support, Git integration, and — importantly
for this course — the AI assistants of §7, all in one window.

### Install

| OS | Method |
|---|---|
| **Linux** | Download the `.deb`/`.rpm` from <https://code.visualstudio.com/download>, or `sudo snap install code --classic`. |
| **macOS** | Download the `.zip` from the same page, unzip, drag **Visual Studio Code** into `/Applications`. Or `brew install --cask visual-studio-code`. |
| **Windows** | Download and run the **User Installer** from the same page. Tick *"Add to PATH"* when offered. |

### Extensions

Open the Extensions panel (<kbd>Ctrl</kbd>/<kbd>Cmd</kbd>+<kbd>Shift</kbd>+<kbd>X</kbd>)
and install:

- **Python** (`ms-python.python`) — language support, environment selection, debugging.
- **Jupyter** (`ms-toolsai.jupyter`) — run `.ipynb` notebooks inside the editor.

**(optional but recommended)**

- **Ruff** (`charliermarsh.ruff`) — fast linter/formatter, catches mistakes as you type.
- **GitLens** (`eamodio.gitlens`) — richer view of Git history.

### Point VS Code at the `aigeo` environment

This is the step most often missed, and the cause of most "but I installed it!"
errors.

1. Open your course folder: **File → Open Folder…**
2. Press <kbd>Ctrl</kbd>/<kbd>Cmd</kbd>+<kbd>Shift</kbd>+<kbd>P</kbd> to open the
   Command Palette.
3. Type `Python: Select Interpreter`, press <kbd>Enter</kbd>.
4. Choose the entry containing **`aigeo`**.

For notebooks, the kernel is chosen separately: open a `.ipynb` file and click the
kernel name at the **top right**, then select `aigeo`.

### Worth knowing

| Feature | How |
|---|---|
| Integrated terminal | <kbd>Ctrl</kbd>+<kbd>`</kbd> |
| Command Palette (everything lives here) | <kbd>Ctrl</kbd>/<kbd>Cmd</kbd>+<kbd>Shift</kbd>+<kbd>P</kbd> |
| Search across all files | <kbd>Ctrl</kbd>/<kbd>Cmd</kbd>+<kbd>Shift</kbd>+<kbd>F</kbd> |
| Source Control (Git) panel | <kbd>Ctrl</kbd>/<kbd>Cmd</kbd>+<kbd>Shift</kbd>+<kbd>G</kbd> |
| Run a Python file | <kbd>Ctrl</kbd>+<kbd>F5</kbd> |

---

## 6. Git and GitHub

**Git** tracks the history of a project as a directed acyclic graph of snapshots
(*commits*) — Lecture 1, slide 7. **GitHub** hosts those repositories online and
adds collaboration: pull requests, issues, code review.

### 6.1 Install Git

| OS | Command |
|---|---|
| **Linux (Debian/Ubuntu)** | `sudo apt update && sudo apt install git` |
| **Linux (Fedora)** | `sudo dnf install git` |
| **macOS** | `xcode-select --install` (installs Git with the developer tools), or `brew install git` |
| **Windows** | Download **Git for Windows** from <https://git-scm.com/download/win>. Accept the defaults; this also installs *Git Bash*. |

Verify:

```bash
$ git --version
git version 2.4x.x
```

### 6.2 Create a GitHub account

Do this before configuring Git, because the next step needs the email address you
register here.

Sign up at <https://github.com>. Use your **institutional address**
(`@unicamp.br`, `@ime.unicamp.br`, …) or add it later under
*Settings → Emails* — it is what makes you eligible for the free
[GitHub Student Developer Pack](https://education.github.com/pack), which in turn
gives free **GitHub Copilot** (§7.1).

Enable two-factor authentication when prompted; GitHub requires it.

> Git and GitHub are independent: Git is the version-control program on your
> machine, GitHub is a company hosting Git repositories. You can use Git with no
> account at all. The account matters from §6.4 onwards, when you start pushing
> your work somewhere other people (and your future self, on another machine) can
> reach it.

### 6.3 One-time configuration

Git stamps every commit with a name and email. Use the ones on the account you
just created — otherwise GitHub cannot match your commits to your profile, and
your contribution history will silently point at nobody:

```bash
$ git config --global user.name  "<Your Name>"
$ git config --global user.email "<you@example.com>"
$ git config --global init.defaultBranch main
$ git config --global pull.rebase false
```

Check what Git believes at any time with `git config --global --list`.

> **Privacy note.** Commit emails are public in a public repository. If you would
> rather not expose yours, GitHub can supply a no-reply address: *Settings →
> Emails → Keep my email addresses private* shows one of the form
> `12345678+username@users.noreply.github.com`. Use that as `user.email` instead.

### 6.4 Authenticate your machine

The simplest route is the official GitHub CLI, which handles credentials for you.

| OS | Install |
|---|---|
| **Linux** | See <https://github.com/cli/cli/blob/trunk/docs/install_linux.md>, or `sudo apt install gh` on recent Ubuntu. |
| **macOS** | `brew install gh` |
| **Windows** | `winget install --id GitHub.cli`, or the installer from <https://cli.github.com>. |

Then:

```bash
$ gh auth login
```

Choose **GitHub.com → HTTPS → Login with a web browser**, and follow the
instructions. From now on `git push` and `git pull` work without asking for a
password.

<details>
<summary>Alternative: SSH keys</summary>

```bash
$ ssh-keygen -t ed25519 -C "<you@example.com>"     # press Enter at every prompt
$ cat ~/.ssh/id_ed25519.pub                        # copy the whole line
```

Paste it at *GitHub → Settings → SSH and GPG keys → New SSH key*. Test with:

```bash
$ ssh -T git@github.com
```

Then use `git@github.com:...` URLs instead of `https://github.com/...`.
</details>

### 6.5 The everyday workflow

```bash
$ git clone <repository-url>       # download a repository (once)
$ cd <repository-name>

# ... edit files ...

$ git status                       # what has changed?
$ git diff                         # show me exactly what changed
$ git add <file>                   # stage a file for the next snapshot
$ git add .                        # stage everything changed
$ git commit -m "Add S^2 sampling and norm check"
$ git push                         # upload commits to GitHub
$ git pull                         # download collaborators' commits
```

Inspect history at any time:

```bash
$ git log --oneline --graph --all   # the DAG of snapshots, drawn
$ git show <commit-hash>            # what did that commit change?
```

### 6.6 Branches

A **branch** is an independent line of development — the co-authored-paper analogy
from Lecture 1. Do your work on a branch, keep `main` clean:

```bash
$ git switch -c tutorial-1          # create a branch and move onto it
# ... work, add, commit ...
$ git push -u origin tutorial-1     # publish the branch (first time only)

$ git switch main                   # go back
$ git merge tutorial-1              # bring the work in
```

`git branch` lists branches; `git switch <name>` moves between them.

### 6.7 Pull requests

On GitHub, a **pull request** (PR) proposes merging one branch into another and
displays the *diff* for review — refereeing, for code. After pushing a branch,
GitHub shows a *"Compare & pull request"* button. Or from the terminal:

```bash
$ gh pr create --fill
```

### 6.8 Do this now

1. Create a repository on GitHub named `mm845-<yourname>`, ticking *"Add a README"*.
2. Clone it, using the URL from the green **Code** button:
   ```bash
   $ cd ~/MM845
   $ git clone https://github.com/<your-username>/mm845-<yourname>.git
   $ cd mm845-<yourname>
   ```
3. Edit `README.md` — add your name and a line about what you want from the course.
4. Commit and push:
   ```bash
   $ git add README.md
   $ git commit -m "Add personal introduction"
   $ git push
   ```
5. Refresh the GitHub page and confirm your change is there.
6. Clone the **course repository** as well (URL given in class):
   ```bash
   $ cd ~/MM845
   $ git clone <course-repo-url>
   ```

### 6.9 What not to commit

Git is for *source*, not for generated output. Large binaries and data files bloat
the history permanently, and secrets committed once stay in the history forever.
Create a file named `.gitignore` at the root of your repository:

```gitignore
# Python
__pycache__/
*.py[cod]
.venv/
env/

# Jupyter
.ipynb_checkpoints/

# Data and results — regenerate these, do not version them
data/raw/
results/
*.npy
*.npz
*.pt
*.ckpt

# OS / editor
.DS_Store
.vscode/
.idea/

# Secrets — never commit these
.env
*.key
```

> Notebooks store their outputs inside the `.ipynb` file, which makes diffs noisy.
> Clearing outputs before committing (**Kernel → Restart Kernel and Clear All
> Outputs**) keeps history readable.

---

## 7. AI coding assistants

Lecture 1 makes the case: syntax is no longer the barrier to entry, so your
mathematical precision in *specifying* a problem becomes the valuable skill. Three
levels of autonomy were distinguished — autocomplete, chat, and agents.

**The course uses one assistant: GitHub Copilot.** It is free for verified students,
it lives inside the editor you set up in §5, and it provides all three levels of
autonomy. Set it up before Tutorial 2. Other agents exist and some will be
demonstrated in class (§7.2), but nothing in this course requires you to pay for
anything.

### 7.1 GitHub Copilot

Copilot lives inside VS Code and provides all three levels: inline completion, a
chat panel, and an agent mode that edits multiple files and runs commands.

1. **Get access.** Apply for the
   [Student Developer Pack](https://education.github.com/pack) with your Unicamp
   address. Verified students get the free **Copilot Student** plan — unlimited
   code completions, plus a monthly allowance of GitHub AI Credits for chat, agent
   mode, code review and the Copilot CLI. See the
   [current plans](https://docs.github.com/en/copilot/get-started/plans).

   **Apply early.** Verification takes days, occasionally longer, and GitHub has
   paused new Copilot sign-ups before (April–June 2026). Until it comes through
   you land on **Copilot Free**, which works with any GitHub account and is
   perfectly usable for this course — just with tighter limits.
2. **Install the extension.** In VS Code, search the Extensions panel for
   **GitHub Copilot** (`GitHub.copilot`) and install it. **GitHub Copilot Chat**
   comes with it.
3. **Sign in.** A prompt appears at the bottom right; otherwise click the account
   icon in the Activity Bar → *Sign in to GitHub*.
4. **Use it.**
   - *Inline*: start typing; grey ghost text is a suggestion — <kbd>Tab</kbd>
     accepts, <kbd>Esc</kbd> dismisses.
   - *Chat*: <kbd>Ctrl</kbd>/<kbd>Cmd</kbd>+<kbd>Alt</kbd>+<kbd>I</kbd>. Ask
     questions about the open file; `#file` and `#selection` add context.
   - *Inline edit*: <kbd>Ctrl</kbd>/<kbd>Cmd</kbd>+<kbd>I</kbd> on a selection,
     describe the change in words.
   - *Agent mode*: select **Agent** in the Chat panel's mode dropdown, then give a
     goal rather than an instruction.

### 7.2 Other agents — demonstrated, not required

[Claude Code](https://claude.com/claude-code) and
[OpenAI Codex](https://developers.openai.com/codex) are terminal agents in the same
family, and both will be shown in the tutorial sessions so that you can see what the
current state of the art looks like. **Both require a paid subscription**, neither
has a student free tier, and nothing in this course needs them. Do not buy anything
on the course's account.

### 7.3 Using it properly

This part matters more than the installation. From Lecture 1, slide 10:

**Do**
- Give precise context: definitions, conventions, expected input/output shapes.
- Work in small, verifiable steps, and **commit after each working step** — Git is
  your undo button when an agent goes astray.
- Ask for tests and sanity checks against cases where you know the answer.
- Ask it to *explain* its code, and read the explanation.

**Avoid**
- Accepting long generated code you have not read.
- Vague prompts (*"make it work"*).
- Letting an agent run without version control.
- Assuming numerical output is correct because the program did not crash.

**Prompt pattern** — *role + task + context + constraints + verification*:

> You are an expert mathematician assisting with differential geometry
> computations. Write a NumPy function sampling `N` points uniformly on
> $S^2 \subset \mathbb{R}^3$. Input: `N`, `seed`. Output: array of shape `(N, 3)`.
> Include a test verifying that all point norms equal 1.

Compare with *"give me points on a sphere"*, and note which one you could referee.

---

## 8. Repository structure and reproducibility

Use one repository per project, laid out predictably:

```
mm845-<yourname>/
├── README.md            # what this is, how to run it
├── environment.yml      # the pinned environment (§3)
├── .gitignore           # what Git should ignore (§6.9)
├── data/                # inputs; raw data ignored by Git, generators committed
├── src/                 # reusable functions and modules
├── notebooks/           # exploratory work, one notebook per tutorial
└── results/             # figures, tables, trained models (ignored by Git)
```

The standard set in Lecture 1: **a colleague should be able to clone your repository
and reproduce your results in two actions** — create the environment, then run the
script.

Three things must be controlled for that to hold:

| Ingredient | How |
|---|---|
| **Code** | Git — commit early, commit often, with messages that say *why*. |
| **Environment** | `environment.yml`, kept up to date in the repository. |
| **Randomness** | Fix seeds explicitly; report behaviour averaged over several. |

Seeding, concretely:

```python
import numpy as np, torch, random

SEED = 0
random.seed(SEED)
np.random.seed(SEED)
torch.manual_seed(SEED)
rng = np.random.default_rng(SEED)   # preferred NumPy style: an explicit generator
```

---

## 9. Troubleshooting

**`conda: command not found`**
The installer did not modify your shell configuration, or you did not reopen the
terminal. Close and reopen it. If it persists:

```bash
$ ~/miniforge3/bin/conda init "$(basename "$SHELL")"
```

then reopen the terminal. On Windows, use the **Miniforge Prompt** rather than
PowerShell.

**`ModuleNotFoundError: No module named 'numpy'` (or similar)**
Almost always the wrong environment. Check:

```bash
$ conda env list          # is (aigeo) the active one?
$ which python            # Windows: where python — is it inside miniforge3/envs/aigeo?
```

In VS Code, re-run `Python: Select Interpreter`. In a notebook, check the kernel
name at the top right.

**A notebook cannot see the `aigeo` environment**
Register the kernel explicitly:

```bash
$ conda activate aigeo
$ python -m ipykernel install --user --name aigeo --display-name "Python (aigeo)"
```

**`git push` asks for a password and rejects it**
GitHub removed password authentication for Git. Use `gh auth login` (§6.4) or SSH.

**PowerShell refuses to run a script** (`running scripts is disabled`)

```powershell
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope CurrentUser
```

**Windows, generally**
If a tool works only under a Unix-like shell, install **WSL2** (Windows Subsystem
for Linux) — `wsl --install` in an *administrator* PowerShell, then reboot — and
follow the Linux instructions inside it.

**Merge conflict** (`CONFLICT (content): Merge conflict in ...`)
Git marks the disputed region in the file with `<<<<<<<`, `=======`, `>>>>>>>`.
Edit the file so it reads as you want, delete the markers, then:

```bash
$ git add <file>
$ git commit
```

Nothing is lost: `git merge --abort` returns you to the state before the merge.

---

## 10. Final checklist

Before Tutorial 2, confirm each of the following:

- [ ] A terminal opens and `conda --version` prints a version.
- [ ] `conda activate aigeo` works, and the prompt shows `(aigeo)`.
- [ ] `python -c "import numpy, scipy, matplotlib, sklearn, torch; print('ok')"` prints `ok`.
- [ ] `jupyter lab` opens in a browser and a code cell runs.
- [ ] The $S^2$ sampling snippet in §4 runs and produces a picture.
- [ ] VS Code opens your course folder and its interpreter is set to `aigeo`.
- [ ] `git --version` works and `git config --global user.name` returns your name.
- [ ] You have a GitHub account, and `gh auth status` reports you as logged in.
- [ ] You created, cloned, edited, committed, and pushed to `mm845-<yourname>`.
- [ ] You cloned the course repository.
- [ ] GitHub Copilot is installed in VS Code and responds (§7.1) — on the free
      student plan, or on Copilot Free while verification is pending.

If any box is unticked, bring it to the tutorial session — that is what it is for.

---

## 11. (Optional) SageMath

SageMath is a computer algebra system built on Python, with a great deal of
geometry and number theory available directly. Tutorial 1 uses it for constructing
charts and differential objects on manifolds. It is a large install; if it gives you
trouble, use the browser-based option and move on.

| Route | How |
|---|---|
| **conda** (recommended, Linux/macOS) | `conda create -n sage -c conda-forge sage python=3.12` — a *separate* environment from `aigeo`, since Sage pins many of its own dependencies. |
| **Linux** | `sudo apt install sagemath` (Debian/Ubuntu) — often an older version. |
| **macOS** | The conda route above, or the `.dmg` from <https://www.sagemath.org/download-mac.html>. |
| **Windows** | Install via WSL2 and follow the Linux instructions. |
| **No install at all** | <https://cocalc.com> runs Sage in the browser; free accounts suffice for the tutorials. |

Once installed, `sage -n jupyter` launches a Jupyter server with the Sage kernel
available.

---

## Further reading

- [Python tutorial (official)](https://docs.python.org/3/tutorial/) — the language itself.
- [NumPy: the absolute basics for beginners](https://numpy.org/doc/stable/user/absolute_beginners.html)
- [Pro Git](https://git-scm.com/book/en/v2) — chapters 1–3 cover everything used here, free online.
- [Matplotlib quick-start guide](https://matplotlib.org/stable/users/explain/quick_start.html)
- [PyTorch: Learn the Basics](https://pytorch.org/tutorials/beginner/basics/intro.html)
- [The Turing Way](https://the-turing-way.netlify.app/) — reproducible research practice, in depth.

---

*Questions and problems: raise them in the tutorial session, or open an issue on the
course repository.*
