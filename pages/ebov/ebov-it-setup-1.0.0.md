---
title: "Ebola virus Nanopore sequencing protocol | amplicon, native barcoding"
keywords: protocol
layout: document
last_updated: May 18, 2018
tags: [protocol] 
permalink: ebov/ebov-it-setup.html
folder: ebov
title_text: "Ebola virus bioinformatics protocol"
subtitle_text: "Nanopore | bioinformatics"
document_name: "ARTIC-EBOV-bioinformaticsSOP"
version: v1.0.0
creation_date: 2018-05-26
forked_from: 
author: Nick Loman, Andrew Rambaut
citation: "Loman *et al.* In Prep."
---

{% include callout.html
type='default'
content='**Overview:** Instructions for setting up the laptop computing environment for running the MinION sequencer and performing the bioinformatics and downstream phylogenetic analyses.'
%}

<br />

This document is part of the Ebola virus Nanopore sequencing protocol package:
: [http://artic.network/ebov/](http://artic.network/ebov/)

<br /><br /><br />

{% include wellcome-trust.html %}

<div class="pagebreak"> </div>

## Preparation

#### Equipment required:

Laptop requirements for MinION:
: Intel i7 or Xeon processor
: 16GB RAM
: 1TB SSD hard drive
: USB 3
: Full Oxford Nanopore Technologies [lab and computing requirements are here](https://nanoporetech.com/sites/default/files/s3/MinION-Computer-Requirements-March-17_Final.pdf).

## Software Setup

These protocols instructions assume a 64-bit UNIX, Linux or similar environment. This could be Mac OS X (Yosemite or later), Linux (e.g., Ubuntu 16 or later), or Windows 10  Subsystem for Linux. It assumes familiarity with a UNIX-like *bash* command-line. 

The steps in this document should be done and tested prior to sequencing, particularly if this will be done in an environment without internet access or where this is slow or unreliable. Once this is done, the bioinformatics and phylogenetics protocols can be performed largely off-line. 

### Conda

Software will be installed using [Conda](https://conda.io/) -- a cross-platform package and dependency installer.
 
For Conda installation instructions for your operating system go to: [https://conda.io/docs/user-guide/install/](https://conda.io/docs/user-guide/install/). We suggest installing the `Miniconda` version which is relatively small and quick to install.

First use the following commands to set up access to [BioConda](https://bioconda.github.io) (a repository of over 3000 bioinformatics packages):

```
conda config --add channels conda-forge
conda config --add channels bioconda
```

Create an custom environment for running software and install the packages:
  
```
conda create -n artic
```

Although not strictly necessary this will prevent any conflicts with other similar software installed and can be readily removed. You can use this command to activate the environment: 

```
source activate artic
```

and then deactivate it again using this:

```
source deactivate
```

### Installing software

Activate the ARTIC environment:

```
source activate artic
```

Install the software packages required (the packages needed are listed in the individual protocols documents):

```
conda install -y [list of software packages]  
```

### Installing Oxford Nanopore MinKNOW and Albacore

The software for running the MinION and basecalling can be downloaded from the [Oxford Nanopore Technologies Community site](https://community.nanoporetech.com). Log in to find the `Software Downloads` button. Download `MinKNOW 2.0` (currently v18.03.1) and `Albacore` (currently v2.3.1) appropriate to the system being used. Follow the `Installation guide` for each package.

MinKNOW is a graphical user-interface programme that will be installed in the application area of the system.

Python 3 is needed to both install and run Albacore but is installed by Conda if not already on your machine. Access to Albacore is through an excutable Python script called `read_fast5_basecaller.py` that will be installed in a directory on the path such as `/usr/loca/bin/`.
