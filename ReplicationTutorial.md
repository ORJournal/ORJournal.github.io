# Tutorial Materials from the *Workshop on Replication at INFORMS Pubs*

This tutorial will cover the basic of putting together a high-quality replication package.
 * Tools for making it easier to replicate results.
 * What to include in the replication package.
 * How to organize the replication package. 
 * How to write a complete and informative README.

## Table of Contents
 * Overview (#overview)
 * Tools
   * [Version Control](VersionControl.md)
   * [BatchProcessing](BatchProcessing.md)
   * [Build and Dependendecy Management](BuildAndDependencyManagement.md) 
   * [Creating Isolated Environments](ReproducibleEnvironments.md)
 * [Replication Package](PackageContents.md)
 * [README](#readme)

## Overview

### Reproducibility Levels

1. **Code only**: Not reproducible (dependencies change)
2. **Code + dependency list**: Somewhat reproducible (versions drift)
3. **Code + lock file**: Good reproducibility (specific versions)
4. **Code + lock file + container**: Very reproducible (includes OS)
5. **Code + Nix/Guix**: Bit-for-bit reproducible (all dependencies pinned)

### Best Practices

#### Version control
1. Code
1. Dependency files
1. Container definitions (Dockerfile)
1. Environment specs
1. Documentation

#### Batch Processing

1. Use scripting to automate workflows.

1. Track experiment with unique IDs

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

1. Keep logs of all procedures

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

1. Be sure to handle errors

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

1. Track required resources

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

1. Keep results organization

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

### Build and Dependency Management

### Using Isolated/Reproducible Environments

1. **Development**: Language-specific tool (conda, renv, Pkg)
1. **Sharing**: Docker container
1. **Publishing**: Docker image + code on GitHub/Zenodo
1. **Optional**: Nix for maximum reproducibility

### Documentation (README)

1. How to reproduce the environment
1. How to run experiments
1. Expected outputs
1. System requirements and dependencies
   - Exact dependency versions (lock files)
    - Runtime version (Python 3.9.7, not just 3.9)
    - OS version (if using containers)
    - Hardware requirements (GPU, memory)
    - Random seeds

## Common Pitfalls

- Not pinning versions (dependencies drift over time)
- Using "latest" tags in Docker (changes unpredictably)
- Forgetting system dependencies (C libraries, etc.)
- Not documenting hardware requirements
- Assuming same results across architectures (ARM vs x86)

