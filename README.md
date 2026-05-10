# flow

Flow cytometry analysis notebooks in R (Bioconductor) and Python.

## Notebooks

| # | Notebook | Kernel | Description |
|---|----------|--------|-------------|
| 1 | `01_csv_matrix_analysis.ipynb` | R | Analyze exported CSV/matrix flow data |
| 2 | `02_single_fcs_analysis.ipynb` | R | Full pipeline for a single .fcs file |
| 3 | `03_batch_fcs_analysis.ipynb` | R | Batch analysis of multiple .fcs files |
| 4 | `04_flow_analysis_python.ipynb` | Python | Lightweight Python alternative |

## Setup

### R kernel for Jupyter

```bash
# Install IRkernel in R
R -e 'install.packages("IRkernel", repos="https://cloud.r-project.org")'
R -e 'IRkernel::installspec(user = TRUE)'

# Verify
jupyter kernelspec list
```

### R packages (Bioconductor)

```r
if (!requireNamespace("BiocManager", quietly = TRUE))
    install.packages("BiocManager", repos = "https://cloud.r-project.org")

BiocManager::install(c(
    "flowCore", "flowWorkspace", "openCyto",
    "FlowSOM", "CATALYST", "ggcyto"
))

install.packages(c(
    "ggplot2", "dplyr", "tidyr", "readr", "pheatmap",
    "uwot", "Rtsne", "viridis", "patchwork"
), repos = "https://cloud.r-project.org")
```

### Python packages

```bash
pip install flowkit fcsparser numpy pandas matplotlib seaborn
pip install umap-learn scikit-learn leidenalg
```

## Data

Place your files in a `data/` directory:

```
data/
├── matrix_data.csv        # exported CSV matrices
├── sample.fcs             # single .fcs file
└── batch/                 # multiple .fcs files
    ├── sample_01.fcs
    ├── sample_02.fcs
    └── ...
```
