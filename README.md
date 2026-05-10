# flow

Flow cytometry analysis notebooks in R (Bioconductor) and Python.

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

## Setup

### Option A: Server (system/user-wide conda environment)

This sets up a shared conda environment accessible to all users on the server.
Run these as the admin account.

#### 1. Install miniforge (if not already present)

```bash
wget https://github.com/conda-forge/miniforge/releases/latest/download/Miniforge3-Linux-x86_64.sh
bash Miniforge3-Linux-x86_64.sh -b -p /opt/miniforge3
echo 'export PATH="/opt/miniforge3/bin:$PATH"' >> /etc/profile.d/conda.sh
source /etc/profile.d/conda.sh
conda init
```

#### 2. Create the shared environment

```bash
conda create -n flow -c conda-forge -c bioconda \
    r-base=4.4 \
    r-irkernel \
    jupyter \
    jupyterlab \
    r-ggplot2 \
    r-dplyr \
    r-tidyr \
    r-readr \
    r-pheatmap \
    r-viridis \
    r-patchwork \
    r-uwot \
    r-rtsne \
    bioconductor-flowcore \
    bioconductor-flowworkspace \
    bioconductor-ggcyto \
    bioconductor-opencyto \
    bioconductor-flowsom \
    bioconductor-catalyst \
    python=3.11 \
    numpy \
    pandas \
    matplotlib \
    seaborn \
    scikit-learn \
    umap-learn \
    -y
```

#### 3. Install Python flow packages (not on conda-forge)

```bash
conda activate flow
pip install flowkit fcsparser leidenalg
```

#### 4. Register the R kernel for Jupyter

```bash
conda activate flow
R -e 'IRkernel::installspec(name = "flow_r", displayname = "R (flow)", user = FALSE)'
```

`user = FALSE` installs the kernel system-wide so all users see it in JupyterLab.

#### 5. Make the environment accessible to all users

```bash
# Ensure the environment is readable by everyone
chmod -R a+rX /opt/miniforge3/envs/flow

# Users activate with:
conda activate flow

# Or launch JupyterLab directly:
conda run -n flow jupyter lab --ip=0.0.0.0 --port=8888 --no-browser
```

#### 6. Verify

```bash
conda activate flow
jupyter kernelspec list
# Should show: flow_r and python3

R -e 'library(flowCore); cat("flowCore OK\n")'
R -e 'library(FlowSOM); cat("FlowSOM OK\n")'
python -c "import flowkit; print('flowkit OK')"
```

---

### Option B: Local machine (macOS / Linux / Windows)

#### 1. Install miniforge (recommended over Anaconda)

macOS (Apple Silicon M1/M2/M3):
```bash
brew install miniforge
conda init zsh
```

Linux:
```bash
wget https://github.com/conda-forge/miniforge/releases/latest/download/Miniforge3-Linux-x86_64.sh
bash Miniforge3-Linux-x86_64.sh
```

#### 2. Create the environment

```bash
conda create -n flow -c conda-forge -c bioconda \
    r-base=4.4 \
    r-irkernel \
    jupyter \
    jupyterlab \
    r-ggplot2 \
    r-dplyr \
    r-tidyr \
    r-readr \
    r-pheatmap \
    r-viridis \
    r-patchwork \
    r-uwot \
    r-rtsne \
    python=3.11 \
    numpy \
    pandas \
    matplotlib \
    seaborn \
    scikit-learn \
    umap-learn \
    -y
```

#### 3. Install Bioconductor packages

On Apple Silicon Macs, some Bioconductor packages don't have pre-built
conda binaries for `osx-arm64` and need to be compiled from source.
Install them from within R:

```bash
conda activate flow
```

```r
# Run in R (type `R` in terminal)
if (!requireNamespace("BiocManager", quietly = TRUE))
    install.packages("BiocManager", repos = "https://cloud.r-project.org")

BiocManager::install(c(
    "flowCore", "flowWorkspace", "openCyto",
    "FlowSOM", "CATALYST", "ggcyto"
))
```

#### 4. Install Python flow packages & register R kernel

```bash
pip install flowkit fcsparser leidenalg
R -e 'IRkernel::installspec(name = "flow_r", displayname = "R (flow)", user = TRUE)'
```

#### 5. Launch

```bash
conda activate flow
jupyter lab
```

---

### Apple Silicon (M1/M2/M3) troubleshooting

Bioconductor packages that compile C/C++/Fortran code can fail on ARM64 Macs.

**Missing compilers:**
```bash
xcode-select --install

# Install compilers via conda
conda install -n flow -c conda-forge \
    gfortran_osx-arm64 clang_osx-arm64 clangxx_osx-arm64
```

**flowWorkspace / RProtoBufLib build failures:**

These depend on protobuf and HDF5. Install system libraries first:
```bash
conda install -n flow -c conda-forge protobuf hdf5
```

If it still fails:
```bash
conda activate flow
export PKG_CONFIG_PATH="$CONDA_PREFIX/lib/pkgconfig:$PKG_CONFIG_PATH"
export LDFLAGS="-L$CONDA_PREFIX/lib"
export CPPFLAGS="-I$CONDA_PREFIX/include"

R -e 'BiocManager::install("flowWorkspace")'
```

**Nuclear option — x86 emulation via Rosetta:**
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

This runs under Rosetta 2 translation — slower, but everything compiles.

---

### Environment file (alternative)

Create the environment from the included `environment.yml`:

```bash
conda env create -f environment.yml
conda activate flow
```

Then install Bioconductor from R (see step 3 above) and pip packages:

```bash
pip install flowkit fcsparser leidenalg
R -e 'IRkernel::installspec(name = "flow_r", displayname = "R (flow)", user = TRUE)'
```
