[![Gitpod ready-to-code](https://img.shields.io/badge/Gitpod-ready--to--code-908a85?logo=gitpod)](https://gitpod.io/#https://github.com/StaPH-B/docker-builds)

# [docker-builds](#)
This repository contains the Dockerfiles and other assorted files necessary for building Docker images for a variety of programs used by members of the StaPH-B (State Public Health Lab Bioinformatics) consortium. The purpose of this repository is to provide a centralized location for Docker images that is easily accessible for users, with clear documentation on how the containers were built and how to use them.

There are several community projects that create and share containers for bioinformatic tools. This repository contains the Dockerfiles and other assorted files necessary for building Docker images for a variety of tools used by members of the StaPH-B (State Public Health Lab Bioinformatics) consortium. The purpose of this repository is to provide a centralized location for Docker images that is easily accessible for users, with clear documentation on how the containers were built and how to use them.

This is a community resource, built and maintined by users from varied backgrounds and expertise levels. As such, we have provided some [templates for contributing to this repository](./dockerfile-template). If **you** would like to add a Docker image or improve upon the existing images, please fork the repository, make your changes/additions, and submit a pull request. If you are having an issue with an existing image, please submit an issue. We welcome any and all feedback!

[See more details on how to contribute here](https://staph-b.github.io/docker-builds/contribute/)

## Docker image repositories & hosting

We host all of our docker images on two different repositories:

  1. [Dockerhub - https://hub.docker.com/r/staphb/](https://hub.docker.com/r/staphb/)
  2. [Quay.io - https://quay.io/organization/staphb/](https://quay.io/organization/staphb/)

The development process of creating a new image is summarized as follows:

```mermaid
graph TD
     A[fork staphb/docker-builds repo]-->B[create tool/version directory]
     B-->C[create readme]
     B-->D[create dockerfile]
     D-->G[create app and test layers]
     A-->E[add License to Program_Licenses.md]
     A-->F[add tool to list in this readme]
     E-->H[submit PR]
     F-->H
     G-->H
     C-->H
     H-->I{build to test}
     I--success-->J[PR merged and docker image pushed to dockerhub and quay]
     I--failure-->K[edit dockerfile]
     K-->I
```

## User Guide

The [StaPH-B Docker User Guide](https://staphb.org/docker-builds/) was created to outline methods and best practices for using and developing docker containers. There are chapters for:

- [Contributing](https://staphb.org/docker-builds/contribute/)
- [Downloading Docker images](https://staphb.org/docker-builds/get_containers/)
- [Running Docker containers](https://staphb.org/docker-builds/run_containers/)
- [Developing Docker images](https://staphb.org/docker-builds/make_containers/)
- [Useful links](https://staphb.org/docker-builds/useful_links/)

### Summarized usage guide for docker

```bash
# Build a docker image to the 'test' layer
docker build --tag tool:test --target test <directory to Dockerfile>
docker build --tag samtools:test --target test samtools/1.15

# Download a docker image from dockerhub (most tools have a 'latest' version tag)
docker pull staphb/tool:version
docker pull staphb/shigatyper:2.0.2

# Run the container (don't forget to mount your volumes!)
docker run --rm -u $(id -u):$(id -g) -v <local directory>:/data tool:version <command>
docker run --rm -u $(id -u):$(id -g) -v $(pwd)/amrfinder_test_files:/data amrfinder:3.10 amrfinder --nucleotide 2021CK-01854_contigs.fa --threads 20 --name 2021CK-01854 --output /data/2021CK-01854.txt --organism Klebsiella
```

Further documentation can be found at [docs.docker.com](https://docs.docker.com/engine/reference/run/)

### Templates

Several template files are provided. These are intended to be copied and edited by contributors.

1. [dockerfile-template/Dockerfile](./dockerfile-template/Dockerfile) is the basic template useful for most images
2. [dockerfile-template/Dockerfile_mamba](./dockerfile-template/Dockerfile_mamba) is a basic template for using the micromamba base image
3. [dockerfile-template/README.md](./dockerfile-template/README.md) is a basic readme file template to assist others in using the image

### What about Singularity?

For many people, Docker is not an option, but Singularity is. Most Docker containers are compatible with Singularity and can easily be converted to Singularity format. Please see the [User Guide](https://staphb.org/docker-builds/) for instructions on how to download docker images from dockerhub and how to run them using Singularity. We've worked hard to ensure that our containers are compatibile with Singularity, but if you find one that isn't, please leave an issue and let us know!

### Summarized usage guide for singularity

```bash
# Pulling a container from dockerhub (creates a file)
singularity pull --name <name of singularity file> docker://staphb/bbtools:38.96
singularity pull --name staphb-bbtools-38.96.simg docker://staphb/bbtools:38.96

# Running the container (don't forget to mount your volumes!)
singularity exec --bind <local directory>:/data <name of singularity file> <command>
singularity exec --bind $(pwd)/fastq:/data staphb-bbtools-38.96.simg bbduk.sh in1=sample1_R1.fastq.gz in2=sample1_R2.fastq.gz out1=bbduk/sample1_rmphix_R1.fastq.gz out2=bbduk/sample1_rmphix_R2.fastq.gz outm=bbduk/sample1.matched_phix.fq ref=/opt/bbmap/resources/phix174_ill.ref.fa.gz stats=bbduk/sample1.phix.stats.txt threads=4
```

Further documentation can be found at [docs.sylabs.io](https://docs.sylabs.io/guides/3.1/user-guide/cli.html)

## Logs

In November 2020, Docker began to implement pull rate limits for images hosted on dockerhub. This limits the number of `docker pull`'s per time period (e.g. anonymous users allowed 100 pulls per six hours). We applied and were approved for Docker's "Open Source Program," which should have removed the pull rate limits for all `staphb` docker images! 🎉 🥳 If you encounter an error such as `ERROR: toomanyrequests: Too Many Requests.` or `You have reached your pull rate limit. You may increase the limit by authenticating and upgrading: https://www.docker.com/increase-rate-limits.` , please let us know by [submitting an issue.](https://github.com/StaPH-B/docker-builds/issues)

**A huge thank you goes to the folks at Docker for supporting our efforts to distribute & share critical tools for public health bioinformatics. This has been especially important during the COVID-19 global pandemic, as many of these tools are used to conduct genomic surveillance on the SARS-CoV-2 virus as well as other important pathogens of public health concern.**

To learn more about the docker pull rate limits and the open source software program, please see these blog posts ([1](https://www.docker.com/blog/docker-hub-image-retention-policy-delayed-and-subscription-updates/), [2](https://www.docker.com/blog/scaling-docker-to-serve-millions-more-developers-network-egress/), and [3](https://www.docker.com/blog/expanded-support-for-open-source-software-projects/)) and Docker documentation ([1](https://docs.docker.com/docker-hub/download-rate-limit/)).

*NOTE: In the table below, we do not provide individual links to the various tools on quay.io, please visit the above quay.io link to find all of our docker images.*

## [Available Docker images](https://hub.docker.com/r/staphb/)

| Software | Version | Link |
| :--------: | ------- | -------- |
| [ABRicate](https://hub.docker.com/r/staphb/abricate/) <br/> [![docker pulls](https://badgen.net/docker/pulls/staphb/abricate)](https://hub.docker.com/r/staphb/abricate) | <ul><li>0.8.7</li><li>0.8.13</li><li>0.8.13s (+serotypefinder db)</li><li>0.9.8</li><li>1.0.0</li><li>[1.0.1 (+ A. baumannii plasmid typing db)](https://github.com/StaPH-B/docker-builds/blob/master/abricate/1.0.1-Abaum-plasmid)</li><li>[1.0.1 (+ InsaFlu db)](https://github.com/StaPH-B/docker-builds/blob/master/abricate/1.0.1-insaflu-220727)</li></ul> | https://github.com/tseemann/abricate |
| [any2fasta](https://hub.docker.com/r/staphb/any2fasta/) <br/> [![docker pulls](https://badgen.net/docker/pulls/staphb/any2fasta)](https://hub.docker.com/r/staphb/any2fasta) | <ul><li>0.4.2</li></ul> | https://github.com/tseemann/any2fasta |
| [ARIBA](https://hub.docker.com/r/staphb/ariba/) <br/> [![docker pulls](https://badgen.net/docker/pulls/staphb/ariba)](https://hub.docker.com/r/staphb/ariba) | <ul><li>2.14.4</li><li>[2.14.6](ariba/2.14.6/)</li></ul> | https://github.com/sanger-pathogens/ariba |
| [artic](https://hub.docker.com/r/staphb/artic-ncov2019) <br/> [![docker pulls](https://badgen.net/docker/pulls/staphb/artic)](https://hub.docker.com/r/staphb/artic) | <ul><li>[1.2.4-1.11.1 (artic-medaka)](artic/1.2.4-1.11.1/)</ul> | https://github.com/artic-network/fieldbioinformatics |
| [artic-ncov2019](https://hub.docker.com/r/staphb/artic-ncov2019) <br/> [![docker pulls](https://badgen.net/docker/pulls/staphb/artic-ncov2019)](https://hub.docker.com/r/staphb/artic-ncov2019) | <ul><li>1.3.0</ul> | https://github.com/artic-network/fieldbioinformatics |
| [artic-ncov2019-epi2me](https://hub.docker.com/r/staphb/artic-ncov2019-epi2me) <br/> [![docker pulls](https://badgen.net/docker/pulls/staphb/artic-ncov2019-epi2me)](https://hub.docker.com/r/staphb/artic-ncov2019-epi2me) | <ul><li>0.3.10</ul> | https://github.com/epi2me-labs/wf-artic |
| [artic-ncov2019-medaka](https://hub.docker.com/r/staphb/artic-ncov2019-medaka) <br/> [![docker pulls](https://badgen.net/docker/pulls/staphb/artic-ncov2019-medaka)](https://hub.docker.com/r/staphb/artic-ncov2019-medaka) | <ul><li>1.1.0</ul> | https://github.com/artic-network/artic-ncov2019 |
| [artic-ncov2019-nanopolish](https://hub.docker.com/r/staphb/artic-ncov2019-nanopolish) <br/> [![docker pulls](https://badgen.net/docker/pulls/staphb/artic-ncov2019-nanopolish)](https://hub.docker.com/r/staphb/artic-ncov2019-nanopolish) | <ul><li>1.1.0</ul> | https://github.com/artic-network/artic-ncov2019 |
| [Augur](https://github.com/nextstrain/augur) <br/> [![docker pulls](https://badgen.net/docker/pulls/staphb/augur)](https://hub.docker.com/r/staphb/augur) | <ul><li>6.3.0</li><li>7.0.2</li><li>8.0.0</li><li>9.0.0</li><li>16.0.3</li></ul> | https://github.com/nextstrain/augur |
| [Auspice](https://github.com/nextstrain/auspice) <br/> [![docker pulls](https://badgen.net/docker/pulls/staphb/auspice)](https://hub.docker.com/r/staphb/auspice) | <ul><li>2.12.0</li></ul> | https://github.com/nextstrain/auspice |
| [BBTools](https://hub.docker.com/r/staphb/bbtools/) <br/> [![docker pulls](https://badgen.net/docker/pulls/staphb/bbtools)](https://hub.docker.com/r/staphb/bbtools) | <ul><li>38.76</li><li>38.86</li><li>38.95</li><li>38.96</li><li>38.97</li><li>38.98</li><li>38.99</li><li>39.00</li><li>39.01</li></ul> | https://jgi.doe.gov/data-and-tools/bbtools/ |
| [bcftools](https://hub.docker.com/r/staphb/bcftools/) <br/> [![docker pulls](https://badgen.net/docker/pulls/staphb/bcftools)](https://hub.docker.com/r/staphb/bcftools) | <ul><li>1.10.2</li><li>1.11</li><li>1.12</li><li>1.13</li><li>1.14</li><li>1.15</li><li>1.16</li><li>1.17</li><li>[1.18](bcftools/1.18/)</li></ul> | https://github.com/samtools/bcftools |
| [bedtools](https://hub.docker.com/r/staphb/bedtools/) <br/> [![docker pulls](https://badgen.net/docker/pulls/staphb/bedtools)](https://hub.docker.com/r/staphb/bedtools) | <ul><li>2.29.2</li><li>2.30.0</li><li>[2.31.0](bedtools/2.31.0/)</li></ul> | https://bedtools.readthedocs.io/en/latest/ <br/>https://github.com/arq5x/bedtools2 |
| [berrywood-report-env](https://hub.docker.com/r/staphb/berrywood-report-env/) <br/> [![docker pulls](https://badgen.net/docker/pulls/staphb/berrywood-report-env)](https://hub.docker.com/r/staphb/berrywood-report-env) | <ul><li>1.0</li></ul> | none |
| [blast+](https://hub.docker.com/r/staphb/blast/) <br/> [![docker pulls](https://badgen.net/docker/pulls/staphb/blast)](https://hub.docker.com/r/staphb/blast) | <ul><li>2.13.0</li><li>[2.14.0](blast/2.14.0/)</li><li>[2.14.1](blast/2.14.1/)</li></ul> | https://www.ncbi.nlm.nih.gov/books/NBK279690/ |
| [bowtie2](https://hub.docker.com/r/staphb/bowtie2/) <br/> [![docker pulls](https://badgen.net/docker/pulls/staphb/bowtie2)](https://hub.docker.com/r/staphb/bowtie2) | <ul><li>2.4.4</li><li>2.4.5</li><li>2.5.1</li></ul> | http://bowtie-bio.sourceforge.net/bowtie2/manual.shtml <br/>https://github.com/BenLangmead/bowtie2 |
| [Bracken](https://hub.docker.com/r/staphb/bracken/) <br/> [![docker pulls](https://badgen.net/docker/pulls/staphb/bracken)](https://hub.docker.com/r/staphb/bracken) | <ul><li>[2.9](./bracken/2.9)</li></ul> | https://ccb.jhu.edu/software/bracken/index.shtml?t=manual <br/>https://github.com/jenniferlu717/Bracken |
| [BUSCO](https://hub.docker.com/r/staphb/busco/) <br/> [![docker pulls](https://badgen.net/docker/pulls/staphb/busco)](https://hub.docker.com/r/staphb/busco) | <ul><li>5.4.7</li></ul> | https://busco.ezlab.org/busco_userguide.html <br/>https://gitlab.com/ezlab/busco |
| [BWA](https://hub.docker.com/r/staphb/bwa) <br/> [![docker pulls](https://badgen.net/docker/pulls/staphb/bwa)](https://hub.docker.com/r/staphb/bwa) | <ul><li>0.7.17</li></ul> | https://github.com/lh3/bwa |
| [Canu](https://hub.docker.com/r/staphb/canu) <br/> [![docker pulls](https://badgen.net/docker/pulls/staphb/canu?)](https://hub.docker.com/r/staphb/canu)| <ul><li>2.0</li><li>2.1.1</li><li>2.2</li></ul> | https://canu.readthedocs.io/en/latest/ <BR/> https://github.com/marbl/canu |
| [Canu-Racon](https://hub.docker.com/r/staphb/canu-racon/) <br/> [![docker pulls](https://badgen.net/docker/pulls/staphb/canu-racon)](https://hub.docker.com/r/staphb/canu-racon) | <ul><li>1.7.1 (Canu), 1.3.1 (Racon), 2.13 (minimap2)</li><li>1.9 (Canu), 1.4.3 (Racon), 2.17 (minimap2)</li><li>1.9i (Canu), 1.4.3 (Racon), 2.17 (minimap2), (+racon_preprocess.py)</li><li>2.0 (Canu), 1.4.3 (Racon), 2.17 (minimap2)</li></ul> | https://canu.readthedocs.io/en/latest/ <br/> https://github.com/lbcb-sci/racon <br/> https://github.com/isovic/racon (ARCHIVED) <br/> https://lh3.github.io/minimap2/ |
| [centroid](https://hub.docker.com/r/staphb/centroid/) <br/> [![docker pulls](https://badgen.net/docker/pulls/staphb/centroid)](https://hub.docker.com/r/staphb/centroid) | <ul><li>1.0.0</li></ul> | https://github.com/stjacqrm/centroid |
| [CDC-SPN](https://hub.docker.com/r/staphb/cdc-spn/) <br/> [![docker pulls](https://badgen.net/docker/pulls/staphb/cdc-spn)](https://hub.docker.com/r/staphb/cdc-spn) | <ul><li>0.1 (no version)</li></ul> | https://github.com/BenJamesMetcalf/Spn_Scripts_Reference |
| [cfsan-snp-pipeline](https://hub.docker.com/r/staphb/cfsan-snp-pipeline) <br/> [![docker pulls](https://badgen.net/docker/pulls/staphb/cfsan-snp-pipeline)](https://hub.docker.com/r/staphb/cfsan-snp-pipeline) | <ul><li>2.0.2</li> <li>2.2.1</li> </ul> | https://github.com/CFSAN-Biostatistics/snp-pipeline |
| [CheckM](https://hub.docker.com/r/staphb/checkm) <br/> [![docker pulls](https://badgen.net/docker/pulls/staphb/checkm)](https://hub.docker.com/r/staphb/checkm) | <ul><li>[1.2.2](./checkm/1.2.2)</li></ul> | https://github.com/Ecogenomics/CheckM |
| [Circlator](https://hub.docker.com/r/staphb/circlator) <br/> [![docker pulls](https://badgen.net/docker/pulls/staphb/circlator)](https://hub.docker.com/r/staphb/circlator) | <ul><li>1.5.6</li><li>1.5.5</li></ul> | https://github.com/sanger-pathogens/circlator |
| [Clustalo](https://hub.docker.com/r/staphb/clustalo) <br/> [![docker pulls](https://badgen.net/docker/pulls/staphb/clustalo)](https://hub.docker.com/r/staphb/clustalo) | <ul><li>1.2.4</li></ul> | http://www.clustal.org/omega/ |
| [colorid](https://hub.docker.com/r/staphb/colorid) <br/> [![docker pulls](https://badgen.net/docker/pulls/staphb/colorid)](https://hub.docker.com/r/staphb/colorid) | <ul><li>0.1.4.3</li></ul> | https://github.com/hcdenbakker/colorid |
| [cutshaw-report-env](https://hub.docker.com/r/staphb/cutshaw-report-env) <br/> [![docker pulls](https://badgen.net/docker/pulls/staphb/cutshaw-report-env)](https://hub.docker.com/r/staphb/cutshaw-report-env) | <ul><li>1.0.0</li></ul> | https://github.com/VADGS/CutShaw |
| [datasets-sars-cov-2](https://github.com/CDCgov/datasets-sars-cov-2) <br/> [![docker pulls](https://badgen.net/docker/pulls/staphb/datasets-sars-cov-2)](https://hub.docker.com/r/staphb/datasets-sars-cov-2) | <ul><li>0.6.2</li><li>0.6.3</li><li>0.7.2</li></ul> | https://github.com/CDCgov/datasets-sars-cov-2 |
| [dnaapler](https://hub.docker.com/r/staphb/dnaapler) <br/> [![docker pulls](https://badgen.net/docker/pulls/staphb/dnaapler)](https://hub.docker.com/r/staphb/dnaapler) | <ul><li>[0.1.0](dnaapler/0.1.0/)</li></ul> <ul><li>[0.4.0](dnaapler/0.4.0/)</li></ul> | https://github.com/gbouras13/dnaapler |
| [dragonflye](https://hub.docker.com/r/staphb/dragonflye) <br/> [![docker pulls](https://badgen.net/docker/pulls/staphb/dragonflye)](https://hub.docker.com/r/staphb/dragonflye) | <ul><li>1.0.14</li><li>[1.1.1](dragonflye/1.1.1/)</li></ul> | https://github.com/rpetit3/dragonflye |
| [Dr. PRG ](https://hub.docker.com/r/staphb/drprg) <br/> [![docker pulls](https://badgen.net/docker/pulls/staphb/drprg)](https://hub.docker.com/r/staphb/drprg) | <ul><li>[0.1.1](drprg/0.1.1/)</li></ul> | https://mbh.sh/drprg/ |
| [DSK](https://hub.docker.com/r/staphb/dsk) <br/> [![docker pulls](https://badgen.net/docker/pulls/staphb/dsk)](https://hub.docker.com/r/staphb/dsk) | <ul><li>0.0.100</li></ul> | https://gatb.inria.fr/software/dsk/ |
| [emboss](https://hub.docker.com/r/staphb/emboss) <br/> [![docker pulls](https://badgen.net/docker/pulls/staphb/emboss)](https://hub.docker.com/r/staphb/emboss) | <ul><li>6.6.0 (no version)</li></ul> | http://emboss.sourceforge.net |
| [emmtyper](https://hub.docker.com/r/staphb/emmtyper) <br/> [![docker pulls](https://badgen.net/docker/pulls/staphb/emmtyper)](https://hub.docker.com/r/staphb/emmtyper) | <ul><li>0.2.0</li></ul> | https://github.com/MDU-PHL/emmtyper |
| [emm-typing-tool](https://hub.docker.com/r/staphb/emm-typing-tool) <br/> [![docker pulls](https://badgen.net/docker/pulls/staphb/emm-typing-tool)](https://hub.docker.com/r/staphb/emm-typing-tool) | <ul><li>0.0.1 (no version)</li></ul> | https://github.com/phe-bioinformatics/emm-typing-tool |
| [EToKi](https://hub.docker.com/r/staphb/etoki) <br/> [![docker pulls](https://badgen.net/docker/pulls/staphb/etoki)](https://hub.docker.com/r/staphb/etoki) | <ul><li>1.2.1</li></ul> | https://github.com/zheminzhou/EToKi |
| [FastANI](https://hub.docker.com/r/staphb/fastani) <br/> [![docker pulls](https://badgen.net/docker/pulls/staphb/fastani)](https://hub.docker.com/r/staphb/fastani) | <ul><li>1.1</li><li>1.32</li><li>1.33</li><li>1.33 + RGDv2</li><li>[1.34](fastani/1.34)</li><li>[1.34 + RGDv2](fastani/1.34-RGDV2/)</li></ul> | https://github.com/ParBLiSS/FastANI |
| [Fastp](https://hub.docker.com/r/staphb/fastp) <br/> [![docker pulls](https://badgen.net/docker/pulls/staphb/fastp)](https://hub.docker.com/r/staphb/fastp) | <ul><li>0.23.2</li><li>[0.23.4](fastp/0.23.4/)</li></ul> | http://opengene.org/fastp/ <br/> https://github.com/OpenGene/fastp |
| [FastTree](https://hub.docker.com/r/staphb/fasttree) <br/> [![docker pulls](https://badgen.net/docker/pulls/staphb/fasttree)](https://hub.docker.com/r/staphb/fasttree) | <ul><li>2.1.11</li></ul> | http://www.microbesonline.org/fasttree/ |
| [FastQC](https://hub.docker.com/r/staphb/fastqc) <br/> [![docker pulls](https://badgen.net/docker/pulls/staphb/fastqc)](https://hub.docker.com/r/staphb/fastqc) | <ul><li>0.11.8</li><li>0.11.9</li><li>0.12.1</li></ul> | https://www.bioinformatics.babraham.ac.uk/projects/fastqc/ <br/> https://github.com/s-andrews/FastQC |
| [fastq-scan](https://hub.docker.com/r/staphb/fastq-scan) <br/> [![docker pulls](https://badgen.net/docker/pulls/staphb/fastq-scan)](https://hub.docker.com/r/staphb/fastq-scan) | <ul><li>0.4.3</li><li>0.4.4</li><li>1.0.0</li><li>1.0.1</li></ul> | https://github.com/rpetit3/fastq-scan |
| [Freebayes](https://hub.docker.com/r/staphb/freebayes) <br/> [![docker pulls](https://badgen.net/docker/pulls/staphb/freebayes)](https://hub.docker.com/r/staphb/freebayes) | <ul><li>1.3.6</li><li>1.3.7</li><li>1.3.7</li><li>1.3.7</li></ul> | https://github.com/freebayes/freebayes |
| [Filtlong](https://hub.docker.com/r/staphb/filtlong) <br/> [![docker pulls](https://badgen.net/docker/pulls/staphb/filtlong)](https://hub.docker.com/r/staphb/filtlong) | <ul><li>0.2.0</li><li>0.2.1</li></ul> | https://github.com/rrwick/filtlong |
| [FLASH](https://hub.docker.com/r/staphb/flash) <br/> [![docker pulls](https://badgen.net/docker/pulls/staphb/flash)](https://hub.docker.com/r/staphb/flash) | <ul><li>1.2.11</li></ul> | http://ccb.jhu.edu/software/FLASH |
| [Flye](https://hub.docker.com/r/staphb/flye) <br/> [![docker pulls](https://badgen.net/docker/pulls/staphb/flye)](https://hub.docker.com/r/staphb/flye) | <ul><li>2.5</li><li>2.7</li><li>2.8</li><li>2.9</li><li>2.9.1</li><li>2.9.2</li></ul> | https://github.com/fenderglass/Flye |
| [Freyja](https://hub.docker.com/r/staphb/freyja) <br/> [![docker pulls](https://badgen.net/docker/pulls/staphb/freyja)](https://hub.docker.com/r/staphb/freyja) | <ul><li>1.2</li><li>1.2.1</li><li>1.3.1</li><li>1.3.2</li><li>1.3.4</li><li>1.3.7</li><li>1.3.8</li><li>1.3.9</li><li>1.3.10</li><li>1.3.11</li><li>1.3.12</li><li>1.4.2</li><li>[1.4.3](freyja/1.4.3/)</li><li>[1.4.4](freyja/1.4.4/)</li><li>[1.4.5](freyja/1.4.5/)</li><li>[1.4.7](freyja/1.4.7/)</li></ul> | https://github.com/andersen-lab/Freyja |
| [GAMBIT](https://hub.docker.com/r/staphb/gambit) <br/> [![docker pulls](https://badgen.net/docker/pulls/staphb/gambit)](https://hub.docker.com/r/staphb/gambit) | <ul><li>0.3.0</li><li>0.4.0</li><li>0.5.0</li><li>1.0.0</li></ul> | https://github.com/jlumpe/gambit |
| [GAMMA](https://hub.docker.com/r/staphb/gamma) <br/> [![docker pulls](https://badgen.net/docker/pulls/staphb/gamma)](https://hub.docker.com/r/staphb/gamma) | <ul><li>1.4</li><li>2.1</li><li>2.2</li></ul> | https://github.com/rastanton/GAMMA/ |
| [GenoVi](https://hub.docker.com/r/staphb/genovi) <br/> [![docker pulls](https://badgen.net/docker/pulls/staphb/genovi)](https://hub.docker.com/r/staphb/genovi) | <ul><li>[0.2.16](./genovi/0.2.16/)</li></ul> | https://github.com/robotoD/GenoVi |
| [gfastats](https://hub.docker.com/r/staphb/gfastats) <br/> [![docker pulls](https://badgen.net/docker/pulls/staphb/gfastats)](https://hub.docker.com/r/staphb/gfastats) | <ul><li>1.3.6</li></ul> | https://github.com/vgl-hub/gfastats |
| [hmmer](https://hub.docker.com/r/staphb/hmmer) <br/> [![docker pulls](https://badgen.net/docker/pulls/staphb/hmmer)](https://hub.docker.com/r/staphb/hmmer) | <ul><li>[3.3](hmmer/3.3/)</li><li>[3.3.2](hmmer/3.3.2/)</li></ul> | http://hmmer.org/ |
| [homopolish](https://hub.docker.com/r/staphb/homopolish) <br/> [![docker pulls](https://badgen.net/docker/pulls/staphb/homopolish)](https://hub.docker.com/r/staphb/homopolish) | <ul><li>0.4.1</li></ul> | https://github.com/ythuang0522/homopolish/ |
| [htslib](https://hub.docker.com/r/staphb/htslib) <br/> [![docker pulls](https://badgen.net/docker/pulls/staphb/htslib)](https://hub.docker.com/r/staphb/htslib) | <ul><li>1.14</li><li>1.15</li><li>1.16</li><li>1.17</li><li>[1.18](htslib/1.18/)</li></ul> | https://www.htslib.org/ |
| [iqtree](https://hub.docker.com/r/staphb/iqtree/) <br/> [![docker pulls](https://badgen.net/docker/pulls/staphb/iqtree)](https://hub.docker.com/r/staphb/iqtree) | <ul><li>1.6.7</li></ul> | http://www.iqtree.org/ |
| [iqtree2](https://hub.docker.com/r/staphb/iqtree2/) <br/> [![docker pulls](https://badgen.net/docker/pulls/staphb/iqtree2)](https://hub.docker.com/r/staphb/iqtree2) | <ul><li>2.1.2</li><li>2.2.2.2</li><li>[2.2.2.6](iqtree2/2.2.2.6/)</li></ul> | http://www.iqtree.org/ |
| [IRMA](https://hub.docker.com/r/staphb/irma/) <br/> [![docker pulls](https://badgen.net/docker/pulls/staphb/irma)](https://hub.docker.com/r/staphb/irma) | <ul><li>1.0.2</li><li>1.0.3</li><li>1.1.2</li><li>1.1.3</li></ul> | https://wonder.cdc.gov/amd/flu/irma/|
| [iVar](https://hub.docker.com/r/staphb/ivar/) <br/> [![docker pulls](https://badgen.net/docker/pulls/staphb/ivar)](https://hub.docker.com/r/staphb/ivar) | <ul><li>1.1</li><li>1.1 (+SARS-CoV2 reference)</li><li>1.2.1</li><li>1.2.1 (+SC2 ref)</li><li>1.2.2 (+SC2 ref and artic bedfiles)</li><li>1.3</li><li>1.3.1</li><li>1.3.2</li><li>1.4.1</li><li>1.4.2</li></ul> | https://github.com/andersen-lab/ivar |
| [Kaptive](https://hub.docker.com/r/staphb/kaptive/) <br/> [![docker pulls](https://badgen.net/docker/pulls/staphb/kaptive)](https://hub.docker.com/r/staphb/kaptive) | <ul><li>2.0.0</li><li>2.0.3</li><li>2.0.5</li></ul> | https://github.com/katholt/Kaptive/ |
| [Kleborate](https://hub.docker.com/r/staphb/kleborate/) <br/> [![docker pulls](https://badgen.net/docker/pulls/staphb/kleborate)](https://hub.docker.com/r/staphb/kleborate) | <ul><li>2.0.4</li><li>2.1.0</li><li>2.2.0</li><li>2.3.2</li><li>[2.3.2-2023-05](kleborate/2.3.2-2023-05/README.md)</li></ul> | https://github.com/katholt/Kleborate/ <br/> https://github.com/katholt/Kaptive/ |
| [kma](https://hub.docker.com/r/staphb/kma/) <br/> [![docker pulls](https://badgen.net/docker/pulls/staphb/kma)](https://hub.docker.com/r/staphb/kma) | <ul><li>1.2.21</li><li>1.4.10 (no database)</li></ul> | https://bitbucket.org/genomicepidemiology/kma/ |
| [Kraken](https://hub.docker.com/r/staphb/kraken/) <br/> [![docker pulls](https://badgen.net/docker/pulls/staphb/kraken)](https://hub.docker.com/r/staphb/kraken) | <ul><li>1.0</li><li>1.1.1</li><li>1.1.1 (no database)</li></ul> | https://github.com/DerrickWood/kraken |
| [Kraken2](https://hub.docker.com/r/staphb/kraken2/) <br/> [![docker pulls](https://badgen.net/docker/pulls/staphb/kraken2)](https://hub.docker.com/r/staphb/kraken2) | <ul><li>2.0.8-beta (no database)</li><li>2.0.8-beta (MiniKraken2_v1_8GB db)</li><li>2.0.8-beta_hv (human + virus db)</li><li>2.0.9-beta (no db)</li><li>2.0.9-beta (Minikraken v2 RefSeq: bacteria, archaea, viral, and human 8GB db)</li><li>2.1.0 (no db)</li><li>2.1.1 (no db)</li><li>2.1.2 (no db)</li><li>[2.1.3](kraken2/2.1.3/) (no db)</li></ul> | https://github.com/DerrickWood/kraken2 |
| [kSNP3](https://hub.docker.com/r/staphb/ksnp3/) <br/> [![docker pulls](https://badgen.net/docker/pulls/staphb/ksnp3)](https://hub.docker.com/r/staphb/ksnp3)| <ul><li>3.1</li></ul> | https://sourceforge.net/projects/ksnp/ |
| [kSNP4](https://hub.docker.com/r/staphb/ksnp4/) <br/> [![docker pulls](https://badgen.net/docker/pulls/staphb/ksnp4)](https://hub.docker.com/r/staphb/ksnp4)| <ul><li>4.0</li></ul> | https://sourceforge.net/projects/ksnp/ |
| [legsta](https://hub.docker.com/r/staphb/legsta/) <br/> [![docker pulls](https://badgen.net/docker/pulls/staphb/legsta)](https://hub.docker.com/r/staphb/legsta)| <ul><li>0.3.7</li><li>0.5.1</li></ul> | https://github.com/tseemann/legsta |
| [liftoff](https://hub.docker.com/r/staphb/liftoff/) <br/> [![docker pulls](https://badgen.net/docker/pulls/staphb/liftoff)](https://hub.docker.com/r/staphb/liftoff)| <ul><li>1.6.3</li></ul> | https://github.com/agshumate/Liftoff |
| [longshot](https://hub.docker.com/r/staphb/longshot/) <br/> [![docker pulls](https://badgen.net/docker/pulls/staphb/longshot)](https://hub.docker.com/r/staphb/longshot)| <ul><li>[0.4.5](longshot/0.4.5)</li></ul> | https://github.com/pjedge/longshot |
| [Lyve-SET (includes CG-Pipeline scripts and raxml)](https://hub.docker.com/r/staphb/lyveset/) <br/> [![docker pulls](https://badgen.net/docker/pulls/staphb/lyveset)](https://hub.docker.com/r/staphb/lyveset) | <ul><li>1.1.4f</li><li>2.0.1</li></ul> | https://github.com/lskatz/lyve-SET https://github.com/lskatz/CG-Pipeline |
| [MAFFT](https://hub.docker.com/r/staphb/mafft/) <br/> [![docker pulls](https://badgen.net/docker/pulls/staphb/mafft)](https://hub.docker.com/r/staphb/mafft)  | <ul><li>7.450</li><li>7.475</li><li>7.505</li></ul> | https://mafft.cbrc.jp/alignment/software/ |
| [Mash](https://hub.docker.com/r/staphb/mash/) <br/> [![docker pulls](https://badgen.net/docker/pulls/staphb/mash)](https://hub.docker.com/r/staphb/mash)  | <ul><li>2.1</li><li>2.2</li><li>2.3</li></ul> | https://github.com/marbl/Mash |
| [mashtree](https://hub.docker.com/r/staphb/mashtree) <br/> [![docker pulls](https://badgen.net/docker/pulls/staphb/mashtree)](https://hub.docker.com/r/staphb/mashtree) | <ul><li>0.52.0</li><li>0.57.0</li><li>1.0.4</li><li>1.2.0</li></ul> | https://github.com/lskatz/mashtree |
| [MaSuRCA](https://hub.docker.com/r/staphb/masurca) <br/> [![docker pulls](https://badgen.net/docker/pulls/staphb/masurca)](https://hub.docker.com/r/staphb/masurca) | <ul><li>4.0.8</li><li>4.0.9</li><li>4.1.0</li></ul> | https://github.com/alekseyzimin/masurca |
| [medaka](https://hub.docker.com/r/staphb/medaka) <br/> [![docker pulls](https://badgen.net/docker/pulls/staphb/medaka)](https://hub.docker.com/r/staphb/medaka) | <ul><li>0.8.1</li><li>1.0.1</li><li>1.2.0</li></ul> | https://github.com/nanoporetech/medaka |
| [metaphlan](https://hub.docker.com/r/staphb/metaphlan) <br/> [![docker pulls](https://badgen.net/docker/pulls/staphb/metaphlan)](https://hub.docker.com/r/staphb/metaphlan) | <ul><li>3.0.3-no-db (no database)</li><li> 3.0.3 (~3GB db) | https://github.com/biobakery/MetaPhlAn/tree/3.0 |
| [MIDAS](https://hub.docker.com/r/staphb/midas) <br/> [![docker pulls](https://badgen.net/docker/pulls/staphb/midas)](https://hub.docker.com/r/staphb/midas) | <ul><li>1.3.2 (no database)</li> | https://github.com/snayfach/MIDAS |
| [minimap2](https://hub.docker.com/r/staphb/minimap2) <br/> [![docker pulls](https://badgen.net/docker/pulls/staphb/minimap2)](https://hub.docker.com/r/staphb/minimap2) | <ul><li>2.17</li><li>2.18</li><li>2.21</li><li>2.22</li><li>2.23</li><li>2.24</li><li>2.25</li></ul> | https://github.com/lh3/minimap2 |
| [minipolish](https://hub.docker.com/r/staphb/minipolish) <br/> [![docker pulls](https://badgen.net/docker/pulls/staphb/minipolish)](https://hub.docker.com/r/staphb/minipolish) | <ul><li>0.1.3</li></ul> | https://github.com/rrwick/Minipolish |
| [mlst](https://hub.docker.com/r/staphb/mlst) <br/> [![docker pulls](https://badgen.net/docker/pulls/staphb/mlst)](https://hub.docker.com/r/staphb/mlst) | <ul><li>2.16.2</li><li>2.17.6</li><li>2.19.0</li><li>2.22.0</li><li>2.22.1</li><li>2.23.0</li><li>[2.23.0-2023-07 (databases updated July 2023)](mlst/2.23.0-2023-07/)</li><li>[2.23.0-2023-08 (databases updated Aug 2023)](mlst/2.23.0-2023-08/)</li></ul> | https://github.com/tseemann/mlst |
| [Mugsy](https://hub.docker.com/r/staphb/mugsy) <br/> [![docker pulls](https://badgen.net/docker/pulls/staphb/mugsy)](https://hub.docker.com/r/staphb/mugsy) | <ul><li>1r2.3</li></ul> | http://mugsy.sourceforge.net/ |
| [MultiQC](https://hub.docker.com/r/staphb/multiqc) <br/> [![docker pulls](https://badgen.net/docker/pulls/staphb/multiqc)](https://hub.docker.com/r/staphb/multiqc) | <ul><li>1.7</li><li>1.8</li></ul> | https://github.com/ewels/MultiQC |
| [Mummer](https://hub.docker.com/r/staphb/mummer) <br/> [![docker pulls](https://badgen.net/docker/pulls/staphb/mummer)](https://hub.docker.com/r/staphb/mummer) | <ul><li>4.0.0</li><li>4.0.0 + RGDv2</li><li>4.0.0 + RGDv2 + gnuplot</li></ul> | https://github.com/mummer4/mummer |
| [Mykrobe + Genotyphi + sonneityping](https://hub.docker.com/r/staphb/mykrobe) <br/> [![docker pulls](https://badgen.net/docker/pulls/staphb/mykrobe)](https://hub.docker.com/r/staphb/mykrobe) | <ul><li>0.11.0 (Mykrobe) & 1.9.1 (Genotyphi) </li><li>0.12.1 (Mykrobe) & 1.9.1 (Genotyphi) & v20210201 (sonneityping) </li><li>0.12.1 (Mykrobe) & 2.0 (Genotyphi) & v20210201 (sonneityping) </li><li>[0.12.2 (Mykrobe) & 2.0 (Genotyphi) & v20210201 (sonneityping)](mykrobe/0.12.2/)</li><li>[0.13.0](./mykrobe/0.13.0)</li></ul> | https://github.com/Mykrobe-tools/mykrobe <br/> https://github.com/typhoidgenomics/genotyphi <br/> https://github.com/katholt/sonneityping |
| [NanoPlot](https://hub.docker.com/r/staphb/nanoplot) <br/> [![docker pulls](https://badgen.net/docker/pulls/staphb/nanoplot)](https://hub.docker.com/r/staphb/nanoplot) | <ul><li>1.27.0</li><li>1.29.0</li><li>1.30.1</li><li>1.32.0</li><li>1.33.0</li><li>1.40.0</li><li>[1.41.6](nanoplot/1.41.6/)</li></ul> | https://github.com/wdecoster/NanoPlot |
| [ngmaster](https://hub.docker.com/r/staphb/ngmaster) <br/> [![docker pulls](https://badgen.net/docker/pulls/staphb/ngmaster)](https://hub.docker.com/r/staphb/ngmaster) | <ul><li>0.5.8</li><li>1.0.0</li></ul> | https://github.com/MDU-PHL/ngmaster |
| [NCBI Datasets](https://hub.docker.com/r/staphb/ncbi-datasets) <br/> [![docker pulls](https://badgen.net/docker/pulls/staphb/ncbi-datasets)](https://hub.docker.com/r/staphb/ncbi-datasets) | <details><summary> Click to see all datasets versions </summary> **datasets versions** <ul><li>13.31.0</li><li>13.35.0</li><li>13.43.2</li><li>14.0.0</li><li>14.3.0</li><li>14.7.0</li><li>14.13.2</li><li>14.20.0</li><li>[14.27.0](ncbi-datasets/14.27.0/)</li><li>[15.1.0](ncbi-datasets/15.1.0/)</li><li>[15.2.0](ncbi-datasets/15.2.0/)</li><li>[15.11.0](ncbi-datasets/15.11.0/)</li><li>[15.27.1](ncbi-datasets/15.27.1/)</li></ul> | [https://github.com/ncbi/datasets](https://github.com/ncbi/datasets) <br/>[https://www.ncbi.nlm.nih.gov/datasets/docs/v1/](https://www.ncbi.nlm.nih.gov/datasets/docs/v1/) |
| [NCBI AMRFinderPlus](https://hub.docker.com/r/staphb/ncbi-amrfinderplus) <br/> [![docker pulls](https://badgen.net/docker/pulls/staphb/ncbi-amrfinderplus)](https://hub.docker.com/r/staphb/ncbi-amrfinderplus) | **AMRFinderPlus & database verion** <details><summary> Click to see AMRFinderplus v3.11.4 and older versions! </summary> <ul><li>3.1.1b</li><li>3.8.4</li><li>3.8.28</li><li>3.9.3</li><li>3.9.8</li><li>3.10.1</li><li>3.10.5</li><li>3.10.16</li><li>3.10.20</li><li>3.10.24</li><li>3.10.30</li><li>3.10.36</li><li>3.10.42</li><li>3.11.2 & 2022-12-19.1</li><li>[3.11.2 & 2023-02-23.1](ncbi-amrfinderplus/3.11.2-2023-02-23.1/)</li><li>[3.11.4 & 2023-02-23.1](ncbi-amrfinderplus/3.11.4-2023-02-23.1/)</li></ul> </details> <ul><li>[3.11.8 & 2023-02-23.1](ncbi-amrfinderplus/3.11.8-2023-02-23.1/)</li><li>[3.11.11 & 2023-04-17.1](ncbi-amrfinderplus/3.11.11-2023-04-17.1)</li><li>[3.11.14 & 2023-04-17.1](ncbi-amrfinderplus/3.11.14-2023-04-17.1/)</li><li>[3.11.17 & 2023-07-13.2](ncbi-amrfinderplus/3.11.17-2023-07-13.2/)</li><li>[3.11.18 & 2023-08-08.2](ncbi-amrfinderplus/3.11.18-2023-08-08.2/)</li><li>[3.11.20 & 2023-09-26.1](ncbi-amrfinderplus/3.11.20-2023-09-26.1/)</li><li>[3.11.26 & 2023-11-15.1](ncbi-amrfinderplus/3.11.26-2023-11-15.1/)</li></ul> | [https://github.com/ncbi/amr](https://github.com/ncbi/amr) |
| [NCBI table2asn](https://hub.docker.com/r/staphb/ncbi-table2asn) <br/> [![docker pulls](https://badgen.net/docker/pulls/staphb/ncbi-table2asn)](https://hub.docker.com/r/staphb/ncbi-table2asn) | <ul><li>1.26.678</li></ul> | [https://www.ncbi.nlm.nih.gov/genbank/table2asn/](https://www.ncbi.nlm.nih.gov/genbank/table2asn/) <br/>[https://ftp.ncbi.nlm.nih.gov/asn1-converters/versions/2022-06-14/by_program/table2asn/](https://ftp.ncbi.nlm.nih.gov/asn1-converters/versions/2022-06-14/by_program/table2asn/) |
| [ONTime](https://hub.docker.com/r/staphb/ontime) <br/> [![docker pulls](https://badgen.net/docker/pulls/staphb/ontime)](https://hub.docker.com/r/staphb/ontime) | <ul><li>[0.2.3](ontime/0.2.3/)</li></ul> | https://github.com/mbhall88/ontime |
| [OrthoFinder](https://hub.docker.com/r/staphb/OrthoFinder) <br/> [![docker pulls](https://badgen.net/docker/pulls/staphb/orthofinder)](https://hub.docker.com/r/staphb/orthofinder) | <ul><li>2.17</li></ul> | https://github.com/davidemms/OrthoFinder |
| [Panaroo](https://hub.docker.com/r/staphb/panaroo)  <br/> [![docker pulls](https://badgen.net/docker/pulls/staphb/panaroo)](https://hub.docker.com/r/staphb/panaroo) | <ul><li>1.2.10</li></ul> | https://github.com/gtonkinhill/panaroo |
| [Pangolin](https://hub.docker.com/r/staphb/pangolin) <br/> [![docker pulls](https://badgen.net/docker/pulls/staphb/pangolin)](https://hub.docker.com/r/staphb/pangolin) | <details><summary> Click to see Pangolin v4.2 and older versions! </summary> **Pangolin version & pangoLEARN data release date** <ul><li>1.1.14</li><li>2.0.4 & 2020-07-20</li><li>2.0.5 & 2020-07-20</li><li>2.1.1 & 2020-12-17</li><li>2.1.3 & 2020-12-17</li><li>2.1.6 & 2021-01-06</li><li>2.1.7 & 2021-01-11</li><li>2.1.7 & 2021-01-20</li><li>2.1.8 & 2021-01-22</li><li>2.1.10 & 2021-02-01</li><li>2.1.11 & 2021-02-01</li><li>2.1.11 & 2021-02-05</li><li>2.2.1 & 2021-02-06</li><li>2.2.2 & 2021-02-06</li><li>2.2.2 & 2021-02-11</li><li>2.2.2 & 2021-02-12</li><li>2.3.0 & 2021-02-12</li><li>2.3.0 & 2021-02-18</li><li>2.3.0 & 2021-02-21</li><li>2.3.2 & 2021-02-21</li><li>2.3.3 & 2021-03-16</li><li>2.3.4 & 2021-03-16</li><li>2.3.5 & 2021-03-16</li><li>2.3.6 & 2021-03-16</li><li>2.3.6 & 2021-03-29</li><li>2.3.8 & 2021-04-01</li><li>2.3.8 & 2021-04-14</li><li>2.3.8 & 2021-04-21</li><li>2.3.8 & 2021-04-23</li><li>2.4 & 2021-04-28</li><li>2.4.1 & 2021-04-28</li><li>2.4.2 & 2021-04-28</li><li>2.4.2 & 2021-05-10</li><li>2.4.2 & 2021-05-11</li><li>2.4.2 & 2021-05-19</li><li>3.0.5 & 2021-06-05</li><li>3.1.3 & 2021-06-15</li><li>3.1.5 & 2021-06-15</li><li>3.1.5 & 2021-07-07-2</li><li>3.1.7 & 2021-07-09</li><li>3.1.8 & 2021-07-28</li><li>3.1.10 & 2021-07-28</li><li>3.1.11 & 2021-08-09</li><li>3.1.11 & 2021-08-24</li><li>3.1.11 & 2021-09-17</li><li>3.1.14 & 2021-09-28</li><li>3.1.14 & 2021-10-13</li><li>3.1.16 & 2021-10-18</li><li>3.1.16 & 2021-11-04</li><li>3.1.16 & 2021-11-09</li><li>3.1.16 & 2021-11-18</li><li>3.1.16 & 2021-11-25</li><li>3.1.17 & 2021-11-25</li><li>3.1.17 & 2021-12-06</li><li>3.1.17 & 2022-01-05</li><li>3.1.18 & 2022-01-20</li><li>3.1.19 & 2022-01-20</li><li>3.1.20 & 2022-02-02</li><li>3.1.20 & 2022-02-28</li></ul> **Pangolin version & pangolin-data version** <ul><li>4.0 & 1.2.133</li><li>4.0.1 & 1.2.133</li><li>4.0.2 & 1.2.133</li><li>4.0.3 & 1.2.133</li><li>4.0.4 & 1.2.133</li><li>4.0.5 & 1.3</li><li>4.0.6 & 1.6</li><li>4.0.6 & 1.8</li><li>4.0.6 & 1.9</li><li>4.1.1 & 1.11</li><li>4.1.2 & 1.12</li><li>4.1.2 & 1.13</li><li>4.1.2 & 1.14</li><li>4.1.3 & 1.15.1</li><li>4.1.3 & 1.16</li><li>4.1.3 & 1.17</li><li>4.2 & 1.18</li><li>4.2 & 1.18.1</li><li>4.2 & 1.18.1.1</li><li>4.2 & 1.19</li></ul> </details> **Pangolin version & pangolin-data version** <ul><li>[4.3 & 1.20](pangolin/4.3-pdata-1.20/)</li><li>[4.3 & 1.21](pangolin/4.3-pdata-1.21/)</li><li>[4.3.1 & 1.22](pangolin/4.3.1-pdata-1.22/)</li><li>[4.3.1 & 1.23](pangolin/4.3.1-pdata-1.23/)</li><li>[4.3.1 & 1.23.1](pangolin/4.3.1-pdata-1.23.1/)</li></ul> |  https://github.com/cov-lineages/pangolin<br/>https://github.com/cov-lineages/pangoLEARN<br/>https://github.com/cov-lineages/pango-designation<br/>https://github.com/cov-lineages/scorpio<br/>https://github.com/cov-lineages/constellations<br/>https://github.com/cov-lineages/lineages (archived)<br/>https://github.com/hCoV-2019/pangolin (archived) |
| [parallel-perl](https://hub.docker.com/r/staphb/parallel-perl) <br/> [![docker pulls](https://badgen.net/docker/pulls/staphb/parallel-perl)](https://hub.docker.com/r/staphb/parallel-perl) | <ul><li>20200722</li></ul> | https://www.gnu.org/software/parallel |
| [pasty](https://hub.docker.com/r/staphb/pasty) <br/> [![docker pulls](https://badgen.net/docker/pulls/staphb/pasty)](https://hub.docker.com/r/staphb/pasty) | <ul><li>1.0.2</li><li>[1.0.3](pasty/1.0.3/)</li></ul> | https://github.com/rpetit3/pasty |
| [Pavian](https://hub.docker.com/r/staphb/pavian) <br/> [![docker pulls](https://badgen.net/docker/pulls/staphb/pavian)](https://hub.docker.com/r/staphb/pavian) | <ul><li>[1.2.1](pavian/1.2.1/)</li></ul> | https://github.com/fbreitwieser/pavian |
| [pbptyper](https://hub.docker.com/r/staphb/pbptyper) <br/> [![docker pulls](https://badgen.net/docker/pulls/staphb/pbptyper)](https://hub.docker.com/r/staphb/pbptyper) | <ul><li>1.0.0</li><li>1.0.1</li><li>1.0.4</li></ul> | https://github.com/rpetit3/pbptyper |
| [Phyml](https://hub.docker.com/r/staphb/phyml) <br/> [![docker pulls](https://badgen.net/docker/pulls/staphb/phyml)](https://hub.docker.com/r/staphb/phyml) | <ul><li>3.3.20220408</li></ul> | https://github.com/stephaneguindon/phyml |
| [Piggy](https://hub.docker.com/r/staphb/piggy) <br/> [![docker pulls](https://badgen.net/docker/pulls/staphb/piggy)](https://hub.docker.com/r/staphb/piggy) | <ul><li>1.5</li></ul> | https://github.com/harry-thorpe/piggy |
| [Pilon](https://hub.docker.com/r/staphb/pilon) <br/> [![docker pulls](https://badgen.net/docker/pulls/staphb/pilon)](https://hub.docker.com/r/staphb/pilon) | <ul><li>1.23.0</li><li>1.24</li></ul> | https://github.com/broadinstitute/pilon |
| [Piranha](https://hub.docker.com/r/staphb/piranha) <br/> [![docker pulls](https://badgen.net/docker/pulls/staphb/piranha)](https://hub.docker.com/r/staphb/piranha) | <ul><li>1.0.4</li></ul> | https://github.com/polio-nanopore/piranha |
| [PlasmidFinder](https://hub.docker.com/r/staphb/plasmidfinder) <br/> [![docker pulls](https://badgen.net/docker/pulls/staphb/plasmidfinder)](https://hub.docker.com/r/staphb/plasmidfinder) | <ul><li>2.1.6</li></ul> | https://bitbucket.org/genomicepidemiology/plasmidfinder/src/master/ |
| [PlasmidSeeker](https://hub.docker.com/r/staphb/plasmidseeker) <br/> [![docker pulls](https://badgen.net/docker/pulls/staphb/plasmidseeker)](https://hub.docker.com/r/staphb/plasmidseeker) | <ul><li>1.0</li><li>1.3</li></ul> | https://github.com/bioinfo-ut/PlasmidSeeker |
| [pmga](https://hub.docker.com/r/staphb/pmga/) <br/> [![docker pulls](https://badgen.net/docker/pulls/staphb/pmga)](https://hub.docker.com/r/staphb/pmga) | <ul><li>3.0.2</li></ul> | https://github.com/rpetit3/pmga |
| [polypolish](https://hub.docker.com/r/staphb/polypolish/) <br/> [![docker pulls](https://badgen.net/docker/pulls/staphb/polypolish)](https://hub.docker.com/r/staphb/polypolish) | <ul><li>0.5.0</li></ul> | https://github.com/rrwick/Polypolish |
| [PopPUNK](https://hub.docker.com/r/staphb/poppunk/) <br/> [![docker pulls](https://badgen.net/docker/pulls/staphb/poppunk)](https://hub.docker.com/r/staphb/poppunk) | <ul><li>2.4.0</li><li>2.5.0</li><li>2.6.0</li></ul> | https://github.com/bacpop/PopPUNK |
| [Porechop](https://hub.docker.com/r/staphb/porechop/) <br/> [![docker pulls](https://badgen.net/docker/pulls/staphb/porechop)](https://hub.docker.com/r/staphb/porechop) | <ul><li>0.2.4</li></ul> | https://github.com/rrwick/Porechop |
| [Prokka](https://hub.docker.com/r/staphb/prokka/) <br/> [![docker pulls](https://badgen.net/docker/pulls/staphb/prokka)](https://hub.docker.com/r/staphb/prokka) | <ul><li>1.13.4</li><li>1.14.0</li><li>1.14.5</li><li>1.14.6</li></ul> | https://github.com/tseemann/prokka |
| [pyCirclize](https://hub.docker.com/r/staphb/pycirclize/) <br/> [![docker pulls](https://badgen.net/docker/pulls/staphb/pycirclize)](https://hub.docker.com/r/staphb/pycirclize) | <ul><li>[1.0.0](pycirclize/1.0.0/)</li></ul> | https://github.com/moshi4/pyCirclize |
| [pyGenomeViz](https://hub.docker.com/r/staphb/pygenomeviz/) <br/> [![docker pulls](https://badgen.net/docker/pulls/staphb/pygenomeviz)](https://hub.docker.com/r/staphb/pygenomeviz) | <ul><li>0.2.2</li><li>0.3.2</li><li>[0.4.2](pygenomeviz/0.4.2/)</li><li>[0.4.3](pygenomeviz/0.4.3/)</li></ul> | https://github.com/moshi4/pyGenomeViz |
| [QUAST](https://hub.docker.com/r/staphb/quast/) <br/> [![docker pulls](https://badgen.net/docker/pulls/staphb/quast)](https://hub.docker.com/r/staphb/quast) | <ul><li>5.0.0</li><li>5.0.2</li><li>[5.2.0](./quast/5.2.0)</li></ul> | https://github.com/ablab/quast |
| [QuickSNP](https://hub.docker.com/r/staphb/quicksnp/) <br/> [![docker pulls](https://badgen.net/docker/pulls/staphb/quicksnp)](https://hub.docker.com/r/staphb/quicksnp) | <ul><li>1.0.1</li></ul> | https://github.com/k-florek/QuickSNP |
| [racon](https://hub.docker.com/r/staphb/racon) <br/> [![docker pulls](https://badgen.net/docker/pulls/staphb/racon)](https://hub.docker.com/r/staphb/racon)| <ul><li>1.4.3</li><li>1.4.20</li></ul> |  https://github.com/lbcb-sci/racon <br/> https://github.com/isovic/racon (ARCHIVED) |
| [rasusa](https://hub.docker.com/r/staphb/rasusa/) <br/> [![docker pulls](https://badgen.net/docker/pulls/staphb/rasusa)](https://hub.docker.com/r/staphb/rasusa) | <ul><li>0.1.0</li><li>0.2.0</li><li>0.3.0</li><li>0.6.0</li><li>0.7.0</li></ul> | https://github.com/mbhall88/rasusa |
| [raven](https://hub.docker.com/r/staphb/raven/) <br/> [![docker pulls](https://badgen.net/docker/pulls/staphb/raven)](https://hub.docker.com/r/staphb/raven) | <ul><li>1.5.1</li><li>1.8.1</li><li>[1.8.3](./raven/1.8.3)</li></ul> | https://github.com/lbcb-sci/raven |
| [RAxML](https://hub.docker.com/r/staphb/raxml/) <br/> [![docker pulls](https://badgen.net/docker/pulls/staphb/raxml)](https://hub.docker.com/r/staphb/raxml) | <ul><li>8.2.12 (RAxML) and 0.9.0 (RAxML Next Generation)</li></ul> | https://github.com/stamatak/standard-RAxML <br/> https://github.com/amkozlov/raxml-ng |
| [ResFinder](https://hub.docker.com/r/staphb/resfinder/) <br/> [![docker pulls](https://badgen.net/docker/pulls/staphb/resfinder)](https://hub.docker.com/r/staphb/resfinder) | <ul><li>4.1.1</li></ul> | https://bitbucket.org/genomicepidemiology/resfinder/src/master/ |
| [Roary](https://hub.docker.com/r/staphb/roary/) <br/> [![docker pulls](https://badgen.net/docker/pulls/staphb/roary)](https://hub.docker.com/r/staphb/roary) | <ul><li>3.12.0</li><li>3.13.0</li></ul> | https://github.com/sanger-pathogens/Roary |
| [SalmID](https://hub.docker.com/r/staphb/salmid) <br/> [![docker pulls](https://badgen.net/docker/pulls/staphb/salmid)](https://hub.docker.com/r/staphb/salmid) | <ul><li>0.1.23</li></ul> | https://github.com/hcdenbakker/SalmID |
| [Samtools](https://hub.docker.com/r/staphb/samtools) <br/> [![docker pulls](https://badgen.net/docker/pulls/staphb/samtools)](https://hub.docker.com/r/staphb/samtools) | <ul><li>1.9</li><li>1.10</li><li>1.11</li><li>1.12</li><li>1.13</li><li>1.14</li><li>1.15</li><li>1.16</li><li>1.16.1</li><li>1.17</li><li>[1.17-2023-06](samtools/1.17-2023-06/)</li><li>[1.18](samtools/1.18/)</li></ul> | https://github.com/samtools/samtools |
| [SeqKit](https://hub.docker.com/r/staphb/SeqKit) <br/> [![docker pulls](https://badgen.net/docker/pulls/staphb/seqkit)](https://hub.docker.com/r/staphb/seqkit) | <ul><li>2.3.1</li></ul> | https://github.com/shenwei356/seqkit |
| [SeqSero](https://hub.docker.com/r/staphb/seqsero/) <br/> [![docker pulls](https://badgen.net/docker/pulls/staphb/seqsero)](https://hub.docker.com/r/staphb/seqsero) | <ul><li>1.0.1</li></ul> | https://github.com/denglab/SeqSero |
| [SeqSero2](https://hub.docker.com/r/staphb/seqsero2/) <br/> [![docker pulls](https://badgen.net/docker/pulls/staphb/seqsero2)](https://hub.docker.com/r/staphb/seqsero2) | <ul><li>0.1.0</li><li>1.0.0</li><li>1.0.2</li><li>1.1.0</li><li>1.1.1</li><li>1.2.1</li></ul> | https://github.com/denglab/SeqSero2/ |
| [seqtk](https://hub.docker.com/r/staphb/seqtk) <br/> [![docker pulls](https://badgen.net/docker/pulls/staphb/seqtk)](https://hub.docker.com/r/staphb/seqtk) | <ul><li>1.3</li></ul> | https://github.com/lh3/seqtk |
| [seqyclean](https://hub.docker.com/r/staphb/seqyclean) <br/> [![docker pulls](https://badgen.net/docker/pulls/staphb/seqyclean)](https://hub.docker.com/r/staphb/seqyclean) | <ul><li>1.10.09</li></ul> | https://github.com/ibest/seqyclean |
| [Seroba](https://hub.docker.com/r/staphb/seroba) <br/> [![docker pulls](https://badgen.net/docker/pulls/staphb/seroba)](https://hub.docker.com/r/staphb/seroba) | <ul><li>1.0.0</li><li>1.0.2</li></ul> | https://github.com/sanger-pathogens/seroba |
| [SerotypeFinder](https://hub.docker.com/r/staphb/serotypefinder/) <br/> [![docker pulls](https://badgen.net/docker/pulls/staphb/serotypefinder)](https://hub.docker.com/r/staphb/serotypefinder) | <ul><li>1.1 (perl version)</li><li>2.0.1 (python version)</li></ul> | https://bitbucket.org/genomicepidemiology/serotypefinder/ |
| [shigatyper](https://hub.docker.com/r/staphb/shigatyper/) <br/> [![docker pulls](https://badgen.net/docker/pulls/staphb/shigatyper)](https://hub.docker.com/r/staphb/shigatyper) | <ul><li>2.0.1</li><li>2.0.2</li><li>2.0.3</li><li>[2.0.4](shigatyper/2.0.4/)</li><li>[2.0.5](shigatyper/2.0.5/)</li></ul> | https://github.com/CFSAN-Biostatistics/shigatyper |
| [ShigEiFinder](https://hub.docker.com/r/staphb/shigeifinder/) <br/> [![docker pulls](https://badgen.net/docker/pulls/staphb/shigeifinder)](https://hub.docker.com/r/staphb/shigeifinder) | <ul><li>[1.3.2](shigeifinder/1.3.2/)</li><li>[1.3.3](shigeifinder/1.3.3/)</li><li>[1.3.5](shigeifinder/1.3.5/)</li></ul> | https://github.com/LanLab/ShigEiFinder |
| [Shovill](https://hub.docker.com/r/staphb/shovill/) <br/> [![docker pulls](https://badgen.net/docker/pulls/staphb/shovill)](https://hub.docker.com/r/staphb/shovill) | <ul><li>1.0.4</li><li>1.1.0</li></ul> | https://github.com/tseemann/shovill |
| [Shovill-se](https://hub.docker.com/r/staphb/shovill-se/) <br/> [![docker pulls](https://badgen.net/docker/pulls/staphb/shovill-se)](https://hub.docker.com/r/staphb/shovill-se) | <ul><li>1.1.0</li></ul> | https://github.com/rpetit3/shovill/tree/v1.1.0se |
| [SISTR](https://hub.docker.com/r/staphb/sistr/) <br/> [![docker pulls](https://badgen.net/docker/pulls/staphb/sistr)](https://hub.docker.com/r/staphb/sistr) | <ul><li>1.0.2</li><li>1.1.1</li></ul> | https://github.com/phac-nml/sistr_cmd |
| [SKA](https://hub.docker.com/r/staphb/ska/) <br/> [![docker pulls](https://badgen.net/docker/pulls/staphb/ska)](https://hub.docker.com/r/staphb/ska) | <ul><li>1.0</li></ul> | https://github.com/simonrharris/SKA |
| [skani](https://github.com/bluenote-1577/skani) <br/> [![docker pulls](https://badgen.net/docker/pulls/staphb/skani)](https://hub.docker.com/r/staphb/skani) | <ul><li>[0.2.0](./skani/0.2.0)</li><li>[0.2.1](./skani/0.2.1)</li></ul> | https://github.com/bluenote-1577/skani |
| [SKESA](https://hub.docker.com/r/staphb/skesa) <br/> [![docker pulls](https://badgen.net/docker/pulls/staphb/skesa)](https://hub.docker.com/r/staphb/skesa) | <ul><li>2.3.0</li><li>2.4.0 (`gfa_connector` & `kmercounter` included)</li></ul> | https://github.com/ncbi/SKESA |
| [Smalt](https://hub.docker.com/r/staphb/smalt) <br/> [![docker pulls](https://badgen.net/docker/pulls/staphb/smalt)](https://hub.docker.com/r/staphb/smalt) | <ul><li>0.7.6</li></ul> | https://www.sanger.ac.uk/tool/smalt-0/ |
| [snpeff](https://hub.docker.com/r/staphb/snpeff) <br/> [![docker pulls](https://badgen.net/docker/pulls/staphb/snpeff)](https://hub.docker.com/r/staphb/snpeff) | <ul><li>5.1</li></ul> | https://pcingola.github.io/SnpEff |
| [Snippy](https://hub.docker.com/r/staphb/snippy) <br/> [![docker pulls](https://badgen.net/docker/pulls/staphb/snippy)](https://hub.docker.com/r/staphb/snippy) | <ul><li>4.4.5</li><li>4.5.1</li><li>4.6.0</li></ul> | https://github.com/tseemann/snippy |
| [snp-dists](https://hub.docker.com/r/staphb/snp-dists) <br/> [![docker pulls](https://badgen.net/docker/pulls/staphb/snp-dists)](https://hub.docker.com/r/staphb/snp-dists) | <ul><li>0.6.2</li><li>0.8.2</li></ul> | https://github.com/tseemann/snp-dists |
| [SNP-sites](https://hub.docker.com/r/staphb/snp-sites) <br/> [![docker pulls](https://badgen.net/docker/pulls/staphb/snp-sites)](https://hub.docker.com/r/staphb/snp-sites) | <ul><li>2.3.3</li><li>2.5.1</li></ul> | https://github.com/sanger-pathogens/snp-sites |
| [SNVPhyl-tools](https://hub.docker.com/r/staphb/snvphyl-tools) <br/> [![docker pulls](https://badgen.net/docker/pulls/staphb/snvphyl-tools)](https://hub.docker.com/r/staphb/snvphyl-tools) | <ul><li>1.8.2</li></ul> | https://github.com/phac-nml/snvphyl-tools |
| [SPAdes](https://hub.docker.com/r/staphb/spades/) <br/> [![docker pulls](https://badgen.net/docker/pulls/staphb/spades)](https://hub.docker.com/r/staphb/spades) | <ul><li>3.8.2</li><li>3.12.0</li><li>3.13.0</li><li>3.14.0</li><li>3.14.1</li><li>3.15.0</li><li>3.15.1</li><li>3.15.2</li><li>3.15.3</li><li>3.15.4</li><li>3.15.5</li></ul> | https://github.com/ablab/spades </br> http://cab.spbu.ru/software/spades/ |
| [SRA-toolkit](https://hub.docker.com/r/staphb/sratoolkit/) <br/> [![docker pulls](https://badgen.net/docker/pulls/staphb/sratoolkit)](https://hub.docker.com/r/staphb/sratoolkit) | <ul><li>2.9.2</li></ul> | https://github.com/ncbi/sra-tools |
| [SRST2](https://hub.docker.com/r/staphb/srst2/) <br/> [![docker pulls](https://badgen.net/docker/pulls/staphb/srst2)](https://hub.docker.com/r/staphb/srst2) | <ul><li>0.2.0</li><li>[0.2.0 + custom Vibrio cholerae database](srst2/0.2.0-vibrio-230224/README.md)</li></ul> | https://github.com/katholt/srst2 |
| [Staramr](https://hub.docker.com/r/staphb/staramr/) <br/> [![docker pulls](https://badgen.net/docker/pulls/staphb/staramr)](https://hub.docker.com/r/staphb/staramr) | <ul><li>0.5.1</li><li>0.7.1</li><li>0.8.0</li></ul> | https://github.com/phac-nml/staramr |
| [TBProfiler](https://hub.docker.com/r/staphb/tbprofiler/) <br/> [![docker pulls](https://badgen.net/docker/pulls/staphb/tbprofiler)](https://hub.docker.com/r/staphb/tbprofiler) | <ul><li>4.3.0</li><li>4.4.0</li><li>4.4.2</li></ul> | https://github.com/jodyphelan/TBProfiler |
| [TipToft](https://hub.docker.com/r/staphb/tiptoft/) <br/> [![docker pulls](https://badgen.net/docker/pulls/staphb/tiptoft)](https://hub.docker.com/r/staphb/tiptoft) | <ul><li>1.0.0</li><li>1.0.2</li></ul> | https://github.com/andrewjpage/tiptoft |
| [Tostadas](https://hub.docker.com/r/staphb/tostadas/) <br/> [![docker pulls](https://badgen.net/docker/pulls/staphb/tostadas)](https://hub.docker.com/r/staphb/tostadas) | <ul><li>0.2.0-beta</li></ul> | https://github.com/CDCgov/tostadas |
| [Treemmer](https://hub.docker.com/r/staphb/treemmer/) <br/> [![docker pulls](https://badgen.net/docker/pulls/staphb/treemmer)](https://hub.docker.com/r/staphb/treemmer) | <ul><li>0.3</li></ul> | https://git.scicore.unibas.ch/TBRU/Treemmer (archived, moved to GitHub) </br> https://github.com/fmenardo/Treemmer |
| [Trimmomatic](https://hub.docker.com/r/staphb/trimmomatic/) <br/> [![docker pulls](https://badgen.net/docker/pulls/staphb/trimmomatic)](https://hub.docker.com/r/staphb/trimmomatic) | <ul><li>0.38</li><li>0.39</li></ul> | http://www.usadellab.org/cms/?page=trimmomatic </br>https://github.com/usadellab/Trimmomatic |
| [Trycycler](https://hub.docker.com/r/staphb/trycycler/) <br/> [![docker pulls](https://badgen.net/docker/pulls/staphb/trycycler)](https://hub.docker.com/r/staphb/trycycler) | <ul><li>0.3.1</li><li>0.3.2</li><li>0.3.3</li><li>0.5.0</li><li>0.5.3</li><li>0.5.4</li></ul> | https://github.com/rrwick/Trycycler |
| [Unicycler](https://hub.docker.com/r/staphb/unicycler/) <br/> [![docker pulls](https://badgen.net/docker/pulls/staphb/unicycler)](https://hub.docker.com/r/staphb/unicycler) | <ul><li>0.4.7</li><li>0.4.8</li><li>0.4.9</li><li>0.5.0</li></ul> | https://github.com/rrwick/Unicycler |
| [VADR](https://hub.docker.com/r/staphb/vadr/) <br/> [![docker pulls](https://badgen.net/docker/pulls/staphb/vadr)](https://hub.docker.com/r/staphb/vadr) | <ul><li>1.1</li><li>1.1.2</li><li>1.1.3</li><li>1.2</li><li>1.2.1</li><li>1.3 & SARS-CoV-2 models 1.3-1</li><li>1.3 & SARS-CoV-2 models 1.3-2</li><li>1.4 & SARS-CoV-2 models 1.3-2</li><li>1.4.1 & SARS-CoV-2 models 1.3-2</li><li>1.4.2 & SARS-CoV-2 models 1.3-2, MPXV models 1.4.2-1</li><li>1.5 & SARS-CoV-2 models 1.3-2, MPXV models 1.4.2-1</li><li>1.5.1 & SARS-CoV-2 models 1.3-2, MPXV models 1.4.2-1, RSV models 1.5-2</li></ul> | https://github.com/nawrockie/vadr (archived, now redirects to ncbi/vadr) </br>https://github.com/ncbi/vadr |
| [VIBRANT](https://hub.docker.com/r/staphb/vibrant/) <br/> [![docker pulls](https://badgen.net/docker/pulls/staphb/vibrant)](https://hub.docker.com/r/staphb/vibrant) | <ul><li>1.2.1</li></ul> | https://github.com/AnantharamanLab/VIBRANT |
| [VIGOR4](https://hub.docker.com/r/staphb/vigor4/) <br/> [![docker pulls](https://badgen.net/docker/pulls/staphb/vigor4)](https://hub.docker.com/r/staphb/vigor4) | <ul><li>4.1.20190131</li></ul> | https://github.com/JCVenterInstitute/VIGOR4 |
| [VirSorter2](https://hub.docker.com/r/staphb/virsorter2/) <br/> [![docker pulls](https://badgen.net/docker/pulls/staphb/virsorter2)](https://hub.docker.com/r/staphb/virsorter2/) | <ul><li>2.1</li></ul> | https://github.com/jiarong/VirSorter2 |
| [VirulenceFinder](https://hub.docker.com/r/staphb/virulencefinder/) <br/> [![docker pulls](https://badgen.net/docker/pulls/staphb/virulencefinder)](https://hub.docker.com/r/staphb/virulencefinder/) | <ul><li>[2.1.4](virulencefinder/2.0.4/)</li></ul> | https://bitbucket.org/genomicepidemiology/virulencefinder/src/master/ <br/> https://bitbucket.org/genomicepidemiology/virulencefinder_db/src/master/ |
| [wtdbg2](https://hub.docker.com/r/staphb/wtdbg2/) <br/> [![docker pulls](https://badgen.net/docker/pulls/staphb/wtdbg2)](https://hub.docker.com/r/staphb/wtdbg2) | <ul><li>2.5</li></ul> | https://github.com/ruanjue/wtdbg2 |

You can also view the list of images on Docker hub here: https://hub.docker.com/r/staphb/

## License
  * [GNU GPLv3 license](/LICENSE) was added 2020-01-16
  * We keep a list of the licenses for the main software within the docker images here: [Program_Licenses.md](/Program_Licenses.md)
  * Links to licenses for each program should also be listed as a metadata `LABEL` within each dockerfile

## Authors/Maintainers
Each Dockerfile lists the author(s)/maintainer(s) as a metadata `LABEL`, but the authors/maintainers of the docker images are:
  * [@kapsakcj](https://github.com/kapsakcj)
  * [@k-florek](https://github.com/k-florek)
  * [@garfinjm](https://github.com/garfinjm)
  * [@kevinlibuit](https://github.com/kevinlibuit)
  * [@erinyoung](https://github.com/erinyoung)
  * [@lskatz](https://github.com/lskatz)
  * [@stjacqrm](https://github.com/stjacqrm)
  * [@AbigailShockey](https://github.com/AbigailShockey)
  * [@andersgs](https://github.com/andersgs)
  * [@logan-fink](https://github.com/logan-fink)
  * [@tgallagh](https://github.com/tgallagh)
  * [@koakeson](https://github.com/koakeson)
  * [@joacjo](https://github.com/joacjo)
  * [@rpetit3](https://github.com/rpetit3/)
  * [@jvhagey](https://github.com/jvhagey)
  * [@Antonia-Chalka](https://github.com/Antonia-Chalka)
  * [@MillironX](https://github.com/MillironX)
  * [@frankambrosio3](https://github.com/frankambrosio3)
  * [@HNHalstead](https://github.com/HNHalstead)
  * [@fanninpm](https://github.com/fanninpm)
  * [@SarahNadeau](https://github.com/SarahNadeau)
  * [@DOH-HNH0303](https://github.com/DOH-HNH0303)
  * [@jwarnn](https://github.com/jwarnn)
  * [@stitam](https://github.com/stitam)
  * [@tiverson](https://github.com/tives82)
  * [@sbthandras](https://github.com/sbthandras)
  * [@idolawoye](https://github.com/idolawoye)
  * [@michellescribner](https://github.com/michellescribner)
  * [@cjjossart](https://github.com/cjjossart)
  * [@jlumpe](https://github.com/jlumpe)
  * [@kissake](https://github.com/kissake)
  * [@sage-wright](https://github.com/sage-wright)
  * [@ankushkgupta](https://github.com/ankushkgupta)
  * [@kyleoconnell](https://github.com/kyleoconnell)
  * [@CTindall-1](https://github.com/CTindall-1)
  * [@hkunerth](https://github.com/hkunerth)
  * [@eetueklund](https://github.com/eetueklund)
  * [@wchen190](https://github.com/wchen190)
  * [@shelby-bennett](https://github.com/shelby-bennett)
  * [@cjalcorta](https://github.com/cjalcorta)
  * [@mndoucette](https://github.com/mndoucette)
  * [@jcw349](https://github.com/jcw349)
  * [@poojasgupta](https://github.com/poojasgupta)
  * [@hollygene](https://github.com/hollygene)
  * [@HarryHung](https://github.com/HarryHung)
  * [@sam-baird](https://github.com/sam-baird)
  * [@eproctor118](https://github.com/eproctor118)
  * [@cimendes](https://github.com/cimendes)
  * [@golden75](https://github.com/golden75)
  * [@Kincekara](https://github.com/Kincekara)
  * [@Haikelnb](https://github.com/Haikelnb)
  * [@kprus](https://github.com/kprus)
  * [@laura-bankers](https://github.com/laura-bankers)

