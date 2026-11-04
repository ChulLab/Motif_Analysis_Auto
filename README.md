# Motif Analysis Automation

Snakemake pipeline for high-throughput in vitro Cas13 cleavage motif discovery using random substrate libraries and next-generation sequencing.

## Associated publication

**Protocol for a high-throughput in vitro cleavage assay for Cas13 motif discovery**

Joe K.C. Lam, Hadrien Ng, and S. Chul Kwon

School of Biomedical Sciences, LKS Faculty of Medicine, The University of Hong Kong, Hong Kong, China

- Technical contact: Joe K.C. Lam (joelamkc@connect.hku.hk)
- Lead contact: S. Chul Kwon (chul@hku.hk)

A DOI for the archived software release will be added here after publication.

## Overview

The pipeline starts from raw paired-end sequencing reads and performs adapter trimming, length and quality filtering, read collapsing, dictionary mapping, cleavage-efficiency ranking, and motif visualization. It produces the main and supplementary figures described in the associated protocol, together with a complete table of mapped cleavage fragments.

The workflow supports one or two biological replicates. The example configuration uses a small preprocessed subset of the published dataset; the full configuration reproduces the complete analysis.

## Repository contents

- `Snakefile`: Snakemake workflow for read preprocessing, dictionary mapping, figure generation, and kpLogo execution.
- `generate_plots.py`: downstream mapping, cleavage-efficiency calculation, motif analysis, and figure generation.
- `example_config/`: configuration and sample sheet for the bundled example dataset.
- `full_config/`: configuration and sample sheet for the full GEO dataset.
- `my_config/`: template configuration and sample sheet for user-provided data.
- `intermediate_files/preprocessed_example_sequences.zip`: preprocessed example reads used by the example configuration.
- `environment.yaml`: Conda environment for kpLogo and command-line sequence-processing tools.
- `pyproject.toml`, `uv.lock`: Python dependencies and locked versions for the Snakemake workflow.
- `docs/USAGE.md`: detailed configuration and parameter reference.

## Previous repository

This repository supersedes the earlier fork at https://github.com/ChulLab/Motif_Analysis_Automation, which is retained as an archive.

## Requirements

- Linux x86-64. The kpLogo Conda package is distributed for linux-64 on the `genomedk` channel.
- Conda or Miniconda.
- uv.
- Approximately 100 GB of free disk space for the full dataset and generated outputs.
- Internet access to download the full dataset from GEO/ENA.

## Installation

```bash
git clone https://github.com/ChulLab/Motif_Analysis_Auto.git
cd Motif_Analysis_Auto

conda env create -f environment.yaml --solver=libmamba
conda activate motif_analysis_env
uv sync
```

The Conda environment must be active whenever the pipeline is run so that `cutadapt`, `fastx_toolkit`, `seqkit`, and `kpLogo` are available on `PATH`.

## Data

The repository includes a preprocessed example dataset:

```bash
unzip intermediate_files/preprocessed_example_sequences.zip -d intermediate_files/
```

The full sequencing data are deposited in the NCBI Gene Expression Omnibus under accession **GSE313507**. The `fetch_raw.sh` script downloads the 22 required R1 FASTQ files from ENA, with an `sracha` fallback:

```bash
chmod +x ./fetch_raw.sh
./fetch_raw.sh full_config/samples.csv input
```

Raw sequencing data are not redistributed in this repository.

## Quick start

Run the bundled example:

```bash
uv run snakemake --configfile ./example_config/example_config.yaml --cores 8
```

Run the full analysis after downloading the data:

```bash
uv run snakemake --configfile ./full_config/full_config.yaml --cores 8
```

Run the pipeline with user-provided data after editing `my_config/my_config.yaml` and `my_config/my_samples.csv`:

```bash
uv run snakemake --configfile ./my_config/my_config.yaml --cores 8
```

Adjust `--cores` to the number of available CPUs. The example dataset starts from preprocessed `.collapsed.tsv` files, so adapter trimming, quality filtering, collapsing, and `seqkit` conversion are not exercised by the example run. Use the full dataset or raw FASTQ files to test the preprocessing steps.

## Outputs

The pipeline writes the following to `output/`:

- `main_fig/`: main-text motif figures and the Mann-Whitney test table.
- `supp_fig/`: supplementary figures.
- `motif_plogo/`: pLogo PDFs for each cleavage/no-protein sample pair.
- `mapped_clv_seq_full.xlsx`: one sheet per sample, containing every cleavage fragment mapped to the dictionary, its count, dictionary match, cleavage site, 2-nt/3-nt/8-nt motifs, relative cleavage efficiency, and rank.

Intermediate files are written to `intermediate_files/`, including preprocessed reads, kpLogo input sequences, and the full kpLogo output.

## Configuration

Detailed parameter descriptions are provided in [`docs/USAGE.md`](docs/USAGE.md). Three configuration templates are included:

- `example_config/example_config.yaml` for the bundled example.
- `full_config/full_config.yaml` for the full GEO dataset.
- `my_config/my_config.yaml` for user-provided data.

The `run_kplogo` option controls whether kpLogo is executed. Set `run_kplogo: false` to skip kpLogo and generate logos manually with the pLogo web server; the remaining figures and tables are still produced.

## Reproducibility

Python package versions are pinned in `uv.lock`. The Conda environment recipe is provided in `environment.yaml`; for long-term reproducibility, pin the Conda package versions or export a lock file for the exact environment used in the publication. The full dataset is available from GEO under accession GSE313507.

## Citation

If you use this software, please cite the associated protocol and the archived software release. Citation metadata are provided in `CITATION.cff`.

Lam JKC, Ng H, Kwon SC. Protocol for a high-throughput in vitro cleavage assay for Cas13 motif discovery. 2026.

The archived release DOI will be added to `CITATION.cff` and this README after publication.

## License

This project is released under the MIT License. See `LICENSE`.

## Code availability

The source code is available at https://github.com/ChulLab/Motif_Analysis_Auto. The version used for the publication is tagged `v1.0.0`.

## Data availability

The full sequencing data are available from the NCBI Gene Expression Omnibus under accession GSE313507. The preprocessed example dataset is included in this repository.

## Contact

- Technical contact: Joe K.C. Lam (joelamkc@connect.hku.hk)
- Lead contact: S. Chul Kwon (chul@hku.hk)
