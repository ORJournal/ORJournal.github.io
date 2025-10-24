# Replication Tutorial 

This tutorial was presented at the *Workshop on Replication at INFORMS Pubs* on October 25, 2025. It covers the basic principles of making experiments replicable and the process of putting together a high-quality replication package. It is targeted at authors submitting publications to journals published by INFORMS Pubs. The following is the basic content. 
 * Tools for making it easier to replicate results.
 * How to compile data into tables and figures.
 * What to include in the replication package.
 * How to write a complete and informative README.

# Table of Contents
 * Overview
 * Tools
   * [Version Control](#version-control)
   * [BatchProcessing](#batch-processing)
   * [Build and Dependendecy Management](#build-and-dependency-management) 
   * [Using Isolated/Reproducible Environments](#using-isolated-environments)
 * [Compiling Tables and Figures](#compiling-tables-and-figures)
 * [Replication Package](PackageContents.md)
 * [Documentation](#documentation)
 * [Pitfalls](#common-pitfalls)

# Overview

Levels of Reproducibility

1. **Code only**: Not reproducible (dependencies change)
2. **Code + dependency list**: Somewhat reproducible (versions drift)
3. **Code + lock file**: Good reproducibility (specific versions)
4. **Code + lock file + container**: Very reproducible (includes OS)
5. **Code + Nix/Guix**: Bit-for-bit reproducible (all dependencies pinned)

## Version control 

See more details [here](VersionControl.md)

1. Code
1. Dependency files
1. Container definitions (Dockerfile)
1. Environment specs
1. Documentation

## Batch Processing 

See more details details [here](BatchProcessing.md)

1. Use scripting to automate workflows.

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

1. Track experiment with unique IDs.

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

1. Keep logs of all procedures.

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

1. Be sure to handle errors well to avoid missing data and undetected errors.

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

1. Track required resources for specifying requirements.

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

1. Keep results organized.

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

## Build and Dependency Management 

See more details [here](BuildAndDependencyManagement.md)

The goal of build and dependency management is to allow reviewers and other users to build (if required) and install your code, as well as replicating the environment in which the exeriments were performed as closely as possible (same version of dependencies). This is typically done using language-specific tools.

1. Always Pin Versions

   **Don't:**
   ```
   requests
   numpy
   boost
   ```

   **Do:**
   ```
   requests==2.31.0
   numpy>=1.24.0,<2.0.0
   boost~=1.82.0
   ```

   **Why:** Unpinned versions lead to "works on my machine" problems as dependencies update.

1. Use Lock Files

   - **Python**: `requirements.txt` + `poetry.lock` or `Pipfile.lock`
   - **Julia**: `Manifest.toml` (auto-generated)
   - **JavaScript**: `package-lock.json` or `yarn.lock`
   - **R**: `renv.lock`
   - **Rust**: `Cargo.lock`

   **Rule:** Commit lock files to version control for applications, optional for libraries.

1. Separate Direct and Transitive Dependencies

   **Direct dependencies** (what you import):
   ```toml
   # pyproject.toml
   [project]
   dependencies = [
       "requests>=2.28.0",
       "pandas>=2.0.0"
   ]
   ```

   **Transitive dependencies** (dependencies of dependencies):
   ```
   # Captured in lock file automatically
   urllib3==2.0.7  # dependency of requests
   numpy==1.26.0   # dependency of pandas
   ```

1. Isolate Environments Per Project

   **Never:**
   - Install everything globally
   - Share environments between projects
   - Use system Python/R/Node for development

   **Always:**
   - Use project-specific environments (venv, conda, renv, Pkg)
   - One environment per project
   - Keep environments reproducible with lock files

## Using Isolated Environments 

See more details [here](ReproducibleEnvironments.md)

The most reliable way to ensure replicability is to provide the reviewer with a way to construct an isolated environment where all dependencies can be automatically installed and the experiments run in a "clean" environment. This also makes it easy for authors to test their own replication package.

1. **Development**: Language-specific tool (conda, renv, Pkg)
1. **Sharing**: Docker or Jupyter Notebook
1. **Publishing**: Docker + code on GitHub/Zenodo
1. **Optional**: Nix for maximum reproducibility

## Compiling Tables and Figures 

See more details [here](TablesAndFigures.md)

Use scripted workflows (Python/R) for reproducibility. Export to LaTeX for tables, PDF for figures. Automate with Make or scripts. Never create tables/figures manually.

### General Principles

1. **Separate data from presentation**
   - Raw data → Processing → Tables/Figures
   - Never modify raw data files

2. **Version control everything**
   - Scripts for generating tables/figures
   - Not the generated files themselves (usually)
   - Exception: Small final PDFs for paper

3. **Reproducible from raw data**
   - Single command to regenerate all materials
   - Document dependencies and versions

4. **Use consistent formatting**
   - Same font sizes across figures
   - Consistent color schemes
   - Matching decimal places in tables

5. **Follow journal guidelines**
   - File formats (PDF, EPS, TIFF)
   - Resolution requirements (300+ DPI)
   - Color vs. grayscale
   - Maximum file sizes

### Table Best Practices

**Do:**
- Use consistent decimal places (usually 2-3)
- Include confidence intervals or standard errors
- Bold or highlight best results
- Use horizontal lines sparingly (booktabs style)
- Align numbers properly (decimal alignment)
- Include units in headers
- Add descriptive captions

**Don't:**
- Use vertical lines (looks dated)
- Report excessive precision (0.8912345)
- Make tables too wide/complex
- Forget to label columns clearly

**Example good table:**
```latex
\begin{table}[h]
\centering
\caption{Model performance on test set (mean ± 95\% CI)}
\label{tab:results}
\begin{tabular}{lcc}
\toprule
Model & Accuracy (\%) & F1-Score \\
\midrule
Baseline & 85.4 ± 1.2 & 0.851 ± 0.015 \\
\textbf{Model A} & \textbf{89.2 ± 0.9} & \textbf{0.887 ± 0.012} \\
Model B & 87.6 ± 1.1 & 0.872 ± 0.013 \\
\bottomrule
\end{tabular}
\end{table}
```

### Figure Best Practices

**Do:**
- Use vector formats (PDF, EPS) for line plots
- Use high-resolution raster (PNG, TIFF ≥300 DPI) for images
- Make text readable (10-12pt minimum)
- Use colorblind-friendly palettes
- Include error bars/shading
- Label axes with units
- Use legends effectively
- Keep figures simple and focused

### Quality Checklist

Before submission:
- [ ] All numbers match source data
- [ ] Figures at required resolution (≥300 DPI)
- [ ] Consistent formatting across tables/figures
- [ ] All captions descriptive and complete
- [ ] Error bars/confidence intervals included
- [ ] Statistical tests reported correctly
- [ ] Colorblind-friendly palettes used
- [ ] Everything reproducible from scripts
- [ ] Scripts documented and version controlled

## Replication Package 

See more details [here](PackageContents.md).

The contents of the repository should be organized to make it as easy as possible for the associate editor to do the replication. This can mean that 
 * `README.md` 
 * `LICENSE` 
 * `AUTHORS` 
 * You may also have a `Makefile` or other files needed to build the software, install dependencies and/or run the experiments.
 * Subdirectories

   * `src` contains the source code for any software.
   * `data` contains data files needed for experiments or used in the paper.
   * `scripts` should contain any required scripts.
   * `docs` contains any additional documentation. 
   * `results` should contain any raw results, as well as
     any plots or figures.

You may wish to have an additional README.md in any of the subdirectories to
provide additional informatio

## Documentation 

See more details [here](READMEContents.md).

1. How to reproduce the environment
1. How to run experiments
1. Expected outputs
1. System requirements and dependencies
    - Exact dependency versions (lock files)
    - Runtime version (Python 3.9.7, not just 3.9)
    - OS version (if using containers)
    - Hardware requirements (GPU, memory)
    - Random seeds

    ```markdown
    # README.md

    ## System Requirements
    - Python 3.9+
    - GCC 9+ (for C extensions)
    - CUDA 11.8 (for GPU support)
    - libpq-dev (PostgreSQL client)

    ## Installation
    ...
    ```
    
## Common Pitfalls

- Not pinning versions (dependencies drift over time)
- Using "latest" tags in Docker (changes unpredictably)
- Forgetting system dependencies (C libraries, etc.)
- Not documenting hardware requirements
- Assuming same results across architectures (ARM vs x86)

