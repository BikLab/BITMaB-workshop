# Welcome to the BITMaB workshop - Corpus Christi, TX (January 9-13, 2017)

This workshop provides training on how to describe the taxonomic diversity in a marine benthic community, using both morphological taxonomy and environmental sequencing approaches.

Workshop Schedule is here: 

# Getting started
### Login using the username assigned to you

```
ssh username@ron.sr.unh.edu
```
### Setting up a temp directory on Ron
This setup is specific to Ron and may not be needed for other servers.

1. #### Create a temp directory in your home directory

2. #### Open your `bashrc` in nano

3. #### Type the following line at the end of your `bashrc` and save the file.

```
export TMPDIR=/home/gomre/USERNAME/tmp
```

4. #### D. Source your `bashrc`

### To use Qiime, run the commands below

```
enable_qiime.sh
```
```
source activate qiime1
```

Now, you should be in the QIIME environment and ready to run through the pipeline. 

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