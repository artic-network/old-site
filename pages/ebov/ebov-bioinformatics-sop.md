---
title: "Ebola virus Nanopore sequencing bioinformatics protocol | amplicon, native barcoding"
keywords: protocol
layout: document
last_updated: Dec 12, 2019
tags: [protocol]
summary:
permalink: ebov/ebov-bioinformatics-sop.html
folder: ebov
title_text: "Ebola virus bioinformatics protocol"
subtitle_text: "Nanopore | bioinformatics"
document_name: "ARTIC-EBOV-bioinformaticsSOP"
version: v1.0.1
creation_date: 2018-05-26
revision_date: 
forked_from: 
author: Nick Loman
citation: "Loman *et al.* In Prep."
---

{% include callout.html
type='default'
content='**Overview:** A complete bioinformatics protocol to take the output from the [sequencing protocol](/ebov/ebov-seq-sop.html) to consensus genome sequences. Includes basecalling, de-multiplexing, mapping, polishing and consensus generation.
'
%}

<br />

This document is part of the Ebola virus Nanopore sequencing protocol package:
: [http://artic.network/ebov/](http://artic.network/ebov/)

#### Related documents:

Ebola virus Nanopore sequencing protocol:
: [http://artic.network/ebov/ebov-seq-sop.html](/ebov/ebov-seq-sop.html)

Setting up the laptop computing environment using Conda:
: [http://artic.network/ebov/ebov-it-setup.html](http://artic.network/ebov/ebov-it-setup.html)

Phylogenetic analysis and visualization:
: [http://artic.network/ebov/ebov-phylogenetics-sop.html](http://artic.network/ebov/ebov-phylogenetics-sop.html)


<br /><br /><br />

{% include wellcome-trust.html %}

<div class="pagebreak"> </div>

## Preparation

Set up the computing environment as described here in this document: [ebov-it-setup](ebov-it-setup.html). This should be done and tested prior to sequencing, particularly if this will be done in an environment without internet access or where this is slow or unreliable. Once this is done, the bioinformatics can be performed largely off-line. 

## Nanopore Bioinformatics

Activate the ARTIC environment:

```bash
source activate artic-ebov
```

### Basecalling with MinKNOW (local basecalling)

When setting up the run, turn Basecalling on.

### Basecalling with Guppy

If you did basecalling with MinKNOW, skip this step.

Run the Guppy basecaller on the new MinION run folder:

For fast mode basecalling:

```bash
guppy_basecaller -c dna_r9.4.1_450bps_fast.cfg -i /path/to/reads -s run_name -x auto -r
```

For high-accuracy mode basecalling:

```bash
guppy_basecaller -c dna_r9.4.1_450bps_hac.cfg -i /path/to/reads -s run_name -x auto -r
```

You need to substitute `/path/to/reads` to the folder where the FAST5 files from your
run are. Common locations are:

   - Mac: ```/Library/MinKNOW/data/run_name```
   - Linux: ```/var/lib/MinKNOW/data/run_name```
   - Windows ```c:/data/reads```
   
This will create a folder called `run_name` with the base-called reads in it.

### Consensus sequence generation

We first collect all the FASTQ files (typically stored in files each containing 4000 reads)
into a single file.

```bash
artic gather --min-length 400 --max-length 700 --prefix run_name /path/to/reads
```

Here `/path_to_readss` should be the folder in which MinKNOW put the base-called reads (i.e., `run_name` from the command above).

We use a length filter here of between 400 and 700 to remove obviously chimeric reads.

You may need to change these numbers if you are using different length primer schemes. Try the minimum lengths of the amplicons as the 
minimum, and the maximum length of the amplicons plus 200 as the maximum.

I.e. if your amplicons are 300 base pairs, use --min-length 300 --max-length 500

You will now have a file called: ``run_name_pass.fastq``
and a file called ``run_name_sequencing_summary.txt``, 
as well as individual files for each barcode (if previously demultiplexed).

### Demultiplex with Porechop with stringent settings

This stage is obligatory, even if you have already demultiplexed with Albacore, due to
significant barcoding misassignments that can confound results:

```bash
artic demultiplex --threads 4 run_name_pass.fastq
```

Now you will have new files called:

```bash
run_name_pass_BC01.fastq
run_name_pass_BC02.fastq
run_name_pass_BC03.fastq
```

### Create the nanopolish index (once per sequencing run, not per sample)

If you used MinKNOW basecalling, use the following command:

```bash
nanopolish index -d /path/to/reads run_name_pass.fastq
```

If you manually performed Guppy basecalling (but not if you used MinKNOW basecalling), this command can be made a little faster by using:

```bash
nanopolish index -s run_name_sequencing_summary.txt -d /path/to/reads run_name_pass.fastq
```

Again, alter ``/path/to/reads`` to point to the original location of the FAST5 files.

## Run the MinION pipeline

For each barcode you wish to process:

```bash
artic minion --normalise 200 --threads 4 --scheme-directory ~/artic/artic-ebov/primer-schemes --read-file run_name_final_NB01.fastq --nanopolish-read-file run_name_pass.fastq ZaireEbola/V2 samplename
```

Replace ``samplename`` as appropriate:

## Output files

   * ``samplename.primertrimmed.bam`` - BAM file for visualisation after primer-binding site trimming
   * ``samplename.vcf`` - detected variants in VCF format
   * ``samplename.variants.tab`` - detected variants
   * ``samplename.consensus.fasta`` - consensus sequence

