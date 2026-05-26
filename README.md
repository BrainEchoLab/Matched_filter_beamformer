# Matched Filter Beamformer

Reference implementation of the spatial matched-filter beamformer for
microbeamforming probes, accompanying the paper:

> Verhoef L., Kruizinga P., Voorneveld J. **Four-dimensional ultrasound imaging
> with microbeamforming arrays.** submitted **.

The single user-facing entry point is the Jupyter notebook
[`matched_filter_ubf.ipynb`](matched_filter_ubf.ipynb). It reconstructs 3D
ultrasound image volumes from preprocessed microbeamformed acquisitions using
a per-frequency matched-filter formulation, with optional SVD tissue
suppression and ParaView (`.vts`/`.pvd`) export.

Example acquisitions (spinning disk phantom, preclinical pig liver, clinical
human brain) are published on Zenodo at
[10.5281/zenodo.20158128](https://doi.org/10.5281/zenodo.20158128). 
You need to download and unzip these files into the following structure 
(from the repository root directory):
```
data/
├── SpinningDisk/
│   ├── widebeam_<speed_mm/s>.h5
│   └── encoded_<speed_mm/s>.h5
├── PigLiver/
│   ├── widebeam.h5
│   └── encoded.h5
└── HumanBrain/
    ├── widebeam.h5
    └── encoded.h5
```

## Quick start

### 1. Requirements

* **NVIDIA GPU + CUDA 12 or 13.** The matched filter is implemented entirely on
  the GPU with [CuPy](https://cupy.dev/); CPU execution is not supported at
  the resolutions used in the paper.
* **Python ≥ 3.12** with `pip`.
* ~10 GB of free disk space if you download all example datasets.

We tested with CUDA 12.x, CuPy 13, Python 3.12, and an NVIDIA RTX A6000 (48 GB)
on Linux.

### 2. Install

Clone the repository and create an environment.

```bash
# navigate to the parent directory you want to clone the repository to
git clone https://github.com/BrainEchoLab/Microbeamforming_Matched_Filter.git
cd Microbeamforming_Matched_Filter
# create a virtual environment using either venv or conda
# venv
python -m venv .venv
source .venv/bin/activate     # Windows: .venv\Scripts\activate
# OR conda
conda create --name matched_filter python=3.12
conda activate matched_filter
```

Install the package requirements
```bash
pip install -r requirements.txt
```

Install the package and the matching CuPy build. 
See https://docs.cupy.dev/en/stable/install.html for more details on how to do this.
Below is an example on how to install CuPy if you already have the Cuda Toolkit installed:

```bash
# check what version of CUDA you have
nvcc --version

# if you have CUDA 12.x:
pip install cupy-cuda12x

# if you have CUDA 13.x:
pip install cupy-cuda13x
```

### 3. Run

```bash
jupyter lab matched_filter_ubf.ipynb
```

Then **Run All**. The notebook will:

1. Check that data has been downloaded from Zenodo and unzipped into the correct structure in `./data/`.
2. Load the channel IQ data and acquisition geometry.
3. Apply optional SVD tissue suppression and the matched filter beamformer
   on the GPU, saving the beamformed volumes in HDF5 format.
4. Display an interactive viewer with maximum intensity projections.
5. (optionally) Save volumes as `.vts`/`.pvd` for easy visualization in ParaView.
6. (optionally) Visualize the transmit and receive fields for a few transmit events.

Switch between datasets by editing the parameter cell:

```python
dataset       = "SpinningDisk"   # "PigLiver", "HumanBrain", or "SpinningDisk"
sequence_type = "widebeam"       # "widebeam" or "encoded"
speed         = "1000"           # spinning-disk only — tangential speed in mm/s
```

## Repository layout

```
.
├── matched_filter_ubf.ipynb     ← user-facing notebook (start here)
├── pyproject.toml               ← installable package definition
├── requirements.txt             ← bare dependency list
├── README.md                    ← this document
├── LICENSE                      ← GPL-3.0
├── data/                        ← created on first run; not tracked in git
├── results/                     ← created on first run; not tracked in git
└── preprocessing/               ← scripts used to build the Zenodo data;
    ├── README.md                  not needed for reproducing the paper
    ├── load_rf_iqmodulate_microbeamform_save_preprocessed.m
    └── preprocess_rfdata.ipynb
```

The contents of `preprocessing/` are included for transparency only — you do
**not** need to run them. See [`preprocessing/README.md`](preprocessing/README.md)
for the raw → preprocessed pipeline.

## Citing

If you use this code, please cite both the paper and the dataset:

```bibtex
@article{verhoef2026microbeamforming,
  author  = {Verhoef, Luuk and Kruizinga, Pieter and Voorneveld, Jason},
  title   = {Four-dimensional ultrasound imaging with microbeamforming arrays},
  journal = {IEEE Transactions on Ultrasonics, Ferroelectrics, and Frequency Control},
  year    = {2026}
}

@dataset{verhoef2026microbeamforming_data,
  author    = {Verhoef, Luuk and Kruizinga, Pieter and Voorneveld, Jason},
  title     = {Example acquisitions for: Four-dimensional ultrasound imaging with microbeamforming arrays},
  year      = {2026},
  publisher = {Zenodo},
  doi       = {10.5281/zenodo.20158128}
}
```

## License

GPL-3.0 — see [`LICENSE`](LICENSE).

## Contact

Jason Voorneveld — *j.voorneveld@erasmusmc.nl* — BrainEcho Lab, Department of
Neuroscience, Erasmus MC, Rotterdam, the Netherlands.
