# Build and Dependency Management

Most projects these days are written in high-level languages that do not require compilation. This simplifies things, but it can still be complicated to package your software in a way that makes it easy for another person to install in on their computer from scratch. 

## Language-Specific Tool

### C/C++

If you have a multi-file project coded natively in C/C++, using a tool to help other build code is a necessity. Below is a quick summary, but more detailed information is [here](CppBuild.md)

 * The Big Picture
   - **No single standard**: C/C++ has the most fragmented build ecosystem of any major language
   - **CMake dominates**: ~60-70% of modern C++ projects use CMake despite its complexity
   - **Context matters**: The "best" build system depends heavily on project size, platform needs, and team experience

 * Meta-build vs. Direct
   - **Meta-build systems** (CMake, Meson, Premake): Generate native build files
   - **Direct systems** (Make, Ninja, Bazel): Execute builds themselves

 * Quick Recommendations
   - **Small/medium projects**: Use **Meson** (simpler) or **CMake** (more ecosystem support)
   - **Large monorepos**: Use **Bazel** if you need extreme scale
   - **Cross-platform GUI apps**: Use **CMake** for best IDE integration

 * Managing Dependencies
   - Build systems traditionally only build code
   - Dependency management is separate (Conan, vcpkg, system packages)
   - Unlike Python/Julia/R, no unified solution for both
   - **Emerging trend**: Tools like build2 and xmake integrate both
   - Linux
     - The native package manager is usually sufficient for installing dependencies.
     - Docker can help to easily create an isolated, clean Linux environment.
     - Conda with `msys2-bash` is a nice alternative for using Unix tools in Windows/OSX.
     - [`linuxbrew'] also provides a way of installing dependencies thst is distribution-independent
   - Windows: Conda with `msys2-bash` is a nice alternative for using Unix tools in Windows/OSX.
   - OS/X: Homebrew

 * When to Use What

   | Build System | Best For | Avoid If |
   |--------------|----------|----------|
   | **CMake** | Cross-platform, ecosystem support, industry standard | You want simple syntax, small hobby projects |
   | **Meson** | New projects, fast builds, clean syntax | Need Windows-specific features, established CMake workflow |
   | **Make** | Simple Unix projects, understanding legacy code | Cross-platform needs, large projects |
   | **Bazel** | Monorepos, massive scale, Google-like setups | Small teams, simple projects, Windows-primary |
   | **Autotools** | Traditional Unix distribution | Modern projects, Windows support, maintainability |
   | **xmake/Premake** | Game development, Lua enthusiasts | Need mature ecosystem, enterprise support |

### Python

Highlights of Python's dependency mechanism are here. More details available [here](PythonDeps.md).

 * Fragmented Ecosystem
   - **Multiple competing standards**: `requirements.txt`, `setup.py`, `pyproject.toml`, Pipfile, etc.
   - **No built-in solution**: Unlike Julia, Python requires third-party tools for advanced dependency management
   - **Evolution over time**: The ecosystem has gradually moved from `setup.py` → `pyproject.toml`

 * Modern Best Practices
   - **pyproject.toml**: Standardized format (PEP 518, 621) for project configuration and dependencies
   - **Lock files**: Poetry, Pipenv, or pip-tools provide deterministic, reproducible builds
   - **Virtual environments**: `venv` or `virtualenv` for project isolation (separate from dependency specification)

 * Tool Landscape
   - **pip + requirements.txt**: Simple, ubiquitous, but lacks advanced features
   - **Poetry**: All-in-one solution with dependency resolution, virtual env management, and publishing
   - **Pipenv**: Combines Pipfile + lock file with virtual environment management
   - **pip-tools**: Minimalist approach to generate lock files from requirements.in

 * Version Specification
   - **Rich syntax**: `==`, `>=`, `~=`, `!=` with multiple constraints
   - **Dependency resolution**: Third-party tools handle complex version conflicts
   - **Optional dependencies**: `extras_require` or `optional-dependencies` for feature-specific packages

 * Key Differences from Other Languages
   - **Not built into the language**: Requires external package manager (pip) and tools
   - **Separation of concerns**: Virtual environments (isolation) vs. dependency files (specification)
   - **Massive ecosystem**: PyPI hosts 500,000+ packages with minimal curation
   - **Ongoing standardization**: Community still converging on best practicesPython offers several methods for specifying project dependencies, each suited for different use cases:

### Julia

Highlights of Python's dependency mechanism are here. More details available [here](JuliaDeps.md).

 * Built-in and Unified
   - **Native package manager (Pkg)**: Fully integrated into Julia, no external tools needed
   - **Single standard**: No fragmentation—everyone uses the same system
   - **Batteries included**: Dependency resolution, environments, and versioning work out of the box

 * Two-File System
   - **Project.toml**: Human-readable file specifying direct dependencies and compatibility
   - **Manifest.toml**: Auto-generated lock file with complete dependency graph and exact versions
   - **Both committed to version control**: Ensures reproducibility across machines

 * Sophisticated Resolution
   - **Automatic conflict resolution**: Built-in solver handles complex dependency graphs
   - **Semantic versioning**: First-class support with `[compat]` specifications
   - **Transitive dependencies**: Automatically resolved and tracked in Manifest.toml

 * UUID-Based Identification
   - **No naming conflicts**: Packages identified by UUID, not just name
   - **Multiple registries**: Can use packages from different sources without collision
   - **Global uniqueness**: UUIDs prevent the "namespace problem"

 * Environment Management
   - **Project-specific environments**: Easy switching with `Pkg.activate()`
   - **Isolated by default**: Each project can have different package versions
   - **No separate tool needed**: Unlike Python's venv/virtualenv, it's built into Pkg

 * Developer Experience
   - **Precompilation**: Packages are precompiled for faster loading
   - **Development mode**: `Pkg.develop()` for working on packages locally
   - **Integrated testing**: `Pkg.test()` runs package tests in isolated environments

 * Key Advantages
   - **Zero configuration**: Works perfectly without setup or config files
   - **Reproducible by default**: Lock files are generated automatically
   - **Scientific computing focus**: Designed with reproducibility in mind from day one
   - **Fast and efficient**: Modern resolver is quick even with large dependency trees

### R

Highlights of R's dependency mechanism are here. More details available [here](RDeps.md).

 * Built-in Tools
   - **Simple but limited**: `install.packages()` and `library()` work globally without isolation
   - **Automatic dependency installation**: Dependencies listed in `Imports` and `Depends` are installed automatically
   - **No sophisticated version resolution**: Uses latest compatible versions from repositories

 * Modern Standard: renv
   - **Project-specific libraries**: Each project has isolated package installations
   - **Reproducible environments**: `renv.lock` captures exact package versions
   - **Global package cache**: Saves disk space by sharing packages across projects
   - **Simple workflow**: `init()`, `snapshot()`, `restore()` cover most use cases

 * Package Repositories
   - **CRAN**: Primary repository with ~20,000 packages and strict quality control
   - **Bioconductor**: Specialized repository for bioinformatics packages
   - **GitHub**: Access to development versions and custom packages

 * For Package Development
   - **DESCRIPTION file**: Specifies `Imports`, `Suggests`, and `Depends`
   - **devtools integration**: Streamlined development workflow
   - **Version specifications**: Support for minimum versions and ranges

 * Differences from Python/Julia
   - Environment isolation was added later (not built-in from the start)
   - Less sophisticated dependency resolution compared to Julia's Pkg
   - More centralized ecosystem (CRAN curation vs. PyPI's open model)
   - **renv** fills the role of Python's Poetry or Julia's built-in Pkg

## Creating Isolated Reproducible Environments

Here is a summary of options for creating enironments for running reprocible experiments. More details are [here](ReproucibleEnvironments.md)

 * Language-specific Virutal Environments
    * `pyenv`
    * `renv`
    * `Pkg`
 * Cross-platform Package Managers
   * [Conda/Mamba](https://docs.conda.io/en/latest/)
   * [Homebrew/Linuxbrew](https://brew.sh)
   * [Spack](https://spack.readthedocs.io/en/latest/features.html)
   * [Nix](https://nixos.org/)
   * [Conan](https://conan.io)
 * Containers
   * [Docker](https://docker.com)
   * [Podman](https://podman.io)
   * [Singularity]()
 * [Jupyter Notebooks](https://jupyter.org)
 * Cloud
   * [Binder](https://mybinder.org)
   * [Colab](https://colab.google.com)
   * [Code Ocean](https://codeocean.com)
   * [Github](https://github.com/)
     * CodeSpaces
     * Actions

## Overall Recommendations by Use Case

### Quick Local Experiment (Python)
```bash
# Lightweight approach
python -m venv venv
source venv/bin/activate
pip install -r requirements.txt
```

### Shareable Experiment (Any Language)
```dockerfile
# Docker for portability
FROM python:3.9
COPY requirements.txt .
RUN pip install -r requirements.txt
COPY . /app
WORKDIR /app
```

### Academic Paper Reproduction
- **Docker** + published image on Docker Hub
- **Binder** for Jupyter notebooks
- **Code Ocean** for full reproducibility platform

### HPC Cluster
- **Singularity** containers
- Or **Spack** for package management
- Plus job scheduler (SLURM, PBS)

### Maximum Reproducibility
- **Nix** for bit-for-bit reproducibility
- Or **Docker** with pinned base image tags and checksums
- Version control everything (code, Dockerfile, lock files)

### Long-term Archival
- **Docker images** pushed to registry
- **Zenodo** or **Docker Hub** for permanent storage
- Include checksums and version tags

### Layered Approach
Combine multiple tools for complete reproducibility:
- **Language environment** (venv, renv, Pkg)
- **+ Version pinning** (requirements.txt, lock files)
- **+ Container** (Docker, Singularity)
- **+ Version control** (git)

### Trade-offs
- **Lightweight** (venv) → Fast, easy, but limited isolation
- **Medium** (conda, Docker) → Good balance for most use cases
- **Heavy** (VMs, Nix) → Maximum reproducibility, higher complexity

### Reproducibility Levels

1. **Code only**: Not reproducible (dependencies change)
2. **Code + dependency list**: Somewhat reproducible (versions drift)
3. **Code + lock file**: Good reproducibility (specific versions)
4. **Code + lock file + container**: Very reproducible (includes OS)
5. **Code + Nix/Guix**: Bit-for-bit reproducible (all dependencies pinned)

## Best Practices

**Always capture**:
- Exact dependency versions (lock files)
- Runtime version (Python 3.9.7, not just 3.9)
- OS version (if using containers)
- Hardware requirements (GPU, memory)
- Random seeds

**Version control**:
- Code
- Dependency files
- Container definitions (Dockerfile)
- Environment specs
- Documentation

**Document**:
- How to reproduce the environment
- How to run experiments
- Expected outputs
- System requirements

### Modern Standard (2025)

For most scientific computing:
1. **Development**: Language-specific tool (conda, renv, Pkg)
2. **Sharing**: Docker container
3. **Publishing**: Docker image + code on GitHub/Zenodo
4. **Optional**: Nix for maximum reproducibility

### Common Pitfalls

- Not pinning versions (dependencies drift over time)
- Using "latest" tags in Docker (changes unpredictably)
- Forgetting system dependencies (C libraries, etc.)
- Not documenting hardware requirements
- Assuming same results across architectures (ARM vs x86)
