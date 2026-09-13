# Python Project Template Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Create a production-ready, minimalist Python project template repository (`python-template`) using `uv`, `ruff`, `pre-commit`, `pytest`, `pip-audit`, a feature-complete `Makefile` with a renaming helper, and GitHub Actions CI.

**Architecture:** Minimalist `src/` layout (`src/app_name/`) with PEP 621 declarative configuration in `pyproject.toml` using `hatchling.build`. Automation through `Makefile` and GitHub Actions ensures parity between local checks and continuous integration. A helper script enables one-command project renaming without manual search-and-replace.

**Tech Stack:** Python 3.12, `uv`, `ruff`, `pytest`, `pre-commit`, `pip-audit`, `hatchling`, GitHub Actions.

**Spec:** `/home/carvalhocaio/www/python-template/docs/superpowers/specs/2026-09-13-python-template-design.md`

## Global Constraints

- Working directory for all tasks: `/home/carvalhocaio/www/python-template`
- Python version floor: `>=3.12` pinned in `.python-version` as `3.12`
- Code formatting line length: `88` characters
- All dev dependencies managed in `[dependency-groups] dev` within `pyproject.toml`
- No unnecessary runtime dependencies or files (minimalist layout)
- Every task must be verified with its specified command before moving forward

---

### Task 1: Environment & Packaging Scaffolding (`.python-version`, `.gitignore`, `pyproject.toml`, `uv.lock`)

**Files:**
- Create: `/home/carvalhocaio/www/python-template/.python-version`
- Create: `/home/carvalhocaio/www/python-template/.gitignore`
- Create: `/home/carvalhocaio/www/python-template/pyproject.toml`
- Test: `/home/carvalhocaio/www/python-template/uv.lock`

**Interfaces:**
- Consumes: None
- Produces: Valid `uv` environment with locked dependencies, `[tool.ruff]` and `[tool.pytest.ini_options]` configs.

- [ ] **Step 1: Create `.python-version`**

Write `3.12` to `/home/carvalhocaio/www/python-template/.python-version`.

- [ ] **Step 2: Create `.gitignore`**

Create comprehensive `.gitignore` covering Python byte-code, virtual environments, tooling caches (`.ruff_cache`, `.pytest_cache`), environment files, and system/editor artifacts.

```gitignore
# Byte-compiled / optimized / DLL files
__pycache__/
*.py[cod]
*$py.class

# Distribution / packaging
dist/
build/
*.egg-info/

# Virtual environments
.venv/

# Environment variables
.env
.env.*
!.env.example

# Tool caches
.ruff_cache/
.pytest_cache/
.coverage
htmlcov/

# Database
*.sqlite3

# IDE / Editor files
.idea/
.vscode/
*.swp
*.swo

# OS files
.DS_Store
Thumbs.db
```

- [ ] **Step 3: Create `pyproject.toml`**

Define project metadata, build backend, empty runtime dependencies, `dev` dependency group, Ruff lint/format options, and pytest settings.

```toml
[project]
name = "python-template"
version = "0.1.0"
description = "Starter template for modern Python projects"
readme = "README.md"
requires-python = ">=3.12"
dependencies = []

[build-system]
requires = ["hatchling"]
build-backend = "hatchling.build"

[dependency-groups]
dev = [
    "pip-audit>=2.10.0",
    "pre-commit>=4.0.0",
    "pytest>=8.4.0",
    "ruff>=0.15.0",
]

[tool.ruff]
line-length = 88
target-version = "py312"
src = ["src", "tests"]

[tool.ruff.lint]
select = [
    "E",    # pycodestyle errors
    "W",    # pycodestyle warnings
    "F",    # pyflakes
    "I",    # isort
    "UP",   # pyupgrade
    "B",    # flake8-bugbear
    "SIM",  # flake8-simplify
    "RUF",  # ruff-specific rules
    "C4",   # flake8-comprehensions
    "RET",  # flake8-return
    "TID",  # flake8-tidy-imports
]

[tool.ruff.format]
quote-style = "double"
indent-style = "space"
line-ending = "auto"

[tool.pytest.ini_options]
testpaths = ["tests"]
addopts = "-q"
```

