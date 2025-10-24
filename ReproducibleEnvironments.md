# Isolated Environments for Reproducible Experiments

Creating reproducible experimental environments requires isolating dependencies, system configurations, and runtime environments. Here are the main approaches, from lightweight to comprehensive.

## Language-Specific Virtual Environments

### Python: venv / virtualenv

Isolates Python packages at the user level:

```bash
# Create virtual environment
python -m venv myenv

# Activate
source myenv/bin/activate  # Unix
myenv\Scripts\activate     # Windows

# Install dependencies
pip install -r requirements.txt

# Deactivate
deactivate
```

**Pros**:
- Lightweight and fast
- Built into Python (venv)
- Easy to create/destroy

**Cons**:
- Only isolates Python packages
- Doesn't isolate Python version or system libraries
- Doesn't capture OS-level dependencies

### Python: conda

Isolates Python version and packages, including non-Python dependencies:

```bash
# Create environment with specific Python version
conda create -n myenv python=3.9 numpy scipy

# Activate
conda activate myenv

# Export environment
conda env export > environment.yml

# Recreate environment
conda env create -f environment.yml
```

**Pros**:
- Isolates Python version itself
- Handles non-Python dependencies (C libraries, R, etc.)
- Good for scientific computing
- Cross-platform reproducibility

**Cons**:
- Heavier than venv
- Slower package resolution
- Mixing conda and pip can cause issues

### R: renv

Project-specific R package libraries:

```r
# Initialize
renv::init()

# Save state
renv::snapshot()

# Restore
renv::restore()
```

**Pros**:
- Project-specific package versions
- Automatic lockfile generation
- Integrates well with RStudio

**Cons**:
- Only isolates R packages
- Doesn't isolate R version or system dependencies

### Julia: Pkg Environments

Built-in project environments:

```julia
# Activate project
using Pkg
Pkg.activate(".")

# Install packages (automatically tracked)
Pkg.add("DataFrames")

# Instantiate from manifest
Pkg.instantiate()
```

**Pros**:
- Built into language
- Automatic manifest generation
- Isolates package versions perfectly

**Cons**:
- Only isolates Julia packages
- Doesn't isolate Julia version or system libraries

### Node.js: npm / yarn

Project-specific Node dependencies:

```bash
# npm
npm install

# yarn
yarn install
```

**Pros**:
- Standard in JavaScript ecosystem
- Lock files (package-lock.json, yarn.lock)
- Local node_modules per project

**Cons**:
- Only isolates Node packages
- Doesn't isolate Node.js version

## Version Managers

### Python: pyenv

Manages multiple Python versions:

```bash
# Install specific Python version
pyenv install 3.9.7

# Set version for directory
pyenv local 3.9.7

# Combine with venv
python -m venv myenv
```

**Pros**:
- Easy Python version switching
- Works with venv/virtualenv

**Cons**:
- Doesn't isolate system dependencies
- Still need venv for package isolation

### Node.js: nvm / fnm

Manages multiple Node.js versions:

```bash
# nvm
nvm install 16.14.0
nvm use 16.14.0

# fnm (faster)
fnm install 16.14.0
fnm use 16.14.0
```

**Pros**:
- Easy Node version switching
- Per-project .nvmrc files

**Cons**:
- Only manages Node versions
- Need npm/yarn for packages

### Ruby: rbenv / rvm

Manages multiple Ruby versions:

```bash
# rbenv
rbenv install 3.1.0
rbenv local 3.1.0

# rvm
rvm install 3.1.0
rvm use 3.1.0
```

## Container Technologies

### Docker

Full OS-level isolation with containers:

```dockerfile
# Dockerfile
FROM python:3.9-slim

WORKDIR /app

COPY requirements.txt .
RUN pip install -r requirements.txt

COPY . .

CMD ["python", "experiment.py"]
```

```bash
# Build image
docker build -t myexperiment:v1 .

# Run container
docker run myexperiment:v1

# Interactive shell
docker run -it myexperiment:v1 bash
```

**Pros**:
- Complete isolation (OS, dependencies, runtime)
- Highly reproducible across machines
- Version control via image tags
- Can specify exact OS version
- Portable across platforms

**Cons**:
- Larger overhead than venv
- Learning curve
- Slower iteration during development
- Need Docker installed

### Docker Compose

Orchestrate multi-container setups:

```yaml
# docker-compose.yml
version: '3'
services:
  experiment:
    build: .
    volumes:
      - ./data:/app/data
    environment:
      - EXPERIMENT_ID=exp001
  
  database:
    image: postgres:13
    environment:
      - POSTGRES_DB=experiments
```

```bash
docker-compose up
```

**Pros**:
- Manage multiple services (app, database, etc.)
- Reproducible multi-component setups
- Easy development environments

**Cons**:
- More complex than single container
- Overkill for simple experiments

### Podman

Docker alternative (daemonless, rootless):

```bash
# Similar commands to Docker
podman build -t myexperiment:v1 .
podman run myexperiment:v1
```

**Pros**:
- More secure (no daemon, rootless)
- Docker-compatible
- Better for HPC environments

**Cons**:
- Less widespread than Docker
- Some compatibility issues

### Singularity / Apptainer

Container system designed for HPC:

```bash
# Build from Docker image
singularity build myexperiment.sif docker://myexperiment:v1

# Run
singularity run myexperiment.sif

# Shell
singularity shell myexperiment.sif
```

**Pros**:
- Designed for scientific computing
- Works on HPC clusters (no root needed)
- Better GPU support than Docker
- Can convert Docker images

