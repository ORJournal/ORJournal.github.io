# Compiling Tables and Figures

## Python-Based Pipeline

The modern standard for reproducible research.

### Data Collection and Storage

```python
# collect_results.py
import pandas as pd
import json
from pathlib import Path

def collect_experiment_results(experiment_dir):
    """Collect results from multiple experiments"""
    results = []
    
    for exp_path in Path(experiment_dir).glob('exp_*/metrics.json'):
        with open(exp_path, 'r') as f:
            metrics = json.load(f)
            results.append(metrics)
    
    df = pd.DataFrame(results)
    df.to_csv('results/all_experiments.csv', index=False)
    return df

# Usage
df = collect_experiment_results('experiments/')
```

### Statistical Analysis

```python
# analyze_results.py
import pandas as pd
import numpy as np
from scipy import stats

def compute_summary_statistics(df, group_by='model'):
    """Compute mean and confidence intervals"""
    summary = df.groupby(group_by).agg({
        'accuracy': ['mean', 'std', 'count'],
        'f1_score': ['mean', 'std', 'count']
    })
    
    # Add confidence intervals
    for metric in ['accuracy', 'f1_score']:
        n = summary[(metric, 'count')]
        mean = summary[(metric, 'mean')]
        std = summary[(metric, 'std')]
        
        # 95% confidence interval
        ci = stats.t.ppf(0.975, n-1) * std / np.sqrt(n)
        summary[(metric, 'ci95')] = ci
    
    return summary

# Usage
df = pd.read_csv('results/all_experiments.csv')
summary = compute_summary_statistics(df)
```

### Table Generation

1. LaTeX Tables with pandas

   ```python
   # generate_tables.py
   import pandas as pd

   def create_results_table(df, output_file='tables/results.tex'):
       """Generate LaTeX table from results"""
    
       # Format numbers
       styled_df = df.style.format({
           'accuracy': '{:.3f}',
           'precision': '{:.3f}',
           'recall': '{:.3f}',
           'f1_score': '{:.3f}'
       })
    
       # Generate LaTeX
       latex = df.to_latex(
           index=True,
           caption='Experimental results on test dataset',
           label='tab:results',
           column_format='lcccc',
           float_format='%.3f',
           escape=False
       )
    
       # Save
       Path(output_file).parent.mkdir(exist_ok=True)
       with open(output_file, 'w') as f:
           f.write(latex)
    
       return latex

   # Usage
   summary = compute_summary_statistics(df)
   create_results_table(summary, 'tables/summary.tex')
   ```

   **Generated LaTeX:**
   ```latex
   \begin{table}[h]
   \centering
   \caption{Experimental results on test dataset}
   \label{tab:results}
   \begin{tabular}{lcccc}
   \toprule
   Model & Accuracy & Precision & Recall & F1-Score \\
   \midrule
   Baseline & 0.854 & 0.812 & 0.893 & 0.851 \\
   Model A & 0.892 & 0.865 & 0.911 & 0.887 \\
   Model B & 0.913 & 0.893 & 0.927 & 0.910 \\
   \bottomrule
   \end{tabular}
   \end{table}
   ```

1. Stargazer (R-style tables in Python)**

   ```python
   from stargazer.stargazer import Stargazer

   # Create regression table
   stargazer = Stargazer([model1, model2, model3])
   stargazer.render_latex()
   ```

### Figure Generation

**matplotlib + seaborn for publication-quality figures:**

