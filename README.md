# flow

Flow cytometry analysis notebooks in R (Bioconductor) and Python.

## Lab server (jupyter.cgenomicslab.org)

If you're on the lab JupyterHub, the repo is already cloned at `~/Projects/flow`. To pull updates:

```bash
cd ~/Projects/flow
git pull
```

---

## Notebooks

| # | Notebook | Kernel | Description |
|---|----------|--------|-------------|
| 1 | `01_csv_matrix_analysis.ipynb` | R | Analyze exported CSV/matrix flow data |
| 2 | `02_single_fcs_analysis.ipynb` | R | Full pipeline for a single .fcs file |
| 3 | `03_batch_fcs_analysis.ipynb` | R | Batch analysis of multiple .fcs files |
| 4 | `04_flow_analysis_python.ipynb` | Python | Lightweight Python alternative |

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

---

## Local setup

### Linux (x86_64)

```bash
conda create -n flow -c conda-forge -c bioconda \
    r-base=4.4 \
    r-irkernel \
    jupyter jupyterlab \
    r-ggplot2 r-dplyr r-tidyr r-readr \
    r-pheatmap r-viridis r-patchwork \
    r-uwot r-rtsne \
    bioconductor-flowcore \
    bioconductor-flowworkspace \
    bioconductor-ggcyto \
    bioconductor-opencyto \
    bioconductor-flowsom \
    bioconductor-catalyst \
    python=3.11 \
    numpy pandas matplotlib seaborn \
    scikit-learn umap-learn \
    -y

conda activate flow
pip install flowkit fcsparser leidenalg
R -e 'IRkernel::installspec(name = "flow_r", displayname = "R (flow)", user = FALSE)'
```

Or use the environment file:

```bash
conda env create -f environment_server.yml
conda activate flow
pip install flowkit fcsparser leidenalg
R -e 'IRkernel::installspec(name = "flow_r", displayname = "R (flow)", user = FALSE)'
```

### macOS / Apple Silicon

Bioconductor conda binaries are often missing for `osx-arm64`. Install the base environment via conda, then Bioconductor from R:

```bash
conda create -n flow -c conda-forge \
    r-base=4.4 \
    r-irkernel \
    jupyter jupyterlab \
    r-ggplot2 r-dplyr r-tidyr r-readr \
    r-pheatmap r-viridis r-patchwork \
    r-uwot r-rtsne \
    python=3.11 \
    numpy pandas matplotlib seaborn \
    scikit-learn umap-learn \
    -y

conda activate flow
pip install flowkit fcsparser leidenalg
R -e 'IRkernel::installspec(name = "flow_r", displayname = "R (flow)", user = TRUE)'
```

Then install Bioconductor packages from R:

```r
if (!requireNamespace("BiocManager", quietly = TRUE))
    install.packages("BiocManager", repos = "https://cloud.r-project.org")

BiocManager::install(c(
    "flowCore", "flowWorkspace", "openCyto",
    "FlowSOM", "CATALYST", "ggcyto"
))
```

Or use the environment file:

```bash
conda env create -f environment.yml
conda activate flow
# Then run the BiocManager::install block above in R
```

### Apple Silicon troubleshooting

`flowWorkspace` and `RProtoBufLib` need protobuf and HDF5 headers to compile.

**Compilers and libraries:**
```bash
xcode-select --install
conda install -n flow -c conda-forge \
    gfortran_osx-arm64 clang_osx-arm64 clangxx_osx-arm64 \
    protobuf hdf5
```

**Point R at conda's libraries:**
```bash
conda activate flow
export PKG_CONFIG_PATH="$CONDA_PREFIX/lib/pkgconfig:$PKG_CONFIG_PATH"
export LDFLAGS="-L$CONDA_PREFIX/lib"
export CPPFLAGS="-I$CONDA_PREFIX/include"
R -e 'BiocManager::install("flowWorkspace")'
```

**If nothing works — Rosetta x86 fallback:**
```bash
CONDA_SUBDIR=osx-64 conda create -n flow_x86 -c conda-forge -c bioconda \
    r-base=4.4 r-irkernel jupyter \
    bioconductor-flowcore bioconductor-flowworkspace \
    bioconductor-opencyto bioconductor-flowsom \
    bioconductor-ggcyto bioconductor-catalyst \
    -y
conda activate flow_x86
conda config --env --set subdir osx-64
```

### Verify

```bash
jupyter kernelspec list          # should show flow_r and python3
R -e 'library(flowCore); cat("flowCore OK\n")'
R -e 'library(FlowSOM); cat("FlowSOM OK\n")'
python -c "import flowkit; print('flowkit OK')"
```
