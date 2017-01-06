```
pick_open_reference_otus.py -i ~/GOM-Illumina/data-clean/GOM_concat1.7_allF04combo_10Jan14.fna \
	-r ~/GOM-Illumina/ref_dbs/Silva119_release/rep_set_eukaryotes/99/Silva_119_rep_set99_18S.fna \
	-o ~/GOM-Illumina/18S_rdp_99pct_silva119_Jan2017 \
	-p ~/GOM-Illumina/qiime-files/parameters/18S_openref99_rdp_silva119.txt \
	-s 0.10 \
	--prefilter_percent_id 0.0 \
	--suppress_align_and_tree \
	-f
```