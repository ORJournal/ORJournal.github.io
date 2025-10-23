# Dependency Management in R

R's dependency management has evolved from basic built-in tools to modern solutions that provide reproducibility and isolation.

## Core Package Management

### Built-in Functions

R includes basic package management functionality:

```r
# Install packages
install.packages("dplyr")
install.packages(c("ggplot2", "tidyr"))

# Load packages
library(dplyr)
require(ggplot2)  # Similar but returns FALSE if not available

# Update packages
update.packages()

# Remove packages
remove.packages("dplyr")
```

### Package Repositories

- **CRAN**: Comprehensive R Archive Network (primary repository)
- **Bioconductor**: Bioinformatics packages
- **GitHub**: Development versions and custom packages

```r
# From CRAN (default)
install.packages("dplyr")

# From Bioconductor
BiocManager::install("DESeq2")

# From GitHub
devtools::install_github("tidyverse/dplyr")
```

## Package Description Files

### DESCRIPTION

For R packages, the `DESCRIPTION` file specifies dependencies:

```
Package: mypackage
Version: 0.1.0
Imports:
    dplyr (>= 1.0.0),
    ggplot2
Suggests:
    testthat,
    knitr
Depends:
    R (>= 4.0.0)
```

- **Imports**: Packages needed for your package to work
- **Suggests**: Optional packages (for vignettes, tests, etc.)
- **Depends**: Packages attached when yours is loaded
- **LinkingTo**: For C++ dependencies

### Version Specifications

```
dplyr (>= 1.0.0)           # Minimum version
ggplot2 (>= 3.0, < 4.0)    # Version range
tidyr (== 1.2.0)           # Exact version (rare)
```

## Modern Dependency Management Tools

### 1. **renv** (Recommended)

The modern standard for project-level dependency management:

```r
# Initialize renv for a project
renv::init()

# Install packages (tracked automatically)
install.packages("dplyr")

# Save the current state
renv::snapshot()

# Restore from lockfile
renv::restore()

# Update packages
renv::update()
```

**Project Structure**:
```
myproject/
├── renv.lock          # Lockfile with exact versions
├── renv/              # Project library
│   └── library/       # Installed packages
├── .Rprofile          # Auto-activates renv
└── renv/activate.R    # Activation script
```

**renv.lock** example:
```json
{
  "R": {
    "Version": "4.2.0",
    "Repositories": [
      {
        "Name": "CRAN",
        "URL": "https://cran.rstudio.com"
      }
    ]
  },
  "Packages": {
    "dplyr": {
      "Package": "dplyr",
      "Version": "1.0.9",
      "Source": "Repository",
      "Repository": "CRAN"
    }
  }
}
```

**Key Features**:
- Project-specific libraries (isolated environments)
- Reproducible with `renv.lock`
- Caches packages globally to save space
- Integrates with RStudio

### 2. **packrat** (Legacy)

The predecessor to renv, now superseded:

```r
packrat::init()
packrat::snapshot()
packrat::restore()
```

**Note**: Use renv instead for new projects.

### 3. **checkpoint**

Uses MRAN (Microsoft R Archive Network) time-based snapshots:

```r
library(checkpoint)
checkpoint("2023-01-15")  # Use packages as of this date
```

**Status**: MRAN was deprecated in 2022; limited usefulness now.

## Dependency Resolution

### Basic Approach

R's built-in `install.packages()`:
- Automatically installs dependencies listed in `Imports` and `Depends`
- Uses the latest compatible versions from repositories
- No sophisticated version conflict resolution

### With renv

```r
# Check for issues
renv::diagnostics()

# See dependency tree
renv::dependencies()
```

## Environment Management

### System Library vs. User Library

R has multiple library paths:

```r
.libPaths()  # View library locations
# [1] "~/R/library"           # User library
# [2] "/usr/lib/R/library"    # System library
```

### Project Isolation

**Without renv**:
- Packages installed globally
- All projects share same package versions
- Can lead to conflicts

**With renv**:
- Each project has its own library
- Different projects can use different package versions
- Isolated from system library

## Package Development

### devtools Workflow

For developing packages:

```r
library(devtools)

# Load your package for development
load_all()

# Install dependencies from DESCRIPTION
install_deps()

# Check package
check()

# Install your package
install()
```

### roxygen2 for Documentation

```r
#' @importFrom dplyr filter mutate
#' @import ggplot2
```

These tags in your documentation generate the `DESCRIPTION` file dependencies.

## Docker Integration

For ultimate reproducibility:

```dockerfile
FROM rocker/r-ver:4.2.0

RUN R -e "install.packages('renv')"

COPY renv.lock renv.lock
RUN R -e "renv::restore()"
```

The `rocker` project provides versioned R Docker images.

## Best Practices

1. **Use renv for projects**: Ensures reproducibility and isolation
2. **Commit renv.lock**: Version control your lockfile, not `renv/library/`
3. **Specify minimum versions**: In `DESCRIPTION` files for packages
4. **Regular snapshots**: Run `renv::snapshot()` after package changes
5. **Use `.Rprofile`**: renv creates this to auto-activate
6. **Document session info**: Use `sessionInfo()` to record your environment

```r
# Save session information
writeLines(capture.output(sessionInfo()), "session_info.txt")
```

## Common Workflows

### Starting a New Project

```r
# Create project directory
dir.create("myproject")
setwd("myproject")

# Initialize renv
renv::init()

# Install packages
install.packages("tidyverse")

# Save state
renv::snapshot()
```

### Collaborating

```r
# Team member clones repo
git clone repo_url
cd repo

# Open R in project
# renv activates automatically

# Install exact versions
renv::restore()
```

### Updating Dependencies

```r
# Update a package
install.packages("dplyr")

# Record the update
renv::snapshot()

# If issues arise, rollback
renv::restore()
```

---

**Key Differences from Python/Julia**:
- R's built-in dependency management is simpler but less sophisticated
- **renv** is the modern solution (similar to Python's Poetry or Julia's Pkg)
- No built-in semantic versioning or complex resolution
- Heavy reliance on CRAN's stable package ecosystem
- Environment isolation was an afterthought, not built-in from the start
