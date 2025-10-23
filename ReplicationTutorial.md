# Tutorial Materials from the *Workshop on Replication at INFORMS Pubs*

This tutorial will cover the basic of putting together a high-quality replication package.
 * Tools for making it easier to replicate results.
 * What to include in the replication package.
 * How to organize the replication package. 
 * How to write a complete and informative README.

## Table of Contents
 * [Tools](#tools)
   * [Version Control](#version-control)
   * [Build and Dependendecy Management](#build-and-dependency-management) 
   * [Environment Management](#environment-management)
   * [Scripting](#scripting)
 * [Replication Package](#replication-package)
   * [Contents](#contents)
   * [Organization](#organization)
 * [README](#readme)

## Tools

### Version Control

Using verion control is a must for replicability. Version control allows you to save and easily retrieve snapshots of your code. It is good practice to save snapshots of your code frequently during development. This makes it easier to track down bugs, do performance tuning, etc. For replication, it is essential to save a snapshot of the exact version used perform any experiments.

The most widely used version control system is [`git`](https://git-scm.com/). It is simple to use and there are many ways to use it.
 * On the command line,
 * Through an IDE, such as [VS Code](https://code.visualstudio.com/),
 * Through [Github](https://github.com) (file upload, CodeSpaces, etc.)

Getting started with git and using it locally is very simple.
```bash
~/path/to/project> git init
~/path/to/project> git add .
~/path/to/project> git commit -m "My first commit"
```
To hook up your local repo to Github, first create an empty repository on Github.

<img width="585" height="706" alt="image" src="https://github.com/user-attachments/assets/d89ee7af-3ccf-4b2d-99d8-42af4069dfbd" />

Then add it as a remote and push (you need to set up an SSH key first).
```bash
~/path/to/project> git remote add origin git@github.com:tkralphs/MyExample.git
~/path/to/project> git branch -M main
~/path/to/project> git push -u origin main
```

### Build and Dependency Management

Most projects these days are written in high-level languages that do not require compilation. This simplifies things, but it can still be complicated to package your software in a way that makes it easy for another person to install in on their computer from scratch. 

#### C/C++

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

#### Python

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

#### Julia

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

#### R

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

### Isolated Environments

 * [Jupyter Notebooks](https://jupyter.org)
   * [Binder](https://mybinder.org)
 * [Docker/Podman](https://docker.com)
 * [Nix](https://nixos.org/)
 * [Conda/Mamba](https://docs.conda.io/en/latest/)
 * [Homebrew/Linuxbrew](https://brew.sh)
 * [Github](https://github.com/)
   * CodeSpaces
   * Actions

### Scripting

#### Bash

#### Julia

#### Python

#### Jupyter Notebooks

## Replication Package

### Contents

### Organization

## README

