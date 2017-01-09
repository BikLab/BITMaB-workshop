## This file contains a basic set of Qiime commands for calculating diversity metrics.

If you have any questions about the options in the following commands, please run the name of the script with the help command as shown below.

### To view the help menu for `pick_open_reference_otus.py`, run:

```
pick_open_reference_otus.py -h

```
### Step 1 - Demultiplex the raw reads

This step is already done for you. Please proceed to Step 2.

### Step 2 - Pick OTUs 
We will start by picking Operational Taxonomic Units (OTUs) in our fasta file that contains reads from each sample. 

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

### Step 3 - Identify amd remove chimeric sequences 

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

### Step 4 - Remove pynast failures from the BIOM table
#### 4a. Remove chimeras from the BIOM table

```
filter_otus_from_otu_table.py \
	-i /home/gomre/taruna/200k-GOM-Illumina-subsample/analysis-results/18Seuks_rdp_99pct_Jan2017/otu_table_mc2_w_tax_metadata.biom \
	-o /home/gomre/taruna/200k-GOM-Illumina-subsample/analysis-results/18Seuks_rdp_99pct_Jan2017/usearch_checked_chimeras/otu_table_mc2_w_tax_metadata_NOpynastfail.biom \
	-e /home/gomre/taruna/200k-GOM-Illumina-subsample/analysis-results/18Seuks_rdp_99pct_Jan2017/pynast_aligned_seqs/rep_set_failures.fasta
```

#### 4b. Summarize the BIOM table to identify the minimum number of sequences per sample and store the output in a text file. The minimum number of sequences per sample should be used for the `-e` in Step 3h
```
biom summarize-table \
	-i /home/gomre/taruna/200k-GOM-Illumina-subsample/analysis-results/18Seuks_rdp_99pct_Jan2017/usearch_checked_chimeras/otu_table_mc2_w_tax_metadata_NOpynastfail_NOchimeras.biom \
	-o /home/gomre/taruna/200k-GOM-Illumina-subsample/analysis-results/18Seuks_rdp_99pct_Jan2017/usearch_checked_chimeras/biom_summary_filt_NOpf_NOc.txt
```

### Step 5 -  Filter fasta file of aligned rep set sequences to only keep OTUs in filtered BIOM file
```
filter_fasta.py \
	-f /home/gomre/taruna/200k-GOM-Illumina-subsample/analysis-results/18Seuks_rdp_99pct_Jan2017/pynast_aligned_seqs/rep_set_aligned_pfiltered.fasta \
	-o /home/gomre/taruna/200k-GOM-Illumina-subsample/analysis-results/18Seuks_rdp_99pct_Jan2017/usearch_checked_chimeras/pfiltered_align_BIOMfilt.fasta \
	-b /home/gomre/taruna/200k-GOM-Illumina-subsample/analysis-results/18Seuks_rdp_99pct_Jan2017/usearch_checked_chimeras/otu_table_mc2_w_tax_metadata_NOpynastfail_NOchimeras.biom
```

### Step 6 - Make new phylogeny with final set of OTUS (no chimeras, no alignment failures)
```
make_phylogeny.py \
	-i /home/gomre/taruna/200k-GOM-Illumina-subsample/analysis-results/18Seuks_rdp_99pct_Jan2017/usearch_checked_chimeras/pfiltered_align_BIOMfilt.fasta \
	-o /home/gomre/taruna/200k-GOM-Illumina-subsample/analysis-results/18Seuks_rdp_99pct_Jan2017/usearch_checked_chimeras/pfiltered_align_BIOMfilt.tre \
	--tree_method fasttree
```

### Step 7 - Run diversity analysis
#### 7a. Run beta diversity analysis
```
beta_diversity_through_plots.py \
	-i /data/gomre/taruna/200k-GOM-Illumina-subsample/analysis-results/18Seuks_rdp_99pct_Jan2017/usearch_checked_chimeras/otu_table_mc2_w_tax_metadata_NOpynastfail_NOchimeras.biom \
	-m /home/gomre/taruna/200k-GOM-Illumina-subsample/qiime-files/mapping-files/QIIMEmappingfile_GOM_Illumina_7Apr14_F04.txt \
	-o /data/gomre/taruna/200k-GOM-Illumina-subsample/analysis-results/18Seuks_rdp_99pct_Jan2017/beta_div_thru_plots_200k_NOpf_NOc \
	-t /data/gomre/taruna/200k-GOM-Illumina-subsample/analysis-results/18Seuks_rdp_99pct_Jan2017/usearch_checked_chimeras/pfiltered_align_BIOMfilt.tre \
	-e 15080
```
#### 7a. Run alpha diversity analysis

```
alpha_diversity.py \
	-i  /data/gomre/taruna/200k-GOM-Illumina-subsample/analysis-results/18Seuks_rdp_99pct_Jan2017/usearch_checked_chimeras/otu_table_mc2_w_tax_metadata_NOpynastfail_NOchimeras.biom \
	-o /data/gomre/taruna/200k-GOM-Illumina-subsample/analysis-results/18Seuks_rdp_99pct_Jan2017/alpha_div_200k_NOpf_NOc \
	-t /data/gomre/taruna/200k-GOM-Illumina-subsample/analysis-results/18Seuks_rdp_99pct_Jan2017/usearch_checked_chimeras/pfiltered_align_BIOMfilt.tre 
```
```
alpha_rarefaction.py \
	-i  /data/gomre/taruna/200k-GOM-Illumina-subsample/analysis-results/18Seuks_rdp_99pct_Jan2017/usearch_checked_chimeras/otu_table_mc2_w_tax_metadata_NOpynastfail_NOchimeras.biom \
	-m /home/gomre/taruna/200k-GOM-Illumina-subsample/qiime-files/mapping-files/QIIMEmappingfile_GOM_Illumina_7Apr14_F04.txt \
	-o /data/gomre/taruna/200k-GOM-Illumina-subsample/analysis-results/18Seuks_rdp_99pct_Jan2017/alpha_rare_200k_NOpf_NOc \
	-t /data/gomre/taruna/200k-GOM-Illumina-subsample/analysis-results/18Seuks_rdp_99pct_Jan2017/usearch_checked_chimeras/pfiltered_align_BIOMfilt.tre \
	-e 3412
```