## QIIME workflow for metabarcoding analysis (18S/16S rRNA)

Here, we will utilize a pipeline called QIIME (v1.9.1) to analyze and visualize microbial diversity using raw DNA sequences.

[Link to the main QIIME website](http://qiime.org) (for more tutorials and detailed documentation of each script).

---

## Pipeline Overview

Here is an overview of the general steps of the QIIME pipeline that we will carry out during the BITMaB workshop (click links to jump to detailed instructions for each step):

#### [Step 1](https://github.com/BikLab/BITMaB-workshop/blob/master/QIIME-metabarcoding-tutorial.md#step-1---demultiplex-the-raw-reads): Demultiplex the raw reads (example workflow for workshop)

#### [Step 2](https://github.com/BikLab/BITMaB-workshop/blob/master/QIIME-metabarcoding-tutorial.md#step-2---pick-operational-taxonomic-units): Pick Operational Taxonomic Units

<<<<<<< HEAD
#### [Step 3](https://github.com/BikLab/BITMaB-workshop/blob/master/Qiime4GOMRI.md#step-3---identify-amd-remove-chimeric-sequences): Identify chimeras and remove chimeric sequences from the OTU table
=======
#### [Step 3](https://github.com/BikLab/BITMaB-workshop/blob/master/QIIME-metabarcoding-tutorial.md#step-3---identify-amd-remove-chimeric-sequences): Identify chimeras and remove these sequences from the OTU table
>>>>>>> origin/master

#### [Step 4](https://github.com/BikLab/BITMaB-workshop/blob/master/QIIME-metabarcoding-tutorial.md#step-4---remove-pynast-failures-from-the-biom-table): Align sequences and remove alignment failures from the OTU table

#### [Step 5](https://github.com/BikLab/BITMaB-workshop/blob/master/QIIME-metabarcoding-tutorial.md#step-5----filter-fasta-file-of-aligned-rep-set-sequences-to-only-keep-otus-in-filtered-biom-file): Filter rep set fasta file to match the OTU IDs in your filtered OTU table 

#### [Step 6](https://github.com/BikLab/BITMaB-workshop/blob/master/QIIME-metabarcoding-tutorial.md#step-6---make-new-phylogeny-with-final-set-of-otus-no-chimeras-no-alignment-failures): Construct a phylogenetic tree

#### [Step 7](https://github.com/BikLab/BITMaB-workshop/blob/master/QIIME-metabarcoding-tutorial.md#step-7---run-diversity-analysis): Carry out microbial community analyses to assess alpha- and beta-diversity 

---

### To view the help menu for any QIIME script, run:

```
<script-name> -h
```
OR

```
<script-name> --help
```

Web documentation of "help" dialogues are also [available on the QIIME website](http://qiime.org/scripts/)

---

<<<<<<< HEAD
### Step 1 - Demultiplex the raw reads (example workflow for workshop)
=======
## Step 1 - Demultiplex the raw reads
>>>>>>> origin/master

Before running QIIME on your own data, you would need to quality filter, trim, and demultiplex your raw sequence reads. Typically, this is done using the following commands:

#### 1a. Join the paired end reads

```
join_paired_ends.py \
	-f R1_1.fastq.gz \
	-r R2_1.fastq.gz \
	-b I1_1.fastq.gz \
	-o <output-dir-name> \
	-j 10 -p 15
```
Your read 1 and read 2 Illumina Paire-End files would be `R1_1.fastq.gz` and `R2_1.fastq.gz`, respectively, and `I1_1.fastq.gz` is your index file containing the barcoded nucleotides which match to samples.
	
`-j 10` sets minimum overlap to 10bp

`-p 15` allows for a 15% error rate in the overlapping area

`-o <output-dir-name>` can be whatever directory name you choose

#### 1b. Demultiplex the joined reads

```
split_libraries_fastq.py \
	-i fastqjoin.join.fastq \
	-b fastqjoin.join_barcodes.fastq \
	-m <qiime-mapping-file> \
	-o <output-dir-name> \
	--rev_comp_mapping_barcodes -q 19 -r 5 -p 0.70
```

`--rev_comp_mapping_barcodes` looks for the reverse compliment of the barcodes in the mapping file

`-q 19` minimum quality score of 20

`-r 5` allows 5 poor quality bases before read truncation

`-p 0.70` minimum fraction of consecutive high quality base calls to include a read

Your quality-filtering parameters may change based on your data type and preferences (e.g. if you want stringent vs. relaxed filtering)

#### 1c. Truncate the reverse primer

```
truncate_reverse_primer.py -f seqs.fna \
	-m <qiime-mapping-file> \
	-o <output-dir-name>
```

This last step removes the flanking primer sequences from your reads (the primer you used to generate your amplicons in the lab). These primer sequences are contained in your QIIME mapping file.

For the purpose of the workshop, we have already completed the above three commands for you.

---

## Step 2 - Pick Operational Taxonomic Units (OTUs)


"Picking" Operational Taxonomic Units (abbreviated as **OTUs**) is a standard method for clustering raw Illumina reads into clusters of sequences. In theory each OTU is the molecular equivalent of a morphological "species" (but in practice the OTU picking approach is arbitrary and not a perfect equivalent - often you will recover many more OTUs than known biological species).

QIIME offers several options for picking OTUs - the two most common are `reference-based OTU picking` and `open-reference OTU picking`

> ### Why would choose use one type of OTU picking over the other?

In this workshop we will be using open-reference OTU picking - [described here in this QIIME tutorial]{http://qiime.org/tutorials/open_reference_illumina_processing.html). The method is also peer-reviewed and published in [Rideout et. al 2014, PeerJ](https://peerj.com/articles/545/) (open access publication)

We will start by picking OTUs using our fasta file that contains quality-filtered Illumina reads from each sample. 

#### 2a. Copy data files and reference database files over to your home directory

File paths for workshop files are located on the main README.md workshop page.

#### 2b. Picking OTUs using the open reference strategy

We pick OTUs using `workflow scripts` in QIIME. These wrap many scripts under one umbrella command - so to modify some parameters, we need to use a parameter file.

> ### Peek in the QIIME parameter file on the command line. What do you see? What parameters are we modifying?

We'll start by running the following command:

```
pick_open_reference_otus.py \
	-i <input-fasta-seqs> \
	-r <datbase-reference-seqs> \
	-o <output-directory> \
	-p <parameters-file> \
	-s 0.10 \
	--prefilter_percent_id 0.0 \
	--suppress_align_and_tree
```

Choose any name for your output directory - it's usually a good idea to make this descriptive so you can remember what type of analysis you did, and when you ran it. Something like: `-o uclust-99pct-18Seuk-11Jan17`

> ### Once the OTU picking script is finished, what files do you see in your output directory? 

> ### Peek into the OTU picking logfile. How many different commands were run using this workflow script?

 
#### 2c. Assign taxonomy

```
assign_taxonomy.py \
	-i <input.rep.set.fna> \
	-r Silva_119_rep_set99_18S.fna \
	-t taxonomy_99_7_levels_consensus.txt \
	-o <output.directory.name> \
	-m rdp \
	--rdp_max_memory 60000
```

---

## Step 3 - Identify chimeras and remove chimeric sequences from the OTU table

#### 3a. identify chimeras
```
identify_chimeric_seqs.py \
	-i <rep.set.fna> \
	-m usearch61 \
	-o <output.directory.name> \
	-r Silva_119_rep_set99_18S.fna
```
#### 3b. Remove chimeras from the BIOM table

```
filter_otus_from_otu_table.py \
	-i <table.biom> \
	-o <out.table.biom> \
	-e <chimeras.txt>
```

---

## Step 4 -  Align sequences and remove alignment failures from the OTU table

#### 4a. Align rep_set.fna against a pre-aligned reference database. In our case, we are using the Silva119 database. 
```
align_seqs.py \
	-i <rep.set.fna> \
	-o <output.directory.name> \
	-t Silva_119_rep_set99_aligned_18S_only.fna \
	--alignment_method pynast \
	--pairwise_alignment_method uclust \
	--min_percent_id 70.0
```


#### 4b. Remove gaps from the aligned rep sets which is important for constructing a phylogeny. If using the Greengenes database, this step is highly recommended. 
```

filter_alignment.py \
	-i <rep.set.aligned.fna> \
	-o <output.directory.name> \
	--suppress_lane_mask_filter
```

#### 4c. Add metadata to the BIOM table. This will be helpful for viewing the diversity results.
```
biom add-metadata \
	-i <table.biom> \
	-o <out.table.biom> \
	-m <mapping.file.txt>
```

#### 4b. Summarize the BIOM table to identify the minimum number of sequences per sample and store the output in a text file. The minimum number of sequences per sample should be used for the `-e` in Step 3h
```
biom summarize-table \
	-i <table.biom> \
	-o <out.table.txt>
```

---

## Step 5 - Filter rep set fasta file to match the OTU IDs in your filtered OTU table 

```
filter_fasta.py \
	-f <rep.set.aligned.filtered.fna> \
	-o <output.fna> \
	-b <table.biom>
```
---

## Step 6 - Make new phylogeny with final set of OTUS (no chimeras, no alignment failures)
```
make_phylogeny.py \
	-i <input.fna> \
	-o <output.tre> \
	--tree_method fasttree
```

---

## Step 7 - Run diversity analysis
#### 7a. Run beta diversity analysis
```
beta_diversity_through_plots.py \
	-i <table.biom> \
	-m <mapping.file.txt> \
	-o <output.directory.name> \
	-t <input.tre> \
	-e <count.per.sample>
```
#### 7b. Run alpha diversity analysis

```
alpha_diversity.py \
	-i  <table.biom> \
	-o <output.txt> \
	-t <input.tre> 
```
```
alpha_rarefaction.py \
	-i  <table.biom> \
	-m <mapping.file.txt> \
	-o <output.directory.name> \
	-t <input.tre> \
	-e <count.per.sample>
```