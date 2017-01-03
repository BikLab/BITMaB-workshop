## Qiime pipeline for analyzing and visualizing marine benthic community data

This tutorial will guide you through an example Qiime workflow. Qiime is a Python-based program that can analyze raw DNA reads directly. It is an open source software and more information about the following commands can be found [here](http://qiime.org/) 
### Picking OTUs using open reference

##### Once the fastq files have been demultiplexed, the next step is to pick OTUs in your data using a ref database. 

```
pick_open_reference_otus.py -i /home/gomre/taruna/gomri/data-clean/NPRB_18S_small.faa \
	-r /home/gomre/taruna/gomri/ref_dbs/SILVA/Silva_119_rep_set99_18S.fna \
	-o /home/gomre/taruna/gomri/analysis-results/0_18Seuks_uclust99_19Dec2016 \
	-p /home/gomre/taruna/gomri/qiime_params/18S_openref99_rdp.txt \
	-s 0.10 \
	--prefilter_percent_id 0.0 \
	--suppress_align_and_tree \
	-f
```

##### Next, you have to align the query seqs against a database. The output will be used to construct a phylogeny
 
 ```
align_seqs.py \
 	-i /home/gomre/taruna/gomri/analysis-results/0_18Seuks_uclust99_19Dec2016/rep_set.fna \
 	-o /home/gomre/taruna/gomri/analysis-results/0_18Seuks_uclust99_19Dec2016/pynast_aligned_seqs \
 	-t /home/gomre/taruna/gomri/ref_dbs/SILVA/Silva_119_rep_set99_aligned_18S_only.fna \
 	--alignment_method pynast \
	--pairwise_alignment_method uclust \
	--min_percent_id 70.0 
 ```
 
 
##### filter alignment to remove gaps
```
filter_alignment.py \
	-i /home/gomre/taruna/gomri/analysis-results/0_18Seuks_uclust99_19Dec2016/pynast_aligned_seqs/rep_set_aligned.fasta \
	-o /home/gomre/taruna/gomri/analysis-results/0_18Seuks_uclust99_19Dec2016/pynast_aligned_seqs \
	--suppress_lane_mask_filter
```


##### run Chimera Checking step
```
identify_chimeric_seqs.py -i /data/gomre/taruna/gomri/analysis-results/0_18Seuks_uclust99_19Dec2016/pynast_aligned_seqs/rep_set_aligned.fasta \
	-m ChimeraSlayer \
	-a /home/gomre/taruna/gomri/ref_dbs/SILVA/Silva_119_rep_set99_aligned_18S_only.fna \
	-o /data/gomre/taruna/gomri/analysis-results/0_18Seuks_uclust99_19Dec2016/chimera_check/chimeric_seqs 

```
##### remove pynast failures from filtered BIOM table
```
filter_otus_from_otu_table.py \
-i /home/gomre/taruna/gomri/analysis-results/0_18Seuks_uclust99_19Dec2016/otu_table_mc2_w_tax.biom \
	-o $CHIMERA/otu_table_mc2_w_tax_NOpynastfail.biom \
	-e $ALIGNSEQS/rep_set_failures.fasta
```
 