# Welcome to the BITMaB-workshop, Corpus Christi, TX (January 9-13, 2017)

This workshop provides guidelines to describe the taxonomic diversity in a marine benthic community. We will utilize a pipeline called Qiime (v1.9.1) to analyze and visualize microbial diversity using raw DNA sequences.

## Getting started
We'll start by logging into Ron using the `ssh` command. Ron is a UNH server computer where Qiime and many other programs are installed. Type the following command using the username assigned to you.

```
ssh username@ron.sr.unh.edu
```
Once you hit enter, type in your password which won't show on the screen but it's there. 

Next, change your password by typing the `passwd` command and hit enter. 

Choose a new password.

## Next, run

```
source ~/.bashrc
```

## To use Qiime, run the commands below

```
enable_qiime.sh
```
```
source activate qiime1
```

Now, you should be in the Qiime environment and ready to run through the pipeline. 

## Where are all the files?
The locations of all the files you will need for the Qiime tutorial are listed below

**Data file:** 

```
/home/gomre/taruna/200k-GOM-Illumina-subsample/data-clean/GOM_concat1.7_subsample200k_10Jan14.fna
```

**Mapping file:**

```
/home/gomre/taruna/200k-GOM-Illumina-subsample/qiime-files/mapping-files/QIIMEmappingfile_GOM_Illumina_7Apr14_F04.txt
```
**Parameters file:**

```
/home/gomre/taruna/200k-GOM-Illumina-subsample/qiime-files/parameters/18S_openref99_rdp_silva119.txt
```

**Silva119 database:**

#### 18S rep set fasta

```
/home/gomre/taruna/200k-GOM-Illumina-subsample/ref-dbs/Silva119_release/rep_set_eukaryotes/99/Silva_119_rep_set99_18S.fna
```

#### Aligned 18S rep set fasta

```
/home/gomre/taruna/200k-GOM-Illumina-subsample/ref-dbs/Silva119_release/aligned_rep_files/99_18S_only/Silva_119_rep_set99_aligned_18S_only.fna
```

#### Taxonomy file

```
/home/gomre/taruna/200k-GOM-Illumina-subsample/ref-dbs/Silva119_release/consensus_majority_taxonomy/consensus_taxonomy_eukaryotes/99/taxonomy_99_7_levels_consensus.txt
```
#### Other database files 

```
/home/gomre/taruna/200k-GOM-Illumina-subsample/ref-dbs/Silva119_release/
```