- [ ] **Step 4: Run `uv sync` to install dependencies and generate `uv.lock`**

Run: `uv sync` in `/home/carvalhocaio/www/python-template`
Expected: Successfully synced dev dependencies; `uv.lock` generated.

- [ ] **Step 5: Commit**

```bash
git -C /home/carvalhocaio/www/python-template add .python-version .gitignore pyproject.toml uv.lock
git -C /home/carvalhocaio/www/python-template commit -m "chore: scaffold base packaging, gitignore and uv environment"
```

---

### Task 2: Minimal Source Package and Smoke Test

**Files:**
- Create: `/home/carvalhocaio/www/python-template/src/app_name/__init__.py`
- Create: `/home/carvalhocaio/www/python-template/src/app_name/py.typed`
- Create: `/home/carvalhocaio/www/python-template/tests/__init__.py`
- Create: `/home/carvalhocaio/www/python-template/tests/test_smoke.py`

**Interfaces:**
- Consumes: `pyproject.toml` and `pytest` from Task 1.
- Produces: `app_name.__version__ == "0.1.0"`, passing smoke test suite.

- [ ] **Step 1: Create failing test `tests/test_smoke.py` and `tests/__init__.py`**

Write `tests/__init__.py` (empty) and `tests/test_smoke.py`:

```python
import app_name


def test_version() -> None:
    assert app_name.__version__ == "0.1.0"
```

- [ ] **Step 2: Run pytest to verify failure**

Run: `uv run pytest` in `/home/carvalhocaio/www/python-template`
Expected: FAIL with `ModuleNotFoundError: No module named 'app_name'`

- [ ] **Step 3: Implement minimal `src/app_name/__init__.py` and `py.typed`**

Create directory `/home/carvalhocaio/www/python-template/src/app_name`.
Write `src/app_name/py.typed` (empty file).
Write `src/app_name/__init__.py`:

```python
"""Application package."""

__version__ = "0.1.0"
```

- [ ] **Step 4: Re-sync package in editable mode and run pytest**

Run: `uv sync && uv run pytest`
Expected: 1 test passed (`tests/test_smoke.py:test_version PASSED`).

- [ ] **Step 5: Run ruff check and format check**

Run: `uv run ruff check . && uv run ruff format --check .`
Expected: All checks passed without errors.

- [ ] **Step 6: Commit**

```bash
git -C /home/carvalhocaio/www/python-template add src/ tests/
git -C /home/carvalhocaio/www/python-template commit -m "feat: add initial minimalist app_name package and smoke test"
```

---

### Task 3: Pre-commit Configuration (`.pre-commit-config.yaml`)

**Files:**
- Create: `/home/carvalhocaio/www/python-template/.pre-commit-config.yaml`

**Interfaces:**
- Consumes: Codebase from Tasks 1 and 2.
- Produces: Fully functional pre-commit pipeline covering ruff and essential hygiene hooks.

- [ ] **Step 1: Create `.pre-commit-config.yaml`**

```yaml
repos:
  - repo: https://github.com/astral-sh/ruff-pre-commit
    rev: v0.15.22
    hooks:
      - id: ruff-check
        args: [--fix]
      - id: ruff-format

  - repo: https://github.com/pre-commit/pre-commit-hooks
    rev: v6.0.0
    hooks:
      - id: trailing-whitespace
      - id: end-of-file-fixer
      - id: check-yaml
      - id: check-toml
      - id: check-merge-conflict
      - id: check-added-large-files
      - id: detect-private-key
```

- [ ] **Step 2: Run pre-commit across all files to verify hooks**

Run: `uv run pre-commit run --all-files` in `/home/carvalhocaio/www/python-template`
Expected: All hooks pass (`Passed`).

- [ ] **Step 3: Commit**

```bash
git -C /home/carvalhocaio/www/python-template add .pre-commit-config.yaml
git -C /home/carvalhocaio/www/python-template commit -m "chore: add pre-commit configuration with ruff and hygiene hooks"
```

---

### Task 4: Makefile and Project Renaming Automation

**Files:**
- Create: `/home/carvalhocaio/www/python-template/scripts/rename.py`
- Create: `/home/carvalhocaio/www/python-template/Makefile`

