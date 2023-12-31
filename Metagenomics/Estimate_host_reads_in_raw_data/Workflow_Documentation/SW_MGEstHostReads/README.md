# SW_MGEstHostReads Workflow Information and Usage Instructions


## General workflow info
The current pipeline for how GeneLab estimates host DNA in Illumina metagenomics sequencing data (MGEstHostReads), [GL-DPPD-7109.md](../../Pipeline_GL-DPPD-7109_Versions/GL-DPPD-7109.md), is implemented as a [Snakemake](https://snakemake.readthedocs.io/en/stable/) workflow and utilizes [conda](https://docs.conda.io/en/latest/) environments to install/run all tools. This workflow (SW_MGEstHostReads) is run using the command line interface (CLI) of any unix-based system. The workflow can be used even if you are unfamiliar with Snakemake and conda, but if you want to learn more about those, [this Snakemake tutorial](https://snakemake.readthedocs.io/en/stable/tutorial/tutorial.html) within [Snakemake's documentation](https://snakemake.readthedocs.io/en/stable/) is a good place to start for that, and an introduction to conda with installation help and links to other resources can be found [here at Happy Belly Bioinformatics](https://astrobiomike.github.io/unix/conda-intro).  

## Utilizing the workflow

1. [Install conda, mamba, and `genelab-utils` package](#1-install-conda-mamba-and-genelab-utils-package)  
2. [Download the workflow template files](#2-download-the-workflow-template-files)  
3. [Modify the variables in the config.yaml file](#3-modify-the-variables-in-the-configyaml-file)  
4. [Run the workflow](#4-run-the-workflow)  

### 1. Install conda, mamba, and `genelab-utils` package
We recommend installing a Miniconda, Python3 version appropriate for your system, as exemplified in [the above link](https://astrobiomike.github.io/unix/conda-intro#getting-and-installing-conda).  

Once conda is installed on your system, we recommend installing [mamba](https://github.com/mamba-org/mamba#mamba), as it generally allows for much faster conda installations:

```bash
conda install -n base -c conda-forge mamba
```

> You can read a quick intro to mamba [here](https://astrobiomike.github.io/unix/conda-intro#bonus-mamba-no-5) if wanted.

Once mamba is installed, you can install the genelab-utils conda package in a new environment with the following command:

```bash
mamba create -n genelab-utils -c conda-forge -c bioconda -c defaults -c astrobiomike 'genelab-utils>=1.1.02'
```

The environment then needs to be activated:

```bash
conda activate genelab-utils
```

### 2. Download the workflow template files
The workflow files required for estimating the proportion of host reads are in the [workflow_code](workflow_code) directory. To get a copy of the latest SW_MGEstHostReads version on to your system, run the following command:

```bash
GL-get-workflow MG-estimate-host-reads
```

This downloaded the workflow into a directory called `SW_MGEstHostReads_*/`, with the workflow version number at the end.

> Note: If wanting an earlier version, the wanted version can be provided as an optional argument like so:
> ```bash
> GL-get-workflow MG-estimate-host-reads --wanted-version 1.0.0
> ```

### 3. Modify the variables in the config.yaml file
Once you've downloaded the workflow template, you can modify the variables in the [config.yaml](workflow_code/config.yaml) file as needed. For example, you will have to provide a text file containing a single-column list of unique sample identifiers (see an example of how to set this up below - if you are running the example dataset, this file is provided in the [workflow_code](workflow_code) directory [here](workflow_code/unique-sample-IDs.txt)). You will also need to indicate the path to your input data (raw reads) and the root directory for where the kraken2 reference database is stored (ones currently available from us can be downloaded from [this page](reference-database-info.md)). Additionally, if necessary, you'll need to modify each variable in the [config.yaml](workflow_code/config.yaml) file. 

> Note: If you are unfamiliar with how to specify paths, one place you can learn more is [here](https://astrobiomike.github.io/unix/getting-started#the-unix-file-system-structure).  

**Example for how to create a single-column list of unique sample identifiers from your raw data file names**

For example, if you have paired-end read data for 2 samples located in `../Raw_Sequence_Data/` relative to your workflow directory, that would look like this:

```bash
ls ../Raw_Sequence_Data/
```

```
Sample-1_R1_raw.fastq.gz
Sample-1_R2_raw.fastq.gz
Sample-2_R1_raw.fastq.gz
Sample-2_R2_raw.fastq.gz
```

You would set up your `unique-sample-IDs.txt` file as follows:

```bash
cat unique-sample-IDs.txt
```

```
Sample-1
Sample-2
```

### 4. Run the workflow

While in the directory holding the Snakefile, config.yaml, and other workflow files that you downloaded in [step 2](#2-download-the-workflow-template-files), here is one example command of how to run the workflow:

```bash
snakemake --use-conda --conda-prefix ${CONDA_PREFIX}/envs -j 2 -p
```

* `--use-conda` – specifies to use the conda environments included in the workflow (these are specified in the [envs/](workflow_code/envs) directory)
* `--conda-prefix` – indicates where the needed conda environments will be stored. Adding this option will also allow the same conda environments to be re-used when processing additional datasets, rather than making new environments each time you run the workflow. The value listed for this option, `${CONDA_PREFIX}/envs`, points to the default location for conda environments (note: the variable `${CONDA_PREFIX}` will be expanded to the appropriate location on whichever system it is run on).
* `-j` – assigns the number of jobs Snakemake should run concurrently (keep in mind that many of the thread and cpu parameters set in the config.yaml file will be multiplied by this)
* `-p` – specifies to print out each command being run to the screen

See `snakemake -h` and [Snakemake's documentation](https://snakemake.readthedocs.io/en/stable/) for more options and details.

A quick example can be run with the files included in the [workflow_code](workflow_code) directory after specifying a location for the reference database in the [config.yaml](workflow_code/config.yaml) file.

---

## Reference database info
Reference database information is available on the [reference database info page](reference-database-info.md).

---
