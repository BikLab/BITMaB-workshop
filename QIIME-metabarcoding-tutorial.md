## QIIME workflow for metabarcoding analysis (18S/16S rRNA)

Here, we will utilize a pipeline called QIIME (v1.9.1) to analyze and visualize microbial diversity using raw DNA sequences.

[Link to the main QIIME website](http://qiime.org) (for more tutorials and detailed documentation of each script).

### To view the help menu for any QIIME script, run:

```
<script-name> -h
```
OR

```
<script-name> --help
```

Web documentation of "help" dialogues are also [available on the QIIME website](http://qiime.org/scripts/)

***

### Step 1 - Demultiplex the raw reads

Before running QIIME on your own data, you would need to quality filter, trim, and demultiplex your raw sequence reads. Typically, this is done using the following commands:

```

```

For the purpose of the workshop, we have already completed this step for you. Please proceed to Step 2.

***

## Step 2 - Pick Operational Taxonomic Units 

"Picking" Operational Taxonomic Units (abbreviated as **OTUs**) is a standard method for clustering raw Illumina reads into clusters of sequences. In theory each OTU is the molecular equivalent of a morphological "species" (but in practice the OTU picking approach is arbitrary and not a perfect equivalent - often you will recover many more OTUs than known biological species).

QIIME offers several options for picking OTUs - the two most common are `reference-based OTU picking` and `open-reference OTU picking`

> Why would choose use one type of OTU picking over the other?

In this workshop we will be using open-reference OTU picking - [described here in this QIIME tutorial]{http://qiime.org/tutorials/open_reference_illumina_processing.html). The method is also peer-reviewed and published in [Rideout et. al 2014, PeerJ](https://peerj.com/articles/545/) (open access publication)

We will start by picking OTUs using our fasta file that contains quality-filtered Illumina reads from each sample. 

#### 2a. Picking OTUs using the open reference strategy
```
pick_open_reference_otus.py \
	-i <input.fna> \
	-r Silva_119_rep_set99_18S.fna \
	-o <output.directory.name> \
	-p <parameters.txt> \
	-s 0.10 \
	--prefilter_percent_id 0.0 \
	--suppress_align_and_tree
```
 
#### 2b. Assign taxonomy

```
assign_taxonomy.py \
	-i <input.rep.set.fna> \
	-r Silva_119_rep_set99_18S.fna \
	-t taxonomy_99_7_levels_consensus.txt \
	-o <output.directory.name> \
	-m rdp \
	--rdp_max_memory 60000
```


#### 2c. Align rep_set.fna against a pre-aligned reference database. In our case, we are using the Silva119 database. 
```
align_seqs.py \
	-i <rep.set.fna> \
	-o <output.directory.name> \
	-t Silva_119_rep_set99_aligned_18S_only.fna \
	--alignment_method pynast \
	--pairwise_alignment_method uclust \
	--min_percent_id 70.0
```


#### 2d. Remove gaps from the aligned rep sets which is important for constructing a phylogeny. If using the Greengenes database, this step is highly recommended. 
```

filter_alignment.py \
	-i <rep.set.aligned.fna> \
	-o <output.directory.name> \
	--suppress_lane_mask_filter
```

#### 2e. Add metadata to the BIOM table. This will be helpful for viewing the diversity results.
```
biom add-metadata \
	-i <table.biom> \
	-o <out.table.biom> \
	-m <mapping.file.txt>
```

***

## Step 3 - Identify amd remove chimeric sequences 

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

***

## Step 4 - Remove pynast failures from the BIOM table
#### 4a. Remove chimeras from the BIOM table

```
filter_otus_from_otu_table.py \
	-i <table.biom> \
	-o <out.table.biom> \
	-e <rep.set.failures.fna>
```

#### 4b. Summarize the BIOM table to identify the minimum number of sequences per sample and store the output in a text file. The minimum number of sequences per sample should be used for the `-e` in Step 3h
```
biom summarize-table \
	-i <table.biom> \
	-o <out.table.txt>
```

***

## Step 5 -  Filter fasta file of aligned rep set sequences to only keep OTUs in filtered BIOM file
```
filter_fasta.py \
	-f <rep.set.aligned.filtered.fna> \
	-o <output.fna> \
	-b <table.biom>
```
***

## Step 6 - Make new phylogeny with final set of OTUS (no chimeras, no alignment failures)
```
make_phylogeny.py \
	-i <input.fna> \
	-o <output.tre> \
	--tree_method fasttree
```

***

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