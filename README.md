# Flow Cytometry Analysis

R-based analysis notebooks for flow cytometry data — from raw .fcs files to gating, clustering, and UMAP. A lightweight Python alternative is also included.

---

## Notebooks

| # | Notebook | Description |
|---|----------|-------------|
| 1 | `01_csv_matrix_analysis.ipynb` | Analyze data exported as CSV/matrix (e.g. from FlowJo, FACS Diva) |
| 2 | `02_single_fcs_analysis.ipynb` | Full pipeline for a single .fcs file: compensate → gate → cluster → UMAP |
| 3 | `03_batch_fcs_analysis.ipynb` | Same pipeline across multiple .fcs files with cross-sample comparison |
| 4 | `04_flow_analysis_python.ipynb` | Python alternative using flowkit/fcsparser |

All notebooks are templates — adjust file paths, gate thresholds, and channel names to match your data.

---

## Data

Place your files in the `data/` folder:

```
data/
├── matrix_data.csv        # CSV export from FlowJo / FACS Diva  (notebook 1)
├── sample.fcs             # single .fcs file                     (notebook 2)
└── batch/                 # folder with multiple .fcs files       (notebook 3)
    ├── sample_01.fcs
    ├── sample_02.fcs
    └── ...
```

---

## Installation

Install [R](https://cran.r-project.org) and [RStudio](https://posit.co/download/rstudio-desktop/) (free), then run the following once in the R console:

```r
# CRAN packages
install.packages(c(
    "ggplot2", "dplyr", "tidyr", "readr",
    "uwot", "Rtsne", "viridis", "patchwork", "pheatmap"
))

# Bioconductor packages
if (!requireNamespace("BiocManager", quietly = TRUE))
    install.packages("BiocManager")

BiocManager::install(c(
    "flowCore", "flowWorkspace", "ggcyto", "openCyto", "FlowSOM"
))
```

This works on **Windows** and **macOS** — pre-built binaries are downloaded automatically, no compilation needed.

### For notebook 4 (Python) only

```bash
pip install flowkit fcsparser umap-learn scikit-learn pandas matplotlib seaborn
```

---

## Running

**In RStudio** — open a notebook (`.ipynb`) directly in RStudio or copy-paste sections into a script. Adjust the file paths and thresholds at the top of each section to match your data.

**In VSCode or JupyterLab** — open the notebook and select the `R (flow)` kernel. Requires the additional setup below.

<details>
<summary>Jupyter kernel setup</summary>

```r
install.packages("IRkernel")
IRkernel::installspec(name = "flow_r", displayname = "R (flow)")
```

</details>

---

## Lab server (temporary)

The lab JupyterHub at [jupyter.cgenomicslab.org](https://jupyter.cgenomicslab.org) has everything pre-installed. The notebooks are at `~/Projects/flow`. Pull updates with:

```bash
cd ~/Projects/flow && git pull
```
