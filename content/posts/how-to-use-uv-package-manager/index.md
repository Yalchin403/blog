+++
date = '2025-11-20T00:51:10+04:00'
draft = false
title = 'How to Use UV (Python Package Manager)'
+++

# **A Complete Guide to _uv_: The Modern Python Package & Project Manager**

Python developers have long relied on a mix of tools—`pip`, virtualenv, pip-tools, and Poetry—to manage environments, dependencies, locking, and packaging. Useful as they are, these tools come with overlapping responsibilities and less-than-ideal performance.

**uv** provides a single, unified, _extremely fast_ ecosystem inspired by Rust’s `cargo`. It consolidates package management, dependency resolution, environment creation, locking, building, and publishing into one clean tool.

---

## **Why Choose uv Over pip, pip-tools, or Poetry?**

### **1. Exceptional Speed**

uv is written in Rust and outperforms pip, Poetry, and pip-tools by a wide margin. Resolving, locking, installing, and creating virtual environments are all significantly faster.

### **2. One Tool Instead of Four**

uv replaces:

- `pip`
- `virtualenv`
- `pip-tools`
- Poetry’s environment and lock management

A single command-line interface covers all core workflows.

### **3. Cargo-Inspired Reliability**

uv adopts the proven patterns Rust developers know well:

- Deterministic locking
- Strict reproducibility
- Clear dependency grouping
- Uniform project layouts
- Predictable and repeatable builds

This brings a modern packaging philosophy to Python.

---

## **Key Highlights of uv**

- Extremely fast dependency resolution and installation
- Manages virtual environments automatically
- Cargo-style lock file (`uv.lock`)
- Zero-config project initialization
- Dependency groups for dev/test/docs
- Drop-in pip interface (`uv pip`)
- Build and publish from one tool
- First-class support for custom index URLs
- Multi-Python version support

---

## **Understanding `uv.lock` and Why It Belongs in Git**

`uv.lock` stores exact versions and hashes of all resolved dependencies.
It guarantees:

- Identical installations across all environments
- Reproducible CI/CD deployments
- Deterministic builds
- Hash-based verification for security

**Commit `uv.lock` to Git** to ensure stable and predictable builds for your entire team.

---

# **Installation**

```bash
curl -LsSf https://astral.sh/uv/install.sh | sh
```

Or via Homebrew:

```bash
brew install uv
```

---

# **Virtual Environment Management with uv**

One of uv’s strongest features is seamless virtual environment management.

### **Automatic Environment Handling**

If you install packages inside a project (not system-wide), uv automatically:

- Creates a `.venv/` directory inside the project
- Installs and removes packages inside this environment
- Activates the correct environment for all operations

This replaces the need for manual `python -m venv`, `.envrc`, or external scripts.

### **Creating a Virtual Environment Manually**

Create `.venv` using the default Python:

```bash
uv venv
```

Specify a custom environment name:

```bash
uv venv my-name
```

Request a specific Python version:

```bash
uv venv --python 3.11
```

This integrates cleanly with dependency installation via `uv add`, making the entire workflow consistent and predictable.

---

# **Initialize a New Project**

```bash
uv init myproject
cd myproject
```

This creates `pyproject.toml`, `uv.lock`, and a standard project structure.

---

# **Migrating an Existing Project to uv**

If your project already has a `pyproject.toml`, uv simply adopts it.

### **From `requirements.txt`**

```bash
uv add -r requirements.txt -c constraints.txt
```

All dependencies are transferred into the project configuration and locked.

---

# **Installing Packages with `uv add`**

Install a package:

```bash
uv add requests
```

### **Dependency Groups (dev, test, docs, etc.)**

```bash
uv add pytest --group dev
```

uv updates `pyproject.toml` and regenerates the lock file automatically.

---

# **Removing Dependencies**

```bash
uv remove requests
```

The package is removed from both project config and lock file.

---

# **Upgrading Packages**

Upgrade a specific package:

```bash
uv lock --upgrade-package requests
```

Upgrade everything:

```bash
uv lock --upgrade
```

---

# **Using uv’s pip-Compatible Interface**

uv provides a fast pip-like interface:

```bash
uv pip install numpy
uv pip uninstall numpy
```

### **Compiling Requirements Files**

Replacement for `pip-compile`:

```bash
uv pip compile pyproject.toml -o requirements.txt
```

This is useful when exporting for older systems.

---

# **Building Your Project**

```bash
uv build
```

Produces:

- source distribution (`sdist`)
- wheel (`.whl`)

Outputs are placed in `dist/`.

---

# **Publishing Your Package**

Publish to PyPI:

```bash
uv publish
```

Credentials are taken from `.pypirc`.

---

# **Exporting Dependencies**

Export standard requirements:

```bash
uv export -o requirements.txt
```

Export dev dependencies:

```bash
uv export --group dev -o requirements-dev.txt
```

---

# **Working with Alternative Indexes: `UV_INDEX_URL`**

Some environments require private or alternative package indexes.

Use:

```bash
export UV_INDEX_URL="https://my.private.index/simple"
```

Or inline:

```bash
UV_INDEX_URL=https://custom/simple uv add internal-package
```

uv will install exclusively from the provided index unless configured otherwise.


Thanks for reading it!