```python
# generate_figures.py
import matplotlib.pyplot as plt
import seaborn as sns
import pandas as pd

# Set publication style
plt.rcParams.update({
    'font.size': 10,
    'font.family': 'serif',
    'text.usetex': True,  # Use LaTeX for text
    'figure.figsize': (6, 4),
    'figure.dpi': 300
})

def create_comparison_plot(df, output_file='figures/comparison.pdf'):
    """Create bar plot comparing models"""
    fig, ax = plt.subplots()
    
    # Plot
    x = df['model']
    y = df['accuracy']
    yerr = df['ci95']
    
    ax.bar(x, y, yerr=yerr, capsize=5, alpha=0.8)
    ax.set_ylabel('Accuracy')
    ax.set_xlabel('Model')
    ax.set_ylim([0.8, 1.0])
    ax.grid(axis='y', alpha=0.3)
    
    plt.tight_layout()
    plt.savefig(output_file, bbox_inches='tight')
    plt.close()

def create_learning_curves(history, output_file='figures/learning_curve.pdf'):
    """Create learning curve plot"""
    fig, (ax1, ax2) = plt.subplots(1, 2, figsize=(10, 4))
    
    # Loss
    ax1.plot(history['train_loss'], label='Training')
    ax1.plot(history['val_loss'], label='Validation')
    ax1.set_xlabel('Epoch')
    ax1.set_ylabel('Loss')
    ax1.legend()
    ax1.grid(alpha=0.3)
    
    # Accuracy
    ax2.plot(history['train_acc'], label='Training')
    ax2.plot(history['val_acc'], label='Validation')
    ax2.set_xlabel('Epoch')
    ax2.set_ylabel('Accuracy')
    ax2.legend()
    ax2.grid(alpha=0.3)
    
    plt.tight_layout()
    plt.savefig(output_file, bbox_inches='tight')
    plt.close()

# Usage
df = pd.read_csv('results/summary.csv')
create_comparison_plot(df)
```

**Advanced: Using seaborn for statistical plots:**

```python
import seaborn as sns

def create_distribution_plot(df, output_file='figures/distributions.pdf'):
    """Create violin plot with distributions"""
    fig, ax = plt.subplots(figsize=(8, 6))
    
    sns.violinplot(
        data=df,
        x='model',
        y='accuracy',
        ax=ax
    )
    sns.swarmplot(
        data=df,
        x='model',
        y='accuracy',
        color='black',
        alpha=0.5,
        size=3,
        ax=ax
    )
    
    ax.set_ylabel('Accuracy')
    ax.set_xlabel('Model')
    plt.tight_layout()
    plt.savefig(output_file, bbox_inches='tight')
    plt.close()
```

## R-Based Pipeline

R excels at statistical analysis and publication-quality figures.

### Data Analysis and Tables

```r
# analyze_results.R
library(tidyverse)
library(kableExtra)

# Load data
df <- read_csv("results/all_experiments.csv")

# Compute summary statistics
summary_stats <- df %>%
  group_by(model) %>%
  summarise(
    mean_acc = mean(accuracy),
    sd_acc = sd(accuracy),
    n = n(),
    se = sd_acc / sqrt(n),
    ci95 = qt(0.975, n-1) * se
  )

# Create LaTeX table
summary_stats %>%
  kable(
    format = "latex",
    digits = 3,
    col.names = c("Model", "Mean", "SD", "N", "SE", "95% CI"),
    caption = "Summary statistics for model accuracy",
    label = "tab:summary",
    booktabs = TRUE
  ) %>%
  kable_styling(latex_options = c("striped", "hold_position")) %>%
  write_lines("tables/summary.tex")
```

### Figure Generation

```r
# generate_figures.R
library(ggplot2)
library(ggpubr)

# Set theme for publication
theme_set(theme_classic(base_size = 10))

# Create comparison plot
p <- ggplot(summary_stats, aes(x = model, y = mean_acc)) +
  geom_bar(stat = "identity", fill = "steelblue", alpha = 0.8) +
  geom_errorbar(
    aes(ymin = mean_acc - ci95, ymax = mean_acc + ci95),
    width = 0.2
  ) +
  labs(
    x = "Model",
    y = "Accuracy",
    title = "Model Comparison"
  ) +
  ylim(0.8, 1.0)

# Save as PDF (vector format)
ggsave("figures/comparison.pdf", p, width = 6, height = 4)

# Box plot with statistical tests
p2 <- ggplot(df, aes(x = model, y = accuracy)) +
  geom_boxplot(fill = "lightblue", alpha = 0.6) +
  geom_jitter(width = 0.2, alpha = 0.5) +
  stat_compare_means(method = "anova") +  # Add p-value
  labs(x = "Model", y = "Accuracy")

ggsave("figures/boxplot.pdf", p2, width = 6, height = 4)
```

## Jupyter Notebooks

Combine code, analysis, and narrative in one document.

