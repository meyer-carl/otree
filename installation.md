---
layout: default
title: "Installing oTree"
permalink: /installation/
---

# Installing oTree with Miniconda

This guide shows you how to install oTree using Miniconda to keep everything cleanly isolated. Even with little or no programming experience, this guide should help you get oTree running within a few minutes.

---

## 1. Install Miniconda

Miniconda bundles Python plus the conda package manager in one simple installer. Download and install the **latest** version for your OS:

[https://www.anaconda.com/docs/getting-started/miniconda/install](https://www.anaconda.com/docs/getting-started/miniconda/install)

After installing, close and reopen your terminal and verify:

```bash
conda --version
# should print something like: conda 23.x.x
```

---

## 2. Create an oTree Environment

We’ll create a dedicated conda environment so oTree and its dependencies don’t interfere with anything else:

1. **Open your terminal.**  
2. **Create** a new environment named `otree_env`:
   ```bash
   conda create --name otree_env
   ```
3. **Activate** it:
   ```bash
   conda activate otree_env
   ```
4. **Update** Python if needed:
   ```bash
   conda update python
   ```
   More on managing Python versions:
   [https://docs.conda.io/projects/conda/en/stable/user-guide/tasks/manage-python.html](https://docs.conda.io/projects/conda/en/stable/user-guide/tasks/manage-python.html)

---

## 3. Install oTree

With `otree_env` active, run:

```bash
pip install otree
```

If that fails, try:

```bash
pip3 install otree
```

Then confirm:

```bash
otree --version
```

> **Tip:** Always activate `otree_env` (`conda activate otree_env`) before running `otree`.  
> Outside this env, the command won’t be found.

---

## 4. Deactivate Your Environment

When you’re done:

```bash
conda deactivate
```

If you ever forget your env’s name, list all envs:

```bash
conda env list
```

---

## 5. Setting Up VS Code

1. **Download & install** from [https://code.visualstudio.com/](https://code.visualstudio.com/)
2. **Open** VS Code → Extensions (⇧⌘X / Ctrl+Shift+X) → search “Python” → install Microsoft’s.  
3. **Select your interpreter**:
   - Open Command Palette (⇧⌘P / Ctrl+Shift+P) → **Python: Select Interpreter** → choose `otree_env`’s Python  
   - Or click the Python version in the bottom-right corner and pick the same.  
4. **Verify** in the integrated terminal that it shows `(otree_env)`.

---

## 6. Launch Your First oTree Project

1. **Clone or download** your oTree app folder (e.g. from Canvas).  
2. In that folder’s terminal:
   ```bash
   cd path/to/firstOtreeProject
   otree devserver
   ```
3. Open your browser to [http://localhost:8000](http://localhost:8000) → you should see the oTree dashboard.

---

## 7. Helpful Links

- oTree Documentation: [https://otree.readthedocs.io/en/latest/](https://otree.readthedocs.io/en/latest/)
- Conda Cheat Sheet: [https://docs.conda.io/projects/conda/en/latest/user-guide/cheatsheet.html](https://docs.conda.io/projects/conda/en/latest/user-guide/cheatsheet.html)
