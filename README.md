# hic-satpathy-lab

[![Nextflow](https://img.shields.io/badge/nextflow%20DSL2-%E2%89%A521.10.3-23aa62.svg?labelColor=000000)](https://www.nextflow.io/)

[![run with singularity](https://img.shields.io/badge/run%20with-singularity-1d355c.svg?labelColor=000000)](https://sylabs.io/docs/)

A Nextflow-based Hi-C data analysis pipeline customized for the Satpathy lab. This pipeline processes chromosome conformation capture (Hi-C) sequencing data from raw reads through to downstream analysis including loop calling, TAD detection, and compartment identification.

> **Note:** This pipeline is derived from [nf-core/hic](https://github.com/nf-core/hic) but has been significantly modified and enhanced for Satpathy lab workflows.

## Overview

**hic-satpathy-lab** provides a comprehensive end-to-end solution for Hi-C data analysis. The pipeline handles read quality control, mapping, contact map generation, normalization, and downstream analysis including chromatin loop detection, TAD calling, and compartment analysis.

### Key Features

- **Multiple loop calling methods**: Supports both MUSTACHE and cooltools dots for chromatin loop detection
- **Juicebox integration**: Generates `.hic` files for interactive visualization in Juicebox
- **Flexible analysis options**: Run loop callers independently or simultaneously
- **Containerized execution**: Uses Docker/Singularity for reproducible analysis
- **Comprehensive QC**: Integrated quality control metrics and reports

## Pipeline Workflow

The pipeline performs the following steps:

1. **Read Quality Control** - FastQC analysis of input sequencing reads
2. **Hi-C Processing** - HiC-Pro-based processing including:
   - Two-step mapping strategy with bowtie2 to rescue reads spanning ligation sites
   - Valid interaction detection
   - Duplicate removal
   - Raw and normalized contact map generation
3. **Contact Map Generation** - Create genome-wide contact maps at multiple resolutions using cooler
4. **Normalization** - Contact map balancing using cooler's balancing algorithm
5. **Format Export** - Export to multiple formats (HiC-Pro, cooler, Juicebox)
6. **Quality Controls** - Comprehensive QC metrics from HiC-Pro and HiCExplorer
7. **Compartments** - A/B compartment calling using cooltools
8. **TADs** - Topologically Associating Domain detection using HiCExplorer and cooltools
9. **Loop Calling** - Chromatin loop detection using:
   - [MUSTACHE](https://github.com/ay-lab/mustache) - Multi-scale loop detection
   - [cooltools dots](https://cooltools.readthedocs.io/en/latest/notebooks/dots.html) - FDR-based statistical loop detection
10. **Report Generation** - MultiQC report summarizing all analysis results

## Installation

### Prerequisites

- [Nextflow](https://www.nextflow.io/docs/latest/getstarted.html#installation) (>=21.10.3)
- One of the following container engines:
  - [Docker](https://docs.docker.com/engine/installation/)
  - [Singularity](https://www.sylabs.io/guides/3.0/user-guide/)
  - [Podman](https://podman.io/)
  - [Conda](https://conda.io/miniconda.html)

### Getting Started

1. **Test the pipeline** with the included test dataset:

   ```bash
   nextflow run hic-satpathy-lab -profile test,docker --outdir results
   ```

2. **Run your own analysis**:

   ```bash
   nextflow run hic-satpathy-lab \
     --input samplesheet.csv \
     --outdir results \
     --genome GRCh37 \
     -profile docker
   ```

### Configuration Profiles

The pipeline supports several execution profiles:

- `docker` - Use Docker containers (recommended for local execution)
- `singularity` - Use Singularity containers (recommended for HPC clusters)
- `conda` - Use Conda environments
- `podman` - Use Podman containers

You can specify multiple profiles separated by commas, e.g., `-profile test,docker`.

## Usage

### Basic Usage

```bash
nextflow run hic-satpathy-lab \
  --input samplesheet.csv \
  --outdir results \
  --genome GRCh37 \
  -profile docker
```

### Loop Calling Options

The pipeline supports multiple loop calling methods. Configure which methods to use:

```bash
# Use both MUSTACHE and cooltools
--loops_caller mustache,cooltools

# Use only MUSTACHE
--loops_caller mustache

# Use only cooltools
--loops_caller cooltools

# Skip loop calling
--skip_loops
```

### Output Formats

The pipeline generates outputs in multiple formats:

- **Cooler format** (`.cool`, `.mcool`) - For downstream analysis
- **Juicebox format** (`.hic`) - For interactive visualization
- **HiC-Pro format** - Text-based contact maps

## Output Structure

Results are organized in the output directory as follows:

```
results/
├── contact_maps/          # Contact maps in various formats
│   ├── cooler/           # Cooler format files
│   └── juicebox/         # Juicebox .hic files
├── loops/                # Loop calling results
│   ├── mustache/         # MUSTACHE results
│   └── cooltools/        # cooltools dots results
├── tads/                 # TAD calling results
├── compartments/         # Compartment analysis results
└── multiqc/              # MultiQC report
```

## Credits

This pipeline is based on **nf-core/hic** (originally written by Nicolas Servant) and has been customized for the Satpathy lab with the following enhancements:

- Multiple loop calling methods (MUSTACHE and cooltools dots)
- Juicebox output generation for interactive visualization
- Additional customizations for Satpathy lab workflows

**Maintainer:** This version is maintained by **Andy Chen**.

## Citations

If you use this pipeline in your research, please cite:

- The original nf-core/hic pipeline: [10.5281/zenodo.2669513](https://doi.org/10.5281/zenodo.2669513)
- Individual tools used in the pipeline (see [`CITATIONS.md`](CITATIONS.md) for a complete list)

## Support

For questions or issues specific to this Satpathy lab version, please contact the maintainer. For general Nextflow or pipeline questions, refer to the [Nextflow documentation](https://www.nextflow.io/docs/latest/getstarted.html).

## License

This pipeline uses code and infrastructure from nf-core/hic, which is licensed under the MIT License.