```python
# research_analysis.ipynb

# Cell 1: Setup
import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns

# Cell 2: Load and explore data
df = pd.read_csv('results/experiments.csv')
df.describe()

# Cell 3: Statistical analysis
from scipy import stats

# Perform t-test
baseline = df[df['model'] == 'baseline']['accuracy']
model_a = df[df['model'] == 'model_a']['accuracy']
t_stat, p_value = stats.ttest_ind(baseline, model_a)

print(f"t-statistic: {t_stat:.3f}, p-value: {p_value:.4f}")

# Cell 4: Create figure
fig, ax = plt.subplots()
sns.boxplot(data=df, x='model', y='accuracy', ax=ax)
plt.savefig('figures/comparison.pdf')
plt.show()

# Cell 5: Generate table
summary = df.groupby('model')['accuracy'].agg(['mean', 'std'])
summary.to_latex('tables/summary.tex')
summary
```

**Export options:**
- PDF via LaTeX
- HTML for supplementary materials
- Python script for automation

## R Markdown

The R equivalent - excellent for statistical reports.

```r
library(tidyverse)
library(knitr)

## Data Loading

df <- read_csv("results/experiments.csv")
summary(df)

## Statistical Analysis

# ANOVA test
model_comparison <- aov(accuracy ~ model, data = df)
summary(model_comparison)

## Results Table

summary_stats <- df %>%
  group_by(model) %>%
  summarise(
    Mean = mean(accuracy),
    SD = sd(accuracy),
    N = n()
  )

kable(summary_stats, caption = "Model Performance Summary")

## Visualization

ggplot(df, aes(x = model, y = accuracy)) +
  geom_boxplot() +
  theme_minimal()

## Render

rmarkdown::render("analysis.Rmd", output_format = "pdf_document")
```

## Quarto (Modern Alternative)

Cross-language literate programming (Python, R, Julia).

```markdown
---
title: "Research Results"
format: pdf
jupyter: python3
---

## Analysis

import pandas as pd
df = pd.read_csv('results.csv')

## Figure

#| label: fig-comparison
#| fig-cap: "Model comparison"

import matplotlib.pyplot as plt
plt.plot(df['epoch'], df['accuracy'])
plt.show()
```
```

## Automated Generation

Makefiles for Reproducibility

```makefile
# Makefile

all: tables figures paper.pdf

# Data processing
results/summary.csv: scripts/analyze_results.py results/raw_data.csv
	python scripts/analyze_results.py

# Tables
tables/results.tex: scripts/generate_tables.py results/summary.csv
	python scripts/generate_tables.py

# Figures
figures/comparison.pdf: scripts/generate_figures.py results/summary.csv
	python scripts/generate_figures.py

# Compile paper
paper.pdf: paper.tex tables/results.tex figures/comparison.pdf
	pdflatex paper.tex
	bibtex paper
	pdflatex paper.tex
	pdflatex paper.tex

