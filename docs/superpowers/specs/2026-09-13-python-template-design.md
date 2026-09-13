# Design Spec: Python Project Template (`python-template`)

- **Date:** 2026-09-13
- **Author:** Carvalho Caio & AI Assistant
- **Status:** Approved

---

## 1. Context and Goals

### 1.1 Context
Across recent Python projects in the workspace (such as `cotton-desk-tasks`, `cotton-claims-agent`, `redis-like-python`, `task-tracker`, and `cost-cap-tracker`), there is a consistent, modern Python ecosystem standard:
- Package and dependency management with `uv`.
- Code quality, formatting, and linting with `ruff` (88 columns, PEP 8 alignment, modern rules).
- Automated git hooks with `pre-commit`.
- Testing suite with `pytest`.
- Security auditing with `pip-audit`.
- Automation via idiomatic `Makefile`.
- Packaging via PEP 621 in `pyproject.toml` with `src/` layout.

### 1.2 Goals
Create a reusable, minimalist GitHub Template repository (`python-template`) that allows initiating new production-ready Python projects in seconds with:
1. Fast local development setup (`make sync`, `make test`, `make lint`, `make format`, `make audit`, `make ci`).
2. Minimalist starting structure: only `src/app_name/__init__.py` and `tests/__init__.py` + `tests/test_smoke.py`.
3. Automated renaming utility (`make rename NAME=novo_nome`) to rename the package folder and update references in `pyproject.toml`.
4. Automated Continuous Integration (CI) via GitHub Actions using `astral-sh/setup-uv`.

---

## 2. Repository Architecture & Layout

```text
python-template/
├── .github/
│   └── workflows/
│       └── ci.yml                 # GitHub Actions pipeline
├── src/
│   └── app_name/
│       └── __init__.py            # Declares __version__ = "0.1.0"
├── tests/
│   ├── __init__.py
│   └── test_smoke.py              # Smoke test validating package import & version
├── .gitignore                     # Python, uv, cache, IDE ignores
├── .pre-commit-config.yaml        # Ruff and hygiene pre-commit hooks
├── .python-version                # Pinned to 3.12
├── Makefile                       # Development task automation & rename helper
├── README.md                      # Usage guide and project setup instructions
└── pyproject.toml                 # PEP 621 metadata, dependencies & tool configs
```

---

## 3. Detailed Specifications

### 3.1 Python Version & Build Backend
- **Python Version:** `>=3.12` pinned in `.python-version` with `3.12`.
- **Build System:** `hatchling.build` (`hatchling` dependency in `[build-system]`).

### 3.2 Dependencies Configuration (`pyproject.toml`)
- **Runtime Dependencies:** `dependencies = []` (minimal base).
- **Development Dependencies (`[dependency-groups] dev`):**
  - `pip-audit>=2.10.0`
  - `pre-commit>=4.0.0`
  - `pytest>=8.4.0`
  - `ruff>=0.15.0`

### 3.3 Linting & Formatting (`[tool.ruff]`)
Configured directly in `pyproject.toml`:
- `line-length = 88`
- `target-version = "py312"`
- `src = ["src", "tests"]`
- Rules:
  - `E`, `W` (pycodestyle errors & warnings)
  - `F` (Pyflakes)
  - `I` (isort)
  - `UP` (pyupgrade)
  - `B` (flake8-bugbear)
  - `SIM` (flake8-simplify)
  - `RUF` (Ruff-specific rules)
  - `C4` (flake8-comprehensions)
  - `RET` (flake8-return)
  - `TID` (flake8-tidy-imports)

### 3.4 Testing Configuration (`[tool.pytest.ini_options]`)
- `testpaths = ["tests"]`
- `addopts = "-q"`
- Initial test file `tests/test_smoke.py`:
  ```python
  import app_name


  def test_version() -> None:
      assert app_name.__version__ == "0.1.0"
  ```

### 3.5 Pre-commit Hooks (`.pre-commit-config.yaml`)
- `astral-sh/ruff-pre-commit` (v0.15.22):
  - `ruff-check` with `args: [--fix]`
  - `ruff-format`
- `pre-commit/pre-commit-hooks` (v6.0.0):
  - `trailing-whitespace`
  - `end-of-file-fixer`
  - `check-yaml`
  - `check-toml`
  - `check-merge-conflict`
  - `check-added-large-files`
  - `detect-private-key`

### 3.6 Makefile Automation (`Makefile`)
Targets with self-documenting help (`make help`):
- `help`: Lists all available targets with descriptions.
- `sync`: `uv sync`
- `install`: Alias for `sync`.
- `hooks`: `uv run pre-commit install`
- `hooks-run`: `uv run pre-commit run --all-files`
- `test`: `uv run pytest`
- `lint`: `uv run ruff check .`
- `lint-fix`: `uv run ruff check --fix .`
- `format`: `uv run ruff format .`
- `format-check`: `uv run ruff format --check .`
- `audit`: `uv run pip-audit`
- `ci` / `check`: Runs `lint format-check audit test`.
- `clean`: Removes `.ruff_cache`, `.pytest_cache`, and `__pycache__` directories.
- `rename`: Utility script invoked via `make rename NAME=meu_projeto` or `make rename NAME=meu-projeto`:
  - Validates `NAME` argument.
  - Normalizes package name to valid Python module identifier (snake_case) for `src/<pkg_name>`.
  - Renames `src/app_name` to `src/<pkg_name>`.
  - Updates `name = "..."` in `pyproject.toml`.
  - Updates import in `tests/test_smoke.py`.

### 3.7 GitHub Actions CI (`.github/workflows/ci.yml`)
- Trigger: `push` and `pull_request` on `[main, master]`.
- Runner: `ubuntu-latest`.
- Steps:
  1. `actions/checkout@v4`
  2. `astral-sh/setup-uv@v5` with `enable-cache: true`.
  3. `uv python install 3.12`
  4. `uv sync`
  5. `uv run ruff check .`
  6. `uv run ruff format --check .`
  7. `uv run pip-audit`
  8. `uv run pytest`

### 3.8 `.gitignore`
Comprehensive ignores:
- `__pycache__/`, `*.py[cod]`, `*$py.class`
- `.venv/`
- `.ruff_cache/`
- `.pytest_cache/`
- `dist/`, `build/`, `*.egg-info/`
- `.env`, `.env.local`
- `.DS_Store`, `Thumbs.db`
- `.idea/`, `.vscode/` (optional editor artifacts)

### 3.9 Documentation (`README.md`)
Clear step-by-step instructions:
1. Using the template (via GitHub "Use this template" or git clone).
2. Renaming the project (`make rename NAME=novo_projeto`).
3. Quickstart: installing dependencies (`make sync`), installing hooks (`make hooks`).
4. Running checks (`make ci` or `make test` / `make lint`).

---

## 4. Verification and Acceptance Criteria
1. `uv sync` completes cleanly, generating a valid `uv.lock`.
2. `make lint` and `make format-check` succeed with zero errors.
3. `make test` runs and passes `tests/test_smoke.py`.
4. `make audit` passes with no vulnerabilities.
5. `make ci` runs the entire local pipeline successfully.
6. `make rename` successfully renames the module, updates files, and all tests/lint continue to pass.
