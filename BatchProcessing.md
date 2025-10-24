# Batch Processing

## Basic Shell Scripting

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

## Python Scripts for Orchestration

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

## Tracking Experiments with YAML/JSON

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

## Other Workflow Management Tools

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