**Interfaces:**
- Consumes: `uv`, `ruff`, `pytest`, `pip-audit`, `pre-commit`.
- Produces: Complete CLI entry points via `make` and one-step project renaming via `make rename NAME=<new_name>`.

- [ ] **Step 1: Write the renaming helper `scripts/rename.py`**

The renaming script will:
- Parse and validate `new_name` (must not be empty, only letters, numbers, hyphens, and underscores).
- Convert to distribution name (`kebab-case` or original) and Python module name (`snake_case`).
- Locate current package directory under `src/` (detects `app_name` or any existing package folder).
- Rename `src/<old_pkg>` to `src/<new_pkg>`.
- Update `name = "..."` in `pyproject.toml`.
- Update imports in `tests/test_smoke.py`.
- Re-run `uv sync` to update the virtual environment editable install.
- Print clear next steps.

```python
#!/usr/bin/env python3
"""Utility script to rename the project and source package."""

import re
import shutil
import subprocess
import sys
from pathlib import Path


def to_valid_identifier(name: str) -> str:
    cleaned = re.sub(r"[^a-zA-Z0-9_]", "_", name)
    cleaned = re.sub(r"_+", "_", cleaned).strip("_")
    if not cleaned or cleaned[0].isdigit():
        cleaned = f"pkg_{cleaned}"
    return cleaned.lower()


def rename_project(raw_name: str) -> None:
    root_dir = Path(__file__).resolve().parent.parent
    dist_name = raw_name.strip()
    if not dist_name:
        print("Error: Project name cannot be empty.", file=sys.stderr)
        sys.exit(1)

    module_name = to_valid_identifier(dist_name)

    src_dir = root_dir / "src"
    current_pkgs = [
        p for p in src_dir.iterdir() if p.is_dir() and (p / "__init__.py").exists()
    ]
    if not current_pkgs:
        print("Error: Could not find current package in src/", file=sys.stderr)
        sys.exit(1)

    old_pkg_dir = current_pkgs[0]
    old_module_name = old_pkg_dir.name
    new_pkg_dir = src_dir / module_name

    print(f"Renaming module '{old_module_name}' -> '{module_name}'...")
    if old_pkg_dir != new_pkg_dir:
        shutil.move(str(old_pkg_dir), str(new_pkg_dir))

    # Update pyproject.toml
    pyproject_file = root_dir / "pyproject.toml"
    if pyproject_file.exists():
        content = pyproject_file.read_text(encoding="utf-8")
        updated = re.sub(
            r'name\s*=\s*"[^"]+"',
            f'name = "{dist_name}"',
            content,
            count=1,
        )
        pyproject_file.write_text(updated, encoding="utf-8")
        print(f"Updated pyproject.toml project name to '{dist_name}'.")

    # Update tests/test_smoke.py
    smoke_test = root_dir / "tests" / "test_smoke.py"
    if smoke_test.exists():
        content = smoke_test.read_text(encoding="utf-8")
        content = content.replace(f"import {old_module_name}", f"import {module_name}")
        content = content.replace(
            f"{old_module_name}.__version__", f"{module_name}.__version__"
        )
        smoke_test.write_text(content, encoding="utf-8")
        print(f"Updated tests/test_smoke.py to import '{module_name}'.")

    # Sync uv
    print("Re-syncing virtual environment...")
    subprocess.run(["uv", "sync"], cwd=root_dir, check=True)
    print(
        f"\nProject successfully renamed to '{dist_name}' (package: '{module_name}')!"
    )


if __name__ == "__main__":
    if len(sys.argv) < 2:
        print("Usage: python scripts/rename.py <new_project_name>")
        sys.exit(1)
    rename_project(sys.argv[1])
```

- [ ] **Step 2: Create `Makefile`**

