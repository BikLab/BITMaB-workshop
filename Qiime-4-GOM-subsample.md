## This file contains a basic set of Qiime commands for calculating alpha and beta diversity metrics.

If you have any questions about the options in the following commands, please run the name of the script with the help command as shown below.

#### To view the help menu for `pick_open_reference_otus.py`, run:

```
pick_open_reference_otus.py -h

```
### Part A: Assessing diversity within data without filtering 
#### Step 1 - OTU picking
We will start by picking Operational Taxonomic Units (OTUs) in our fasta file that contains reads from each sample. 


```
pick_open_reference_otus.py \
	-i /home/gomre/taruna/50k-GOM-Illumina-subsample/data-clean/GOM_concat1.7_subsample50K_10Jan14.fna \
	-r /home/gomre/taruna/50k-GOM-Illumina-subsample/ref-dbs/Silva119_release/rep_set_eukaryotes/99/Silva_119_rep_set99_18S.fna \
	-o /home/gomre/taruna/50k-GOM-Illumina-subsample/analysis-results/18Seuks_rdp_99pct_Jan2017 \
	-p /home/gomre/taruna/GOM-Illumina/qiime-files/parameters/18S_openref99_rdp_silva119.txt \
	-s 0.10 \
	--prefilter_percent_id 0.0 \
	--suppress_align_and_tree
```
#### Step 2 - Assigning taxonomy 

```
assign_taxonomy.py \
	-i /home/gomre/taruna/50k-GOM-Illumina-subsample/analysis-results/18Seuks_rdp_99pct_Jan2017/rep_set.fna \
	-r /home/gomre/taruna/50k-GOM-Illumina-subsample/ref-dbs/Silva119_release/rep_set_eukaryotes/99/Silva_119_rep_set99_18S.fna \
	-t /home/gomre/taruna/50k-GOM-Illumina-subsample/ref-dbs/Silva119_release/consensus_majority_taxonomy/consensus_taxonomy_eukaryotes/99/taxonomy_99_7_levels_consensus.txt \
	-o /home/gomre/taruna/50k-GOM-Illumina-subsample/analysis-results/18Seuks_rdp_99pct_Jan2017/rdp_assigned_taxonomy \
	-m rdp \
	--rdp_max_memory 60000
```


#### Step 3 - Aligning rep_set.fna against a pre-aligned reference database. In our case, we are using the Silva119 database. 
```
align_seqs.py \
	-i /data/gomre/taruna/50k-GOM-Illumina-subsample/analysis-results/18Seuks_rdp_99pct_Jan2017/rep_set.fna \
	-o /data/gomre/taruna/50k-GOM-Illumina-subsample/analysis-results/18Seuks_rdp_99pct_Jan2017/pynast_aligned_seqs \
	-t /data/gomre/taruna/50k-GOM-Illumina-subsample/ref-dbs/Silva119_release/aligned_rep_files/99_18S_only/Silva_119_rep_set99_aligned_18S_only.fna \
	--alignment_method pynast \
	--pairwise_alignment_method uclust \
	--min_percent_id 70.0
```


#### Step 4 - Remove gaps from the aligned rep sets which is important for constructing a phylogeny. If using the Greengenes database, this step is highly recommended. 
```

filter_alignment.py \
	-i /data/gomre/taruna/50k-GOM-Illumina-subsample/analysis-results/18Seuks_rdp_99pct_Jan2017/pynast_aligned_seqs/rep_set_aligned.fasta \
	-o /data/gomre/taruna/50k-GOM-Illumina-subsample/analysis-results/18Seuks_rdp_99pct_Jan2017/pynast_aligned_seqs \
	--suppress_lane_mask_filter
```

#### Step 5 - Add metadata to the BIOM table. This will be helpful for viewing the diversity results.
```
biom add-metadata \
	-i /data/gomre/taruna/50k-GOM-Illumina-subsample/analysis-results/18Seuks_rdp_99pct_Jan2017/otu_table_mc2_w_tax.biom \
	-o /data/gomre/taruna/50k-GOM-Illumina-subsample/analysis-results/18Seuks_rdp_99pct_Jan2017/otu_table_mc2_w_tax_metadata.biom \
	-m /home/gomre/taruna/GOM-Illumina/qiime-files/mapping-files/QIIMEmappingfile_GOM_Illumina_7Apr14_F04.txt
```

#### Step 6 - Summarize the BIOM table to identify the minimum number of sequences per sample. This value should be used for the `-e` in Step 8  

```
biom summarize-table \
	-i /data/gomre/taruna/50k-GOM-Illumina-subsample/analysis-results/18Seuks_rdp_99pct_Jan2017/otu_table_mc2_w_tax_metadata.biom
```

#### Step 7 - Build a phylogeny
```
make_phylogeny.py \
	-i /data/gomre/taruna/50k-GOM-Illumina-subsample/analysis-results/18Seuks_rdp_99pct_Jan2017/pynast_aligned_seqs/rep_set_aligned_pfiltered.fasta \
	-o /data/gomre/taruna/50k-GOM-Illumina-subsample/analysis-results/18Seuks_rdp_99pct_Jan2017/pynast_aligned_seqs/rep_set.tre \
	--tree_method fasttree
```
#### Step 8 - Assess beta diversity among samples

