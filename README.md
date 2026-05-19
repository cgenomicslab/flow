# Flow Cytometry Analysis

R-based analysis notebooks for flow cytometry data — from raw .fcs files to gating, clustering, and UMAP. A lightweight Python alternative is also included.

---

## Notebooks

| # | Notebook | Description |
|---|----------|-------------|
| 1 | [`01_csv_matrix_analysis.ipynb`](notebooks/01_csv_matrix_analysis.ipynb) | Analyze data exported as CSV/matrix (e.g. from FlowJo, FACS Diva) |
| 2 | [`02_single_fcs_analysis.ipynb`](notebooks/02_single_fcs_analysis.ipynb) | Full pipeline for a single .fcs file: compensate → gate → cluster → UMAP |
| 3 | [`03_batch_fcs_analysis.ipynb`](notebooks/03_batch_fcs_analysis.ipynb) | Same pipeline across multiple .fcs files with cross-sample comparison |
| 4 | [`04_flow_analysis_python.ipynb`](notebooks/04_flow_analysis_python.ipynb) | Python alternative using flowkit/fcsparser |

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

On **Windows** and **Intel macOS**, pre-built binaries are downloaded automatically — no compilation needed. On **Apple Silicon (M1/M2/M3) macOS**, the native path is untested; Bioconductor should ship osx-arm64 binaries for CRAN R, but if `BiocManager` falls back to building `RProtoBufLib` from source you'll hit an `arm64-apple-darwin` autoconf error. See the [Apple Silicon notes](#apple-silicon-notes--rprotobuflib-build-fix) under the conda section for the fix.

### Reproducible setup (conda / mamba)

For a pinned environment matching the lab server, use the conda files in this repo:

```bash
# Linux (server) — installs everything including Bioconductor packages from bioconda
mamba env create -f environment_server.yml

# macOS / Windows — installs R, CRAN, and Python deps only
mamba env create -f environment.yml
```

On osx-arm64, Bioconductor packages aren't available from bioconda — install them via `BiocManager` from inside the env's R, using the same `BiocManager::install(...)` call above. This is the path that needs the fix below.

<details>
<summary><b id="apple-silicon-notes--rprotobuflib-build-fix">Apple Silicon notes — RProtoBufLib build fix</b></summary>

When `BiocManager` runs inside a conda env on osx-arm64, it builds `RProtoBufLib` (a dependency of `flowWorkspace` / `cytolib`) from source. The bundled protobuf 3.8.0 ships an autoconf that doesn't recognise `arm64-apple-darwin`, and the build fails with `Invalid configuration ... unrecognized machine`. To fix:

1. Download the package source and unpack it:
   ```r
   download.file(
       "https://bioconductor.org/packages/release/bioc/src/contrib/RProtoBufLib_<version>.tar.gz",
       "RProtoBufLib.tar.gz"
   )
   ```
   ```bash
   tar -xzf RProtoBufLib.tar.gz
   ```
2. Replace the bundled `config.sub` with a current one:
   ```bash
   curl -o RProtoBufLib/src/protobuf-3.8.0/config.sub \
       https://git.savannah.gnu.org/cgit/config.git/plain/config.sub
   ```
3. Install from the patched source:
   ```bash
   R CMD INSTALL RProtoBufLib
   ```
4. Then continue in R: `BiocManager::install(c("cytolib", "flowCore", "flowWorkspace", "ggcyto", "openCyto", "FlowSOM"))`.

Do **not** install `libprotobuf` from conda-forge alongside this — the conda build (≥3.21) requires C++17 and breaks `RProtoBufLib`'s C++11 link step.

</details>

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