```makefile
.PHONY: help sync install hooks hooks-run test lint lint-fix format format-check audit ci check clean rename

help: ## Lists all available Makefile commands
	@grep -E '^[a-zA-Z_-]+:.*?## .*$$' $(MAKEFILE_LIST) | sort | awk 'BEGIN {FS = ":.*?## "}; {printf "\033[36m%-16s\033[0m %s\n", $$1, $$2}'

sync: ## Installs runtime and dev dependencies using uv
	uv sync

install: sync ## Alias for sync

hooks: ## Installs the pre-commit hooks into .git/hooks
	uv run pre-commit install

hooks-run: ## Runs all pre-commit hooks against all files
	uv run pre-commit run --all-files

test: ## Runs the test suite with pytest
	uv run pytest

lint: ## Checks code with ruff
	uv run ruff check .

lint-fix: ## Automatically fixes ruff lint issues
	uv run ruff check --fix .

format: ## Formats code with ruff
	uv run ruff format .

format-check: ## Verifies formatting with ruff without modifying files
	uv run ruff format --check .

audit: ## Audits dependencies for known security vulnerabilities
	uv run pip-audit

ci: lint format-check audit test ## Runs full verification pipeline locally

check: ci ## Alias for ci

clean: ## Cleans build artifacts and caches
	rm -rf .ruff_cache .pytest_cache dist build *.egg-info
	find . -type d -name '__pycache__' -exec rm -rf {} +

rename: ## Renames the project package: make rename NAME=my_new_project
	@if [ -z "$(NAME)" ]; then \
		echo "Error: NAME is required. Example: make rename NAME=my-project"; \
		exit 1; \
	fi
	uv run python scripts/rename.py "$(NAME)"
```

- [ ] **Step 3: Test Makefile commands**

Run in `/home/carvalhocaio/www/python-template`:
1. `make help` -> Expected: Displays formatted list of targets.
2. `make lint` -> Expected: All lint checks pass.
3. `make format-check` -> Expected: All format checks pass.
4. `make test` -> Expected: Smoke test passes.
5. `make audit` -> Expected: No vulnerabilities found.
6. `make ci` -> Expected: All steps pass.

- [ ] **Step 4: Commit**

```bash
git -C /home/carvalhocaio/www/python-template add scripts/rename.py Makefile
git -C /home/carvalhocaio/www/python-template commit -m "feat: add Makefile automation and project rename helper"
```

---

### Task 5: GitHub Actions Continuous Integration Workflow

**Files:**
- Create: `/home/carvalhocaio/www/python-template/.github/workflows/ci.yml`

**Interfaces:**
- Consumes: Makefile targets and `uv.lock`.
- Produces: Automated GitHub Actions CI workflow matching local `make ci`.

- [ ] **Step 1: Create `.github/workflows/ci.yml`**

```yaml
name: CI

on:
  push:
    branches: [main, master]
  pull_request:
    branches: [main, master]

concurrency:
  group: ${{ github.workflow }}-${{ github.ref }}
  cancel-in-progress: true

jobs:
  check:
    name: Lint, Audit and Test
    runs-on: ubuntu-latest

    steps:
      - name: Check out repository
        uses: actions/checkout@v4

      - name: Install uv
        uses: astral-sh/setup-uv@v5
        with:
          enable-cache: true

      - name: Set up Python
        run: uv python install 3.12

      - name: Install dependencies
        run: uv sync

      - name: Check code linting
        run: uv run ruff check .

      - name: Check code formatting
        run: uv run ruff format --check .

      - name: Audit dependencies
        run: uv run pip-audit

      - name: Run tests
        run: uv run pytest
```

- [ ] **Step 2: Validate workflow syntax with pre-commit hook**

Run: `uv run pre-commit run check-yaml --all-files` in `/home/carvalhocaio/www/python-template`
Expected: `check-yaml ....................................................Passed`

- [ ] **Step 3: Commit**

```bash
git -C /home/carvalhocaio/www/python-template add .github/workflows/ci.yml
git -C /home/carvalhocaio/www/python-template commit -m "ci: add GitHub Actions workflow for lint, audit and tests"
```

---

### Task 6: Documentation and Quickstart Guide (`README.md`)

**Files:**
- Create: `/home/carvalhocaio/www/python-template/README.md`

**Interfaces:**
- Consumes: Entire template specification.
- Produces: User-facing guide on how to clone/use the template, rename, and run development commands.

- [ ] **Step 1: Create `README.md`**