```
beta_diversity_through_plots.py \
	-i /data/gomre/taruna/50k-GOM-Illumina-subsample/analysis-results/18Seuks_rdp_99pct_Jan2017/otu_table_mc2_w_tax_metadata.biom \
	-m /home/gomre/taruna/GOM-Illumina/qiime-files/mapping-files/QIIMEmappingfile_GOM_Illumina_7Apr14_F04.txt \
	-o /data/gomre/taruna/50k-GOM-Illumina-subsample/analysis-results/18Seuks_rdp_99pct_Jan2017/beta_div_thru_plots \
	-t /data/gomre/taruna/50k-GOM-Illumina-subsample/analysis-results/18Seuks_rdp_99pct_Jan2017/pynast_aligned_seqs/rep_set.tre \
	-e 3479
```
### Part B: Assessing diversity within data with filtering. In this section, we will remove the sequences that are chimeras and/or did not align against the reference database in Step 3.

#### Step 9 - Identify chimeric sequences in the original `rep_set.fna`
```
identify_chimeric_seqs.py \
	-i /data/gomre/taruna/50k-GOM-Illumina-subsample/analysis-results/18Seuks_rdp_99pct_Jan2017/rep_set.fna \
	-m usearch61 \
	-o usearch_checked_chimeras/ \
	-r /home/gomre/taruna/50k-GOM-Illumina-subsample/ref-dbs/Silva119_release/rep_set_eukaryotes/99/Silva_119_rep_set99_18S.fna
```


#### Step 10 - Remove pynast failures from the BIOM table
```
filter_otus_from_otu_table.py \
	-i /home/gomre/taruna/50k-GOM-Illumina-subsample/analysis-results/18Seuks_rdp_99pct_Jan2017/otu_table_mc2_w_tax_metadata.biom \
	-o /home/gomre/taruna/50k-GOM-Illumina-subsample/analysis-results/18Seuks_rdp_99pct_Jan2017/usearch_checked_chimeras/otu_table_mc2_w_tax_metadata_NOpynastfail.biom \
	-e /home/gomre/taruna/50k-GOM-Illumina-subsample/analysis-results/18Seuks_rdp_99pct_Jan2017/pynast_aligned_seqs/rep_set_failures.fasta
```

#### Step 11 - Remove chimeras from the resulting BIOM table in Step 10

```
filter_otus_from_otu_table.py \
	-i /home/gomre/taruna/50k-GOM-Illumina-subsample/analysis-results/18Seuks_rdp_99pct_Jan2017/usearch_checked_chimeras/otu_table_mc2_w_tax_metadata_NOpynastfail.biom \
	-o /home/gomre/taruna/50k-GOM-Illumina-subsample/analysis-results/18Seuks_rdp_99pct_Jan2017/usearch_checked_chimeras/otu_table_mc2_w_tax_metadata_NOpynastfail_NOchimeras.biom \
	-e /home/gomre/taruna/50k-GOM-Illumina-subsample/analysis-results/18Seuks_rdp_99pct_Jan2017/usearch_checked_chimeras/chimeras.txt
```
#### Step 12 - Summarize the BIOM table to identify the minimum number of sequences per sample and store the output in a text file. The minimum number of sequences per sample should be used for the `-e` in Step 15
```
biom summarize-table \
	-i /home/gomre/taruna/50k-GOM-Illumina-subsample/analysis-results/18Seuks_rdp_99pct_Jan2017/usearch_checked_chimeras/otu_table_mc2_w_tax_NOpynastfail_NOchimeras_metadata.biom \
	-o /home/gomre/taruna/50k-GOM-Illumina-subsample/analysis-results/18Seuks_rdp_99pct_Jan2017/usearch_checked_chimeras/biom_summary_filt_NOpf_NOc.txt
```

#### Step 13 - Filter fasta file of aligned rep set sequences to only keep OTUs in filtered BIOM file from Step 11
```
filter_fasta.py \
	-f /home/gomre/taruna/50k-GOM-Illumina-subsample/analysis-results/18Seuks_rdp_99pct_Jan2017/pynast_aligned_seqs/rep_set_aligned_pfiltered.fasta \
	-o /home/gomre/taruna/50k-GOM-Illumina-subsample/analysis-results/18Seuks_rdp_99pct_Jan2017/usearch_checked_chimeras/pfiltered_align_BIOMfilt.fasta \
	-b /home/gomre/taruna/50k-GOM-Illumina-subsample/analysis-results/18Seuks_rdp_99pct_Jan2017/usearch_checked_chimeras/otu_table_mc2_w_tax_metadata_NOpynastfail_NOchimeras.biom
```

#### Step 14 - Make new phylogeny with final set of OTUS (no chimeras, no alignment failures)
```
make_phylogeny.py \
	-i /home/gomre/taruna/50k-GOM-Illumina-subsample/analysis-results/18Seuks_rdp_99pct_Jan2017/usearch_checked_chimeras/pfiltered_align_BIOMfilt.fasta \
	-o /home/gomre/taruna/50k-GOM-Illumina-subsample/analysis-results/18Seuks_rdp_99pct_Jan2017/usearch_checked_chimeras/pfiltered_align_BIOMfilt.tre \
	--tree_method fasttree
```

#### Step 15 - Run beta div analysis
```
beta_diversity_through_plots.py \
	-i /data/gomre/taruna/50k-GOM-Illumina-subsample/analysis-results/18Seuks_rdp_99pct_Jan2017/usearch_checked_chimeras/otu_table_mc2_w_tax_metadata_NOpynastfail_NOchimeras.biom \
	-m /home/gomre/taruna/GOM-Illumina/qiime-files/mapping-files/QIIMEmappingfile_GOM_Illumina_7Apr14_F04.txt \
	-o /data/gomre/taruna/50k-GOM-Illumina-subsample/analysis-results/18Seuks_rdp_99pct_Jan2017/beta_div_thru_plots_50K_NOpf_NOc \
	-t /data/gomre/taruna/50k-GOM-Illumina-subsample/analysis-results/18Seuks_rdp_99pct_Jan2017/usearch_checked_chimeras/pfiltered_align_BIOMfilt.tre \
	-e 3412
```
