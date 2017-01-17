# Welcome to the BITMaB workshop - Corpus Christi, TX (January 9-13, 2017)

This workshop provides training on how to describe the taxonomic diversity in a marine benthic community, using both morphological taxonomy and environmental sequencing approaches.

Final workshop Schedule is here: https://docs.google.com/document/d/1H8aHrddJ6VoBwrK6XWA9Zqs5fj8IkqbZIfniPwMKEUQ/edit?usp=sharing

Main Dropbox Folder with all workshop resources is available here: https://www.dropbox.com/sh/p6udtygnk7cf8dr/AAAmpar-QO7jCF_RRxnXigAia?dl=0 

Participant List: NEED LINK

---

# Workshop Schedule (permanent links to talks & handouts)

#### Monday Jan 9th:

* [Introduction to Genomics, Bioinformatics: DNA sequencing and Assembly of Genomes](https://www.dropbox.com/s/cds8uj2l81mounz/KelleyThomas-Intro2NGS-9Jan2017.pdf?dl=0) (Kelley Thomas)
* [Introduction to the command line environment and the world of BASH](NEED LINK) (Jordan Ramsdell)
* **Taxonomy Lecture**: [Goals of the workshop and how it fits into the context of the research project “Genomic Responses to the Deepwater Horizon event and development of high-throughput biological assays for oil spills”](https://www.dropbox.com/s/krr1xn3x814d0nr/FrancescoLeasi-WorkshopGoals-9Jan2017.pdf?dl=0) (Francesca Leasi)
* **Taxonomy Lecture**: Harpacticoid Copepods (Melissa Rohal)

#### Tuesday Jan 10th:

* Bash and Command line tricks, continued  (Jordan Ramsdell and UNH team)
* Intro to the QIIME pipeline for microbial ecology (Holly Bik and UCR team) 
* **Taxonomy Lecture**: [Sipuncula](https://www.dropbox.com/s/iuk69djfave8y3s/AnjaSchulze-Sipuncula-10Jan2017.pdf?dl=0) (Anja Schulze)
* **Taxonomy Lecture**: [Gastrotricha](https://www.dropbox.com/s/vedfgjjebigjqc6/RickHochberg-Gastrotricha-10Jan2017.pdf?dl=0) (Rick Hochberg)

#### Wednesday Jan 11th:

* Data analysis in the QIIME pipeline, continued (Holly Bik and UCR team)
* **Taxonomy Lecture**: Platyhelminthes (Julian Smith III)
* **Taxonomy Lecture**: Nemertea (Jon Norenburg)

#### Thursday Jan 12th:

* Genome assembly and annotation (Jordan Ramsdell and UNH team)
* Introduction to Whole Metagenome Shotgun Analysis.  (Kelley Thomas)
* Mapping DNA sequence reads to reference genomes to investigate genetic diversity or profiling gene expression. (Kelley Thomas)
* **Taxonomy Lecture**: [Aplacophora](https://www.dropbox.com/s/tn4o8a910gnenu3/Kocot_Aplacophora.pdf?dl=0) (Kevin Kocot)
    * Related paper that Kevin mentioned during his talk: [https://academic.oup.com/sysbio/article/doi/10.1093/sysbio/syw079/2449704/Phylogenomics-of-Lophotrochozoa-with-consideration](https://academic.oup.com/sysbio/article/doi/10.1093/sysbio/syw079/2449704/Phylogenomics-of-Lophotrochozoa-with-consideration)
* **Taxonomy Lecture**: Kinorhyncha, Tardigrada, Gnathostomulida, and Loricifera (Martin Sørensen)

#### Friday Jan 13th:

* Visualizing metabarcoding data, and other data analysis tools outside of QIIME (Holly Bik)
* [Reccomendations for Enhancing Research Reproducibility](https://www.dropbox.com/s/sxc0o3a83ziu52k/FASEB_Enhancing%20Research%20Reproducibility%202016.pdf?dl=0) (Kelley Thomas)  
* **Taxonomy Lecture**: Nematoda (Alberto de Jesús Navarrete)
* **Taxonomy Lecture**: Ostracoda (Simone Nunes Brandão)






---
# Getting started
### Login using the username assigned to you

```
ssh username@ron.sr.unh.edu
```
### Setting up a temp directory on Ron
This setup is specific to Ron and may not be needed for other servers.

* **Create a temp directory in your home directory**

* **Open your `~/.bashrc` in nano**

* **Type the following line at the end of your `~/.bashrc` and save the file.**

```
export TMPDIR=/home/gomre/USERNAME/temp
```

* **Source your `~/.bashrc`**

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