```markdown
# python-template

A minimalist, modern Python project template preconfigured with:
- **Python 3.12+** and packaging via PEP 621 (`pyproject.toml` + `hatchling`)
- **[uv](https://github.com/astral-sh/uv)** for fast package and virtual environment management
- **[Ruff](https://github.com/astral-sh/ruff)** for linting and formatting (PEP 8 compliant, 88 columns)
- **[Pre-commit](https://pre-commit.com/)** git hooks for code hygiene and security
- **[Pytest](https://pytest.org/)** test runner with smoke test
- **[pip-audit](https://github.com/pypa/pip-audit)** for dependency vulnerability scanning
- **Idiomatic Makefile** for development workflow automation
- **GitHub Actions CI** matching local checks

---

## 🚀 Quickstart

### 1. Using this template

Click **"Use this template"** on GitHub or clone the repository:

```bash
git clone https://github.com/<username>/<repo-name>.git
cd <repo-name>
```

### 2. Rename the project

Run the renaming helper to configure your package name and update `pyproject.toml` and tests:

```bash
make rename NAME=my-new-project
```

### 3. Install dependencies and git hooks

```bash
make sync
make hooks
```

---

## 🛠️ Available Commands

| Command | Description |
|---|---|
| `make help` | Show all available commands |
| `make sync` | Install runtime and dev dependencies using `uv` |
| `make hooks` | Install pre-commit hooks into `.git/hooks` |
| `make hooks-run` | Run pre-commit checks on all files |
| `make test` | Run tests with `pytest` |
| `make lint` | Check code with `ruff` |
| `make lint-fix` | Automatically fix linting issues |
| `make format` | Format code with `ruff` |
| `make format-check` | Check code formatting without modifying |
| `make audit` | Audit dependencies for vulnerabilities with `pip-audit` |
| `make ci` | Run full verification pipeline locally (`lint`, `format-check`, `audit`, `test`) |
| `make clean` | Remove caches and build artifacts |
| `make rename NAME=...` | Rename package and update configuration |

---

## 📁 Project Structure

```text
.
├── .github/workflows/ci.yml   # GitHub Actions CI workflow
├── src/
│   └── app_name/              # Source code directory (renamed via make rename)
│       ├── __init__.py
│       └── py.typed
├── tests/
│   ├── __init__.py
│   └── test_smoke.py          # Initial smoke test
├── .gitignore
├── .pre-commit-config.yaml
├── .python-version
├── Makefile
├── pyproject.toml
└── README.md
```
```

- [ ] **Step 2: Check formatting and lint on README and all files**

Run: `make ci && make hooks-run` in `/home/carvalhocaio/www/python-template`
Expected: All checks pass cleanly.

- [ ] **Step 3: Commit**

```bash
git -C /home/carvalhocaio/www/python-template add README.md
git -C /home/carvalhocaio/www/python-template commit -m "docs: add comprehensive README with quickstart and usage guide"
```

---

### Task 7: Full End-to-End Verification & Dry-run Rename Test

**Files:**
- Test verification across `/home/carvalhocaio/www/python-template`

**Interfaces:**
- Consumes: All components from Tasks 1-6.
- Produces: Verified repository ready to be pushed to GitHub as a template.

- [ ] **Step 1: Test the full pipeline on `python-template`**

Run: `make ci`
Expected: Zero lint errors, zero format warnings, zero vulnerabilities, 1 passing test.

- [ ] **Step 2: Test `make rename` in an isolated temporary copy**

Create a temporary copy in `/tmp/opencode/test-rename`, verify `make rename NAME=awesome-service` updates the package to `src/awesome_service`, updates `pyproject.toml` and `tests/test_smoke.py`, and `make ci` passes in the renamed project.

Commands:
```bash
cp -r /home/carvalhocaio/www/python-template /tmp/opencode/test-rename
cd /tmp/opencode/test-rename
make rename NAME=awesome-service
make ci
```
Expected: `make ci` in `/tmp/opencode/test-rename` exits with code 0 and passes all checks.
Cleanup:
```bash
rm -rf /tmp/opencode/test-rename
```

- [ ] **Step 3: Verify clean git status in `python-template`**

Run: `git -C /home/carvalhocaio/www/python-template status`
Expected: Clean working tree, nothing untracked or uncommitted.
