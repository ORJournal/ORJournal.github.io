# Dependency Management in Julia

Julia has a built-in package manager called `Pkg` that provides sophisticated dependency management out of the box.

## Core Concepts

### 1. **Project.toml**

The manifest file that specifies your project's direct dependencies:

```toml
name = "MyProject"
uuid = "12345678-1234-1234-1234-123456789abc"
version = "0.1.0"

[deps]
DataFrames = "a93c6f00-e57d-5684-b7b6-d8193f3e46c0"
Plots = "91a5bcdd-55d7-5caf-9e0b-520d859cae80"

[compat]
DataFrames = "1.3"
Plots = "1.25"
julia = "1.6"
```

- Lists direct dependencies with their UUIDs
- `[compat]` section specifies version requirements
- Human-readable and version-controlled

### 2. **Manifest.toml**

The lock file that captures the entire dependency graph with exact versions:

```toml
[[DataFrames]]
deps = ["Compat", "DataAPI", "Tables"]
uuid = "a93c6f00-e57d-5684-b7b6-d8193f3e46c0"
version = "1.3.4"

[[Tables]]
deps = ["DataAPI"]
uuid = "bd369af6-aec1-5ad0-b16a-f7cc5008161c"
version = "1.7.0"
```

- Auto-generated, should not be edited manually
- Ensures reproducible environments
- Includes all transitive dependencies with exact versions

## Using Pkg

### Basic Commands

**REPL mode** (press `]` to enter):
```julia
add DataFrames          # Add a package
add DataFrames@1.3      # Add specific version
rm DataFrames           # Remove a package
update                  # Update all packages
status                  # List installed packages
```

**Script mode**:
```julia
using Pkg
Pkg.add("DataFrames")
Pkg.instantiate()       # Install from Manifest.toml
```

## Environments

Julia supports isolated environments similar to Python's virtual environments:

### Project-Level Environments

```julia
# Create a new project
Pkg.activate("path/to/project")
Pkg.add("DataFrames")
```

This creates `Project.toml` and `Manifest.toml` in the project directory.

### Global Environment

```julia
Pkg.activate()  # Switch to default environment
```

The default environment is located at `~/.julia/environments/v1.x/`.

## Dependency Resolution

Julia's package manager uses a sophisticated resolver that:

- Handles semantic versioning automatically
- Resolves version conflicts across the dependency tree
- Finds compatible versions of all packages
- Updates `Manifest.toml` with resolved versions

### Version Compatibility

The `[compat]` section uses standard version specifiers:

```toml
[compat]
DataFrames = "1.3"           # >=1.3.0, <2.0.0
Plots = "1.25.0 - 1.30.0"    # Range
CSV = "^0.10"                # Caret (>=0.10.0, <0.11.0)
```

## Registries

Julia packages are published to registries (default: General Registry):

- Packages are identified by name and UUID
- UUIDs prevent naming conflicts
- Multiple registries can be used simultaneously

### Adding Packages from URLs

```julia
# From GitHub
Pkg.add(url="https://github.com/user/Package.jl")

# From local path
Pkg.develop(path="path/to/Package")
```

## Development Workflow

### Developing a Package

```julia
Pkg.develop("MyPackage")  # Clone package for local development
Pkg.free("MyPackage")     # Return to using registry version
```

### Creating a New Package

```julia
Pkg.generate("MyPackage")
```

Creates a package structure with `Project.toml`, `src/`, and `test/`.

## Key Features

1. **Automatic Dependency Resolution**: No need for separate tools like pip-tools or Poetry
2. **Reproducible Environments**: `Manifest.toml` ensures exact version reconstruction
3. **UUID-based Identification**: Prevents naming conflicts
4. **Built-in**: No external tools required
5. **Per-Project Isolation**: Easy environment switching
6. **Precompilation**: Packages are precompiled for faster loading

## Best Practices

- **Commit both files**: Version control both `Project.toml` and `Manifest.toml`
- **Use `[compat]`**: Specify compatibility bounds to avoid breaking changes
- **Run `Pkg.instantiate()`**: On new machines to recreate exact environment
- **Separate environments**: Use project-specific environments, not the global one
- **Test compatibility**: Run `Pkg.test()` to verify package functionality

---

**Key Difference from Python**: Julia's dependency management is fully integrated into the language from the start, providing a unified experience without needing third-party tools like Poetry, Pipenv, or conda.
