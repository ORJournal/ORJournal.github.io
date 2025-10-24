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
   * [Creating Isolated Environments](#creating-isolated-environments)
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

### Creating Isolated Reproducible Environments

Here is a summary of options for creating enironments for running reprocible experiments. More details are [here](ReproucibleEnvironments.md)

 * Language-specific Virutal Environments
    * `pyenv`
    * `renv`
    * `Pkg`
 * Cross-platform Package
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

### Overall Recommendations by Use Case

#### Quick Local Experiment (Python)
```bash
# Lightweight approach
python -m venv venv
source venv/bin/activate
pip install -r requirements.txt
```

#### Shareable Experiment (Any Language)
```dockerfile
# Docker for portability
FROM python:3.9
COPY requirements.txt .
RUN pip install -r requirements.txt
COPY . /app
WORKDIR /app
```

#### Academic Paper Reproduction
- **Docker** + published image on Docker Hub
- **Binder** for Jupyter notebooks
- **Code Ocean** for full reproducibility platform

#### HPC Cluster
- **Singularity** containers
- Or **Spack** for package management
- Plus job scheduler (SLURM, PBS)

#### Maximum Reproducibility
- **Nix** for bit-for-bit reproducibility
- Or **Docker** with pinned base image tags and checksums
- Version control everything (code, Dockerfile, lock files)

#### Long-term Archival
- **Docker images** pushed to registry
- **Zenodo** or **Docker Hub** for permanent storage
- Include checksums and version tags

#### Layered Approach
Combine multiple tools for complete reproducibility:
- **Language environment** (venv, renv, Pkg)
- **+ Version pinning** (requirements.txt, lock files)
- **+ Container** (Docker, Singularity)
- **+ Version control** (git)

#### Trade-offs
- **Lightweight** (venv) → Fast, easy, but limited isolation
- **Medium** (conda, Docker) → Good balance for most use cases
- **Heavy** (VMs, Nix) → Maximum reproducibility, higher complexity

#### Reproducibility Levels

1. **Code only**: Not reproducible (dependencies change)
2. **Code + dependency list**: Somewhat reproducible (versions drift)
3. **Code + lock file**: Good reproducibility (specific versions)
4. **Code + lock file + container**: Very reproducible (includes OS)
5. **Code + Nix/Guix**: Bit-for-bit reproducible (all dependencies pinned)

### Best Practices

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

### Scripting

#### Basic Shell Scripting

More flexible - generate parameter combinations programmatically:

```bash
#!/bin/bash
# run_experiments.sh

# Create results directory
mkdir -p results

for lr in 0.001 0.01 0.1; do
    echo "Running with lr=$lr"
    
    # Redirect output to log file
    python experiment.py --lr $lr \
        > results/experiment_lr${lr}.log 2>&1
    
    # Check exit status
    if [ $? -eq 0 ]; then
        echo "✓ Success: lr=$lr"
    else
        echo "✗ Failed: lr=$lr"
    fi
done

echo "All experiments complete!"
```
**Run it:**
```bash
chmod +x run_experiments.sh
./run_experiments.sh
```

#### Python Scripts for Orchestration

Python provides more power and options for processing data within the script. 

```python
#!/usr/bin/env python3
# run_experiments.py

import subprocess
import itertools

def run_experiment(lr, batch_size, epochs=10):
    """Run a single experiment"""
    cmd = [
        'python', 'experiment.py',
        '--lr', str(lr),
        '--batch-size', str(batch_size),
        '--epochs', str(epochs)
    ]
    
    print(f"Running: lr={lr}, batch_size={batch_size}")
    result = subprocess.run(cmd, capture_output=True, text=True)
    
    if result.returncode == 0:
        print(f"✓ Success: lr={lr}, batch_size={batch_size}")
    else:
        print(f"✗ Failed: lr={lr}, batch_size={batch_size}")
        print(f"Error: {result.stderr}")
    
    return result.returncode

def main():
    # Define parameter grid
    learning_rates = [0.001, 0.01, 0.1]
    batch_sizes = [32, 64, 128]
    
    # Run all combinations
    for lr, bs in itertools.product(learning_rates, batch_sizes):
        run_experiment(lr, bs)

if __name__ == '__main__':
    main()
```

#### Tracking Experiments with YAML/JSON

```yaml
# experiments.yaml
experiments:
  - name: "baseline"
    params:
      lr: 0.01
      batch_size: 64
      epochs: 50
  
  - name: "high_lr"
    params:
      lr: 0.1
      batch_size: 64
      epochs: 50
  
  - name: "large_batch"
    params:
      lr: 0.01
      batch_size: 256
      epochs: 50

# Parameter sweeps
sweep:
  lr: [0.001, 0.01, 0.1]
  batch_size: [32, 64, 128]
  dropout: [0.1, 0.3, 0.5]
```

```python
#!/usr/bin/env python3
# run_from_config.py

import yaml
import subprocess
import itertools
from pathlib import Path

def load_config(config_file):
    """Load experiment configuration"""
    with open(config_file, 'r') as f:
        return yaml.safe_load(f)

def run_experiment(name, params):
    """Run experiment with given parameters"""
    cmd = ['python', 'experiment.py', '--name', name]
    
    # Add all parameters
    for key, value in params.items():
        cmd.extend([f'--{key}', str(value)])
    
    print(f"Running: {name}")
    result = subprocess.run(cmd)
    return result.returncode == 0

def run_named_experiments(config):
    """Run individually named experiments"""
    for exp in config.get('experiments', []):
        run_experiment(exp['name'], exp['params'])

def run_sweep(config):
    """Run parameter sweep"""
    sweep_config = config.get('sweep', {})
    
    if not sweep_config:
        return
    
    # Generate all combinations
    keys = sweep_config.keys()
    values = sweep_config.values()
    
    for combination in itertools.product(*values):
        params = dict(zip(keys, combination))
        name = '_'.join(f'{k}{v}' for k, v in params.items())
        run_experiment(name, params)

def main():
    config = load_config('experiments.yaml')
    
    # Run named experiments
    run_named_experiments(config)
    
    # Run parameter sweep
    run_sweep(config)

if __name__ == '__main__':
    main()
```

#### Other Workflow Management Tools

 * [Snakemake](https://snakemake.readthedocs.io/en/stable/)
 * [Sacred](https://sacred.readthedocs.io/en/stable/)
 * [SLURM]

#### Best Practices

 1. Unique Experiment IDs

    Always generate unique identifiers:

    ```python
    import uuid
    from datetime import datetime

    # Timestamp-based
    exp_id = f"exp_{datetime.now().strftime('%Y%m%d_%H%M%S')}"

    # UUID-based
    exp_id = f"exp_{uuid.uuid4().hex[:8]}"

    # Parameter-based
    exp_id = f"lr{lr}_bs{bs}_seed{seed}"
    ```

 1. Logging and Checkpointing

    ```python
    import logging
    from pathlib import Path

    def setup_logging(exp_dir):
        """Setup logging for experiment"""
        log_file = exp_dir / 'experiment.log'
    
        logging.basicConfig(
            level=logging.INFO,
            format='%(asctime)s - %(levelname)s - %(message)s',
            handlers=[
                logging.FileHandler(log_file),
                logging.StreamHandler()
            ]
        )

    # In experiment script
    setup_logging(exp_dir)
    logging.info(f"Starting experiment with params: {params}")
    ```

 1. Error Handling

    ```python
    def safe_run_experiment(params):
        """Run experiment with error handling"""
        try:
            result = run_experiment(params)
            return {'success': True, 'result': result}
        except Exception as e:
            logging.error(f"Experiment failed: {e}")
            return {'success': False, 'error': str(e)}
    ```

 1. Resource Management

    ```python
    import psutil
    import time

    def monitor_resources(interval=60):
        """Monitor CPU and memory usage"""
        while True:
            cpu = psutil.cpu_percent(interval=1)
            mem = psutil.virtual_memory().percent
            logging.info(f"CPU: {cpu}%, Memory: {mem}%")
            time.sleep(interval)

    # Run in background thread
    import threading
    monitor_thread = threading.Thread(target=monitor_resources, daemon=True)
    monitor_thread.start()
    ```

 1. Results Organization

    ```
    experiments/
    ├── exp_001_20250101_120000/
    │   ├── params.json
    │   ├── metrics.json
    │   ├── model.pkl
    │   ├── logs/
    │   │   └── training.log
    │   └── plots/
    │       └── learning_curve.png
    ├── exp_002_20250101_130000/
    │   └── ...
    └── summary.csv
    ```

## Replication Package

### Contents

### Organization

## README

