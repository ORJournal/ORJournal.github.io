# Dependency Management in Python

Python offers several methods for specifying project dependencies, each suited for different use cases:

## 1. **requirements.txt**

The traditional and simplest approach:

```txt
requests==2.28.1
numpy>=1.20.0
pandas~=1.5.0
```

- Uses pip's format with version specifiers (`==`, `>=`, `~=`, `!=`)
- Common convention: `requirements.txt` for production, `requirements-dev.txt` for development dependencies
- Installed via `pip install -r requirements.txt`

## 2. **setup.py / setup.cfg**

For creating installable packages:

```python
setup(
    name="mypackage",
    install_requires=[
        "requests>=2.20.0",
        "numpy",
    ],
    extras_require={
        "dev": ["pytest", "black"],
    }
)
```

- Defines both package metadata and dependencies
- Supports optional dependency groups via `extras_require`
- Used when distributing packages to PyPI

## 3. **pyproject.toml**

The modern, standardized approach (PEP 518, 621):

```toml
[project]
dependencies = [
    "requests>=2.20.0",
    "numpy>=1.20",
]

[project.optional-dependencies]
dev = ["pytest", "black"]
```

- Unified configuration file for build systems and tools
- Replaces `setup.py` for many use cases
- Supported by modern tools like Poetry, Flit, and pip

## 4. **Pipfile / Pipfile.lock**

Used by Pipenv:

```toml
[packages]
requests = "*"

[dev-packages]
pytest = "*"
```

- Separates abstract dependencies (Pipfile) from concrete locked versions (Pipfile.lock)
- Provides deterministic builds via lock file

## 5. **poetry.lock / pyproject.toml**

Poetry's approach combines both:

- Dependencies specified in `pyproject.toml`
- Exact versions locked in `poetry.lock`
- Handles dependency resolution automatically

## 6. **Conda environment.yml**

For conda environments:

```yaml
dependencies:
  - python=3.9
  - numpy
  - pip:
    - requests
```

- Can specify both conda and pip packages
- Useful for scientific computing with non-Python dependencies

## Common Version Specifiers

- `==2.0.0` - exact version
- `>=1.5.0` - minimum version
- `~=1.5.0` - compatible release (>=1.5.0, <1.6.0)
- `>=1.0,<2.0` - version range

---

**Current trend**: The ecosystem is moving toward `pyproject.toml` as the standard for all Python projects, with lock files (Poetry, PDM, or pip-tools) for reproducible environments.