**Cons**:
- Less common than Docker
- Smaller ecosystem

## Virtual Machines

### Vagrant

Manages development VMs with code:

```ruby
# Vagrantfile
Vagrant.configure("2") do |config|
  config.vm.box = "ubuntu/focal64"
  
  config.vm.provision "shell", inline: <<-SHELL
    apt-get update
    apt-get install -y python3-pip
    pip3 install -r /vagrant/requirements.txt
  SHELL
end
```

```bash
vagrant up
vagrant ssh
```

**Pros**:
- Complete OS isolation
- Can test different operating systems
- Reproducible VM configurations

**Cons**:
- Heavy resource usage
- Slow startup
- Large disk space requirements
- Overkill for most experiments

### VirtualBox / VMware

Full virtualization platforms:

**Pros**:
- Complete isolation
- Can snapshot states
- Test different OS versions

**Cons**:
- Manual setup (unless using Vagrant)
- Resource intensive
- Slow

## Cloud/Remote Options

### Google Colab / Kaggle Notebooks

Cloud-based Jupyter notebooks:

**Pros**:
- No local setup needed
- Free GPU/TPU access
- Easy sharing

**Cons**:
- Limited to specific runtimes
- Session timeouts
- Not fully reproducible (environment changes)

### Binder / MyBinder.org

Reproducible Jupyter environments from GitHub:

```yaml
# environment.yml
name: myenv
dependencies:
  - python=3.9
  - numpy
  - matplotlib
```

**Pros**:
- Reproducible from repo
- Free hosting
- Easy sharing

**Cons**:
- Limited resources
- Slow startup
- Not suitable for long-running experiments

### Code Ocean / Gigantum

Platforms designed for computational reproducibility:

**Pros**:
- Built for scientific reproducibility
- Version control integrated
- Captures full environment

**Cons**:
- May require paid plans
- Platform lock-in

## Specialized Tools

### Nix / NixOS

Declarative package management and system configuration:

```nix
# shell.nix
{ pkgs ? import <nixpkgs> {} }:

pkgs.mkShell {
  buildInputs = [
    pkgs.python39
    pkgs.python39Packages.numpy
    pkgs.python39Packages.scipy
  ];
}
```

```bash
nix-shell
```

**Pros**:
- Bit-for-bit reproducibility
- Can specify exact package versions (even old ones)
- Isolated environments per project
- Works for any language/tool

**Cons**:
- Steep learning curve
- Nix language is complex
- Smaller community
- Can be slow to build

### Guix

Similar to Nix with Scheme-based configuration:

**Pros**:
- Reproducible package management
- Uses Scheme (Lisp dialect)
- Transactional updates

**Cons**:
- Even smaller community than Nix
- Learning curve

### Spack

Package manager for HPC:

```bash
spack install python@3.9.7 ^openmpi@4.1.0
spack load python@3.9.7
```

**Pros**:
- Designed for scientific computing
- Handles complex dependency graphs
- Good for HPC environments

**Cons**:
- Primarily for HPC use cases
- Overkill for simple experiments

## Workflow Management Systems

### Snakemake

Workflow system with environment management:

```python
# Snakefile
rule experiment:
    conda: "environment.yml"
    script: "experiment.py"
```

**Pros**:
- Integrated environment specification
- Reproducible workflows
- Can use conda or containers

**Cons**:
- Need to learn workflow system
- Overhead for simple experiments

### Nextflow

Workflow system with container support:

```groovy
process experiment {
    container 'myexperiment:v1'
    
    script:
    "python experiment.py"
}
```

**Pros**:
- Designed for reproducibility
- Native container support
- Good for bioinformatics

**Cons**:
- Learning curve
- Groovy-based DSL

## Comparison Matrix

| Approach | Isolation Level | Reproducibility | Setup Complexity | Resource Overhead | Best For |
|----------|----------------|-----------------|------------------|-------------------|----------|
| **venv/virtualenv** | Packages only | Low | Very Low | Minimal | Quick Python experiments |
| **conda** | Packages + Python version | Medium | Low | Low | Scientific Python work |
| **Docker** | Complete OS | High | Medium | Medium | Cross-platform reproducibility |
| **Singularity** | Complete OS | High | Medium | Medium | HPC environments |
| **Vagrant** | Full VM | High | Medium | High | OS-level testing |
| **Nix** | Bit-for-bit | Very High | High | Low | Maximum reproducibility |
| **Language tools (renv, Pkg)** | Language packages | Medium | Very Low | Minimal | Language-specific projects |

## Recommendations by Use Case

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
- Or **Binder** for Jupyter notebooks
- Or **Code Ocean** for full reproducibility platform

### HPC Cluster
- **Singularity** containers
- Or **Spack** for package management
- Plus job scheduler (SLURM, PBS)

### Maximum Reproducibility
- **Nix** for bit-for-bit reproducibility
- Or **Docker** with pinned base image tags and checksums
- Version control everything (code, Dockerfile, lock files)

### Team Collaboration
- **Docker Compose** for multi-service setups
- Or **conda** with environment.yml in git
- Plus CI/CD for validation

### Long-term Archival
- **Docker images** pushed to registry
- **Zenodo** or **Docker Hub** for permanent storage
- Include checksums and version tags

## Key Takeaways

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

---

**Bottom Line**: For most reproducible experiments, use **conda** (Python scientific) or **language-specific tools** for development, then package in **Docker** for sharing and long-term reproducibility. For maximum reproducibility or HPC work, consider **Singularity** or **Nix**.
