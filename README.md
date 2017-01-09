# Welcome to the BITMaB workshop - Corpus Christi, TX (January 9-13, 2017)

This workshop provides training on how to describe the taxonomic diversity in a marine benthic community, using both morphological taxonomy and environmental sequencing approaches.

Workshop Schedule is here: 

# Getting started
### Login using the username assigned to you

```
ssh username@ron.sr.unh.edu
```

### To use Qiime, run the commands below

```
enable_qiime.sh
```
```
source activate qiime1
```

Now, you should be in the QIIME environment and ready to run through the pipeline. 

***

### BITMaB Workshop Pipeline for analyzing metabarcoding data using QIIME

Here is an overview of the general steps of the QIIME pipeline that we will carry out during the BITMaB workshop (click links to jump to detailed instructions for each step):

#### [Step 1](https://github.com/BikLab/BITMaB-workshop/blob/master/Qiime4GOMRI.md#step-1---demultiplex-the-raw-reads): Demultiplex the raw reads (example workflow for workshop)

#### [Step 2](https://github.com/BikLab/BITMaB-workshop/blob/master/Qiime4GOMRI.md#step-2---pick-otus): Pick Operational Taxonomic Units

#### [Step 3](https://github.com/BikLab/BITMaB-workshop/blob/master/Qiime4GOMRI.md#step-3---identify-amd-remove-chimeric-sequences): Identify chimeras and remove these sequences from the OTU table

#### [Step 4](https://github.com/BikLab/BITMaB-workshop/blob/master/Qiime4GOMRI.md#step-4---remove-pynast-failures-from-the-biom-table): Align sequences and remove alignment failures from the OTU table

#### [Step 5](https://github.com/BikLab/BITMaB-workshop/blob/master/Qiime4GOMRI.md#step-5----filter-fasta-file-of-aligned-rep-set-sequences-to-only-keep-otus-in-filtered-biom-file): Filter rep set fasta file to match the OTU IDs in your filtered OTU table 

#### [Step 6](https://github.com/BikLab/BITMaB-workshop/blob/master/Qiime4GOMRI.md#step-6---make-new-phylogeny-with-final-set-of-otus-no-chimeras-no-alignment-failures): Construct a phylogenetic tree

#### [Step 7](https://github.com/BikLab/BITMaB-workshop/blob/master/Qiime4GOMRI.md#step-7---run-diversity-analysis): Carry out microbial community analyses to assess alpha- and beta-diversity 

***

### Where are all the files?
The locations of all the files you will need for the QIIME tutorial are listed below

**Data file** - GOM Illumina seqeunces, in fasta format:

```
/home/gomre/taruna/200k-GOM-Illumina-subsample/data-clean/GOM_concat1.7_subsample200k_10Jan14.fna
```

**Mapping file** - tab-delimited spreadsheet containing sample metadata:

```
/home/gomre/taruna/200k-GOM-Illumina-subsample/qiime-files/mapping-files/QIIMEmappingfile_GOM_Illumina_7Apr14_F04.txt
```
**Parameters file** - 

```
/home/gomre/taruna/200k-GOM-Illumina-subsample/qiime-files/parameters/18S_openref99_rdp_silva119.txt
```

**Silva119 database** - Publically available reference database files, [downloaded from the QIIME website](http://qiime.org/home_static/dataFiles.html). SILVA is the normal database we use for microbial eukayote metabarcoding analyses (meiofauna, protists, etc.), but other databases you can use include Greengenes (for bacterial/archaeal 16S rRNA data) and UNITE (for fungal ITS rRNA data). Both of these are also available for download at the above QIIME link. Or you can build and use your own custom database! 

**18S rep set file** - unaligned reference sequences, in fasta format

```
/home/gomre/taruna/200k-GOM-Illumina-subsample/ref-dbs/Silva119_release/rep_set_eukaryotes/99/Silva_119_rep_set99_18S.fna
```

**Aligned 18S rep set fasta** - the reference SILVA sequences which were subjected to multiple sequence alignment (using secondary structure information from rRNA)

```
/home/gomre/taruna/200k-GOM-Illumina-subsample/ref-dbs/Silva119_release/aligned_rep_files/99_18S_only/Silva_119_rep_set99_aligned_18S_only.fna
```

**Taxonomy file** - mapping file which links sequence IDs to taxonomic hierarchies (what we usually refer to as "taxonomy strings"")

```
/home/gomre/taruna/200k-GOM-Illumina-subsample/ref-dbs/Silva119_release/consensus_majority_taxonomy/consensus_taxonomy_eukaryotes/99/taxonomy_99_7_levels_consensus.txt
```
Full directory of SILVA database files (For reference, in case you want to look around or try out some customized analyses)

```
/home/gomre/taruna/200k-GOM-Illumina-subsample/ref-dbs/Silva119_release/
```