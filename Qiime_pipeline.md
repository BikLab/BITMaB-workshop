## Qiime pipeline for analyzing and visualizing marine benthic community data

This tutorial will guide you through an example Qiime workflow. Qiime is a Python-based program that can analyze raw DNA reads directly. It is an open source software and more information about the following commands can be found [here](http://qiime.org/).

### Checking for errors in mapping file
Before we begin working with our data, we must check our mapping file for errors so Qiime can function properly. We can run the command below to do just that.  
 
```
validate_mapping_file.py -o my-checked-map/ -m my-map.tsv
```

### Demultiplex the reads

Once the mapping errors are corrected, proceed to splitting the fastq files
```
split_libraries_fastq.py \
	-o split-libraries-out/ \
	-i forward_reads.fastq.gz \
	-b barcodes.fastq.gz \
	-m map.tsv
```
### Picking OTUs using open reference

Once the fastq files have been demultiplexed, the next step is to pick OTUs in your data using a ref database. 

```
pick_open_reference_otus.py -i /data/share/gomre/Bitmap/fastas/NAME.faa \
	-r /data/share/gomre/Bitmap/ref_dbs/SILVA/Silva_119_rep_set99_18S.fna \
	-o ~/analysis-results/18Seuks_uclust99_Jan2017 \
	-p /data/share/gomre/Bitmap/qiime_params/18S_openref99_rdp.txt \
	-s 0.10 \
	--prefilter_percent_id 0.0 \
	--suppress_align_and_tree \
	-f
```

Next, you have to align the query seqs against a database. The output will be used to construct a phylogeny.
 
 ```
align_seqs.py \
 	-i ~/analysis-results/18Seuks_uclust99_Jan2017/rep_set.fna \
 	-o ~/analysis-results/18Seuks_uclust99_Jan2017/pynast_aligned_seqs \
 	-t /data/share/gomre/Bitmap/ref_dbs/SILVA/Silva_119_rep_set99_aligned_18S_only.fna \
 	--alignment_method pynast \
	--pairwise_alignment_method uclust \
	--min_percent_id 70.0 
 ```
 
 
##### filter alignment to remove gaps
```
filter_alignment.py \
	-i ~/analysis-results/18Seuks_uclust99_Jan2017/pynast_aligned_seqs/rep_set_aligned.fasta \
	-o ~/analysis-results/18Seuks_uclust99_Jan2017/pynast_aligned_seqs \
	--suppress_lane_mask_filter
```


##### run Chimera Checking step
```
identify_chimeric_seqs.py -i ~/analysis-results/18Seuks_uclust99_Jan2017/pynast_aligned_seqs/rep_set_aligned.fasta \
	-m usearch61 \
	-a /data/share/gomre/Bitmap/ref_dbs/SILVA/Silva_119_rep_set99_aligned_18S_only.fna \
	-o ~/analysis-results/18Seuks_uclust99_Jan2017/chimera_check/ 
```


##### filter any unwanted taxa from the BIOM table. BIOM table contains OTUs.

```
filter_taxa_from_otu_table.py -i ~/analysis-results/18Seuks_uclust99_Jan2017/otu_table_mc2_w_tax.biom \
	-o ~/analysis-results/18Seuks_uclust99_Jan2017/chimera_check/otu_table_mc2_w_tax_filtered.biom \
	-n c__Chloroplast,f__mitochondria,Unassigned
```
##### remove pynast failures from filtered BIOM table
```
filter_otus_from_otu_table.py -i ~/analysis-results/18Seuks_uclust99_Jan2017/chimera_check/otu_table_mc2_w_tax_filtered.biom \
	-o ~/analysis-results/18Seuks_uclust99_Jan2017/chimera_check/otu_table_mc2_w_tax_filtered_NOpynastfail.biom \
	-e ~/analysis-results/18Seuks_uclust99_Jan2017/pynast_aligned_seqs/rep_set_failures.fasta
```

##### remove OTUs identified as chimeric from the filtered-no-Pynast-failures BIOM table
```
filter_otus_from_otu_table.py -i ~/analysis-results/18Seuks_uclust99_Jan2017/chimera_check/otu_table_mc2_w_tax_filtered_NOpynastfail.biom \
	-o ~/analysis-results/18Seuks_uclust99_Jan2017/chimera_check/otu_table_mc2_w_tax_filtered_NOpynastfail_NOchimeras.biom \
	-e ~/analysis-results/18Seuks_uclust99_Jan2017/chimera_check/chimeric_seqs.txt
```

##### add metadata to BIOM table
```
biom add-metadata -i ~/analysis-results/18Seuks_uclust99_Jan2017/chimera_check/otu_table_mc2_w_tax_filtered_NOpynastfail_NOchimeras.biom \
	-o ~/analysis-results/18Seuks_uclust99_Jan2017/chimera_check/otu_table_mc2_w_tax_filtered_NOpynastfail_NOchimeras_metadata.biom \
	-m map.tsv
```

##### summarize BIOM table
```
biom summarize-table -i ~/analysis-results/18Seuks_uclust99_Jan2017/chimera_check/otu_table_mc2_w_tax_filtered_NOpynastfail_NOchimeras_metadata.biom \
	-o ~/analysis-results/18Seuks_uclust99_Jan2017/chimera_check/biom_summary_filt_NOpf_NOc.txt
```

##### filter fasta file of aligned rep set sequences to only keep OTUs in final filtered BIOM table
```
filter_fasta.py -f ~/analysis-results/18Seuks_uclust99_Jan2017/pynast_aligned_seqs/rep_set_aligned_pfiltered.fasta \
	-o ~/analysis-results/18Seuks_uclust99_Jan2017/chimera_check/pfiltered_align_BIOMfilt.fasta \
	-b ~/analysis-results/18Seuks_uclust99_Jan2017/chimera_check/otu_table_mc2_w_tax_filtered_NOpynastfail_NOchimeras_metadata.biom
```

##### make new phylogeny with newly filtered fasta file
```
make_phylogeny.py -i ~/analysis-results/18Seuks_uclust99_Jan2017/chimera_check/pfiltered_align_BIOMfilt.fasta \
	-o ~/analysis-results/18Seuks_uclust99_Jan2017/chimera_check/pfiltered_align_BIOMfilt.tre \
	--tree_method fasttree
```

##### diversity analyses 
```
beta_diversity_through_plots.py -i  ~/analysis-results/18Seuks_uclust99_Jan2017/chimera_check/otu_table_mc2_w_tax_filtered_NOpynastfail_NOchimeras_metadata.biom \
	-m my-map.tsv \
	-o ~/analysis-results/18Seuks_uclust99_Jan2017/diversity-analyses/beta_div_thru_plots_1700 \
	-t ~/analysis-results/18Seuks_uclust99_Jan2017/chimera_check/pfiltered_align_BIOMfilt.tre \
	-e 1700
```
```
alpha_diversity -i  ~/analysis-results/18Seuks_uclust99_Jan2017/chimera_check/otu_table_mc2_w_tax_filtered_NOpynastfail_NOchimeras_metadata.biom \
	-o ~/analysis-results/18Seuks_uclust99_Jan2017/diversity-analyses/alpha_div \
	-t ~/analysis-results/18Seuks_uclust99_Jan2017/chimera_check/pfiltered_align_BIOMfilt.tre 
```
```
alpha_rarefaction -i  ~/analysis-results/18Seuks_uclust99_Jan2017/chimera_check/otu_table_mc2_w_tax_filtered_NOpynastfail_NOchimeras_metadata.biom \
	-m my-map.tsv \
	-o ~/analysis-results/18Seuks_uclust99_Jan2017/diversity-analyses/alpha_rare \
	-t ~/analysis-results/18Seuks_uclust99_Jan2017/chimera_check/pfiltered_align_BIOMfilt.tre \
	-e 1700
```