clean:
	rm -f results/*.csv tables/*.tex figures/*.pdf paper.pdf

.PHONY: all clean
```

**Usage:**
```bash
make           # Build everything
make figures   # Just regenerate figures
make clean     # Remove generated files
```

### Python Scripts with Click

```python
# scripts/compile_paper.py
import click
import subprocess
from pathlib import Path

@click.group()
def cli():
    """Compile academic paper materials"""
    pass

@cli.command()
def analyze():
    """Run data analysis"""
    click.echo("Analyzing results...")
    subprocess.run(['python', 'scripts/analyze_results.py'])

@cli.command()
def tables():
    """Generate all tables"""
    click.echo("Generating tables...")
    subprocess.run(['python', 'scripts/generate_tables.py'])

@cli.command()
def figures():
    """Generate all figures"""
    click.echo("Generating figures...")
    subprocess.run(['python', 'scripts/generate_figures.py'])

@cli.command()
def compile():
    """Compile LaTeX paper"""
    click.echo("Compiling paper...")
    subprocess.run(['pdflatex', 'paper.tex'])

@cli.command()
def all():
    """Run complete pipeline"""
    analyze()
    tables()
    figures()
    compile()

if __name__ == '__main__':
    cli()
```

**Usage:**
```bash
python scripts/compile_paper.py all
python scripts/compile_paper.py figures  # Just figures
```

## Specialized Tools

### For Tables

#### Python: tabulate

```python
from tabulate import tabulate

data = [
    ["Model A", 0.892, 0.865],
    ["Model B", 0.913, 0.893],
]

print(tabulate(data, 
               headers=["Model", "Accuracy", "Precision"],
               tablefmt="latex_booktabs"))
```

#### Python: pytablewriter

```python
import pytablewriter

writer = pytablewriter.LatexTableWriter(
    table_name="results",
    headers=["Model", "Accuracy", "F1-Score"],
    value_matrix=[
        ["Baseline", 0.854, 0.851],
        ["Model A", 0.892, 0.887],
    ]
)
writer.write_table()
```

#### R: gt (Grammar of Tables)

```r
library(gt)

summary_stats %>%
  gt() %>%
  tab_header(title = "Model Performance") %>%
  fmt_number(columns = c(mean_acc, sd_acc), decimals = 3) %>%
  cols_label(
    model = "Model",
    mean_acc = "Mean Accuracy",
    sd_acc = "SD"
  ) %>%
  as_latex()
```

### For Figures

#### Python: plotly (Interactive)

```python
import plotly.graph_objects as go

fig = go.Figure(data=[
    go.Bar(name='Model A', x=['Accuracy', 'Precision'], y=[0.89, 0.86]),
    go.Bar(name='Model B', x=['Accuracy', 'Precision'], y=[0.91, 0.89])
])

fig.update_layout(barmode='group')
fig.write_image("figures/comparison.pdf")  # Static export
fig.write_html("figures/comparison.html")  # Interactive
```

#### Python: PGFPlots (LaTeX integration)

```python
import matplotlib.pyplot as plt
import matplotlib
matplotlib.use("pgf")

plt.rcParams.update({
    "pgf.texsystem": "pdflatex",
    "pgf.rcfonts": False,
})

# Create plot
plt.plot([1, 2, 3], [1, 4, 9])
plt.savefig("figure.pgf")  # Save as PGF for LaTeX
```

#### R: patchwork (Complex layouts)

```r
library(patchwork)

p1 <- ggplot(df, aes(x = epoch, y = loss)) + geom_line()
p2 <- ggplot(df, aes(x = epoch, y = accuracy)) + geom_line()
p3 <- ggplot(df, aes(x = model, y = accuracy)) + geom_boxplot()

# Combine plots
(p1 | p2) / p3

ggsave("figures/combined.pdf", width = 10, height = 8)
```


**Color-blind friendly palettes:**
```python
# matplotlib
colors = ['#0173B2', '#DE8F05', '#029E73']  # Blue, Orange, Green

# seaborn
sns.set_palette("colorblind")

# R
library(viridis)
scale_color_viridis_d()
```

## Complete Example Workflow

### Project Structure

```
paper/
├── data/
│   ├── raw/                  # Original data (read-only)
│   └── processed/            # Cleaned data
├── scripts/
│   ├── 01_process_data.py
│   ├── 02_analyze_results.py
│   ├── 03_generate_tables.py
│   └── 04_generate_figures.py
├── results/
│   ├── summary.csv
│   └── statistics.json
├── tables/
│   ├── table1.tex
│   └── table2.tex
├── figures/
│   ├── fig1.pdf
│   └── fig2.pdf
├── paper.tex
├── Makefile
├── requirements.txt
└── README.md
```

### Master Script

```python
#!/usr/bin/env python3
# compile_all.py

"""
Master script to compile all tables and figures for the paper.
Run this after experiments are complete.
"""

import subprocess
from pathlib import Path

def run_script(script_path):
    """Run a Python script and check for errors"""
    print(f"Running {script_path}...")
    result = subprocess.run(['python', script_path], capture_output=True)
    if result.returncode != 0:
        print(f"Error in {script_path}:")
        print(result.stderr.decode())
        return False
    print(f"✓ {script_path} completed successfully")
    return True

def main():
    """Run complete compilation pipeline"""
    
    # Create output directories
    for dir_name in ['results', 'tables', 'figures']:
        Path(dir_name).mkdir(exist_ok=True)
    
    # Run scripts in order
    scripts = [
        'scripts/01_process_data.py',
        'scripts/02_analyze_results.py',
        'scripts/03_generate_tables.py',
        'scripts/04_generate_figures.py',
    ]
    
    for script in scripts:
        if not run_script(script):
            print("Pipeline failed. Fix errors and try again.")
            return 1
    
    print("\n✓ All tables and figures generated successfully!")
    print("Ready to compile paper.tex")
    
    return 0

if __name__ == '__main__':
    exit(main())
```

