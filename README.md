# Genome Annotation using MAKER

[MAKER](http://www.yandell-lab.org/software/maker.html) is a great tool for annotating a reference genome using empirical and *ab initio* gene predictions. [GMOD](http://gmod.org/wiki/Main_Page), the umbrella organization that includes MAKER, has some nice tutorials online for running MAKER.

## Software & Data

#### Software prerequisites:
1. [RepeatModeler](http://www.repeatmasker.org/RepeatModeler/) and [RepeatMasker](http://www.repeatmasker.org/RMDownload.html) with all dependencies (I used NCBI BLAST) and [RepBase](http://www.girinst.org/repbase/) (version used was 20150807).
2. MAKER version 3.01.03 (though any other version 2 releases should be okay).
3. [Augustus](http://bioinf.uni-greifswald.de/augustus/) version 3.2.3.
4. [SNAP](http://korflab.ucdavis.edu/software.html) version 2013-02-16.
5. [BEDtools](https://bedtools.readthedocs.io/en/latest/) version 2.28.0
#### Raw data/resources:
1. `GCA_012979105.1_ASM1297910v1_genomic.fna.gz`: The *de novo* *RPW_Genome_Project* reference genome assembled as part of New York University Abu Dhabi RPW Genome Project
2. `transcripts.fasta`: A *de novo* transcriptome assembly created using DNASTAR with heterogeneous RNA-Seq data of male and female RPWs.
3. `EST.fasta` : An alternative species ESTs from four different species : *Tribolium castaneum*, *Sitophilus oryzae*, *Dendroctonus ponderosae*, and *Drosophila melanogaster*. This file was used in maker_opts.ctl under `altest`option to increase the accuracy of maker initio prediction model.
4. Full protein amino acid sequences, in FASTA format, for three other Squamate species from [NCBI](https://www.ncbi.nlm.nih.gov/genome/) or [Ensembl](http://www.ensembl.org/index.html): *Tribolium castaneum*, *Sitophilus oryzae*, *Dendroctonus ponderosae*, and *Drosophila melanogaster*.
#### Running commands:
I've become accustomed to running an adaptive methods for most programs( which run on Linux server) to ruun on Mac OSX.

## Repeat Annotation

#### 1. *De Novo* Repeat Identification
Need to be updated
#### 2. Full Repeat Annotation
Need to be updated
#### 3. Initial MAKER Analysis
MAKER is pretty easy to get going and relies on properly completed control files. These can be generated by issuing the command `maker -CTL`. The only control file we will be using is the `maker_opts.ctl` file. In this first round, we will obviously providing the data files for the transcriptome assembly (`est`), and for the other Squamate protein sequences (`protein.fasta`). We will also set the `ultest` to `EST.fasta`. Here is the full control file for reference. Here is a snapshot of the maker_opts.ctl for the initial running:
`cat maker_opts.ctl`

#-----Genome (these are always required)
genome= /Users/abdulmalek/Documents/iGenome/RNA_SEQ_WORKSHOP/data/RPW_Genome.fna #genome sequence (fasta file or fasta embeded in GFF3 file)
organism_type=eukaryotic #eukaryotic or prokaryotic. Default is eukaryotic

#-----Re-annotation Using MAKER Derived GFF3
maker_gff= #MAKER derived GFF3 file
est_pass=0 #use ESTs in maker_gff: 1 = yes, 0 = no
altest_pass=0 #use alternate organism ESTs in maker_gff: 1 = yes, 0 = no
protein_pass=0 #use protein alignments in maker_gff: 1 = yes, 0 = no
rm_pass=0 #use repeats in maker_gff: 1 = yes, 0 = no
model_pass=0 #use gene models in maker_gff: 1 = yes, 0 = no
pred_pass=0 #use ab-initio predictions in maker_gff: 1 = yes, 0 = no
other_pass=0 #passthrough anyything else in maker_gff: 1 = yes, 0 = no

#-----EST Evidence (for best results provide a file for at least one)
est= /Users/abdulmalek/Documents/iGenome/RNA_SEQ_WORKSHOP/data/transcripts.fasta #set of ESTs or assembled mRNA-seq in fasta format
altest= /Users/abdulmalek/Documents/iGenome/RNA_SEQ_WORKSHOP/data/ESTs.fna #EST/cDNA sequence file in fasta format from an alternate organism
est_gff= #aligned ESTs or mRNA-seq from an external GFF3 file
altest_gff= #aligned ESTs from a closly relate species in GFF3 format

#-----Protein Homology Evidence (for best results provide a file for at least one)
protein= /Users/abdulmalek/Documents/iGenome/RNA_SEQ_WORKSHOP/data/Protein.faa #protein sequence file in fasta format (i.e. from mutiple organisms)
protein_gff=  #aligned protein homology evidence from an external GFF3 file

#-----Repeat Masking (leave values blank to skip repeat masking)
model_org=all #select a model organism for RepBase masking in RepeatMasker
rmlib= #provide an organism specific repeat library in fasta format for RepeatMasker
repeat_protein=/usr/local/maker/data/te_proteins.fasta #provide a fasta file of transposable element proteins for RepeatRunner
rm_gff= #pre-identified repeat elements from an external GFF3 file
prok_rm=0 #forces MAKER to repeatmask prokaryotes (no reason to change this), 1 = yes, 0 = no
softmask=1 #use soft-masking rather than hard-masking in BLAST (i.e. seg and dust filtering)

#-----Gene Prediction
snaphmm= #SNAP HMM file
gmhmm= #GeneMark HMM file
augustus_species= #Augustus gene prediction species model
fgenesh_par_file= #FGENESH parameter file
pred_gff= #ab-initio predictions from an external GFF3 file
model_gff= #annotated gene models from an external GFF3 file (annotation pass-through)
run_evm=0 #run EvidenceModeler, 1 = yes, 0 = no
est2genome=1 #infer gene predictions directly from ESTs, 1 = yes, 0 = no
protein2genome=1 #infer predictions from protein homology, 1 = yes, 0 = no
trna=0 #find tRNAs with tRNAscan, 1 = yes, 0 = no
snoscan_rrna= #rRNA file to have Snoscan find snoRNAs
snoscan_meth= #-O-methylation site fileto have Snoscan find snoRNAs
unmask=0 #also run ab-initio prediction programs on unmasked sequence, 1 = yes, 0 = no
allow_overlap= #allowed gene overlap fraction (value from 0 to 1, blank for default)

#-----Other Annotation Feature Types (features MAKER doesn't recognize)
other_gff= #extra features to pass-through to final MAKER generated GFF3 file

#-----External Application Behavior Options
alt_peptide=C #amino acid used to replace non-standard amino acids in BLAST databases
cpus=1 #max number of cpus to use in BLAST and RepeatMasker (not for MPI, leave 1 when using MPI)

#-----MAKER Behavior Options
max_dna_len=100000 #length for dividing up contigs into chunks (increases/decreases memory usage)
min_contig=1 #skip genome contigs below this length (under 10kb are often useless)

pred_flank=200 #flank for extending evidence clusters sent to gene predictors
pred_stats=0 #report AED and QI statistics for all predictions as well as models
AED_threshold=1 #Maximum Annotation Edit Distance allowed (bound by 0 and 1)
min_protein=0 #require at least this many amino acids in predicted proteins
alt_splice=0 #Take extra steps to try and find alternative splicing, 1 = yes, 0 = no
always_complete=0 #extra steps to force start and stop codons, 1 = yes, 0 = no
map_forward=0 #map names and attributes forward from old GFF3 genes, 1 = yes, 0 = no
keep_preds=0 #Concordance threshold to add unsupported gene prediction (bound by 0 and 1)

split_hit=10000 #length for the splitting of hits (expected max intron size for evidence alignments)
min_intron=20 #minimum intron length (used for alignment polishing)
single_exon=0 #consider single exon EST evidence when generating annotations, 1 = yes, 0 = no
single_length=250 #min length required for single exon ESTs if 'single_exon is enabled'
correct_est_fusion=0 #limits use of ESTs in annotation to avoid fusion genes

tries=2 #number of times to try a contig if there is a failure for some reason
clean_try=0 #remove all data from previous run before retrying, 1 = yes, 0 = no
clean_up=0 #removes theVoid directory with individual analysis files, 1 = yes, 0 = no
TMP= #specify a directory other than the system default temporary directory for temporary files

***Two other important settings are `est2genome` and `protein2genome`, which are set to 1 so that MAKER gene predictions are based on the aligned transcripts and proteins (the only form of evidence we currently have).***


# KACST RPW annotated genome is now available at NCBI Accession number ASM1446268v1
The annotation part will be suspended for now.
## RNA-Seq analysis of Red palm weevil
## Software & Data
#### Software prerequisites:
1. [STAR] https://github.com/alexdobin/STAR/archive/2.7.6a.tar.gz
2. [BEDtools](https://bedtools.readthedocs.io/en/latest/) version 2.28.0
3. [samtools](http://www.htslib.org/) samtools 1.10; Using htslib 1.10.2
## Preparing data for STAR
### 1: Generating Genome Indices
### "This protocol describes generating the genome indices using the genome FASTA file as input. The genome indices are required for all type of mapping jobs. This step needs to be performed only once for each genome assembly. The resulting files are saved in a user-specified directory, and can be re-used for mapping different samples to the same genome." Mapping RNA-seq Reads with STAR for Alexander Dobin and Thomas R. Gingeras.
For quite a while I tried to generate genome indices for red palm weevil however each time the process failed because the STAR expect clean sort and cohernt data to start with. Here are the issues I run into preparing the data for generating genome indices:
1. The annotation file and the genome file needed some preprocessing. STAR --genomeGenerate option failed becuase the names of chroms/scaffolds are not identical so I had to parse the fasta headers.

`cut -d ' ' -f1 RPW.genome.fa > RPW.genomeNEW.fa`

2. RAM issues: STAR is fast but requires huge ammount of RAM resources. I spent quite sometime solving this problem. Finally found an issue with same problem at https://github.com/alexdobin/STAR/issues/103 . 
De novo assemblies generates large number of scaffolds which increase the memory consumption. The Red palm weevil genome has 24005 scaffolds. I followed this https://github.com/alexdobin/STAR/issues/103#issuecomment-173009628 to solve the issue.

Here is the script for generating genome indices
`STAR --runMode genomeGenerate \`
`--genomeDir Redpalm \`
`--genomeFastaFiles RPW.genomeNEW.fa \`
`--sjdbGTFfile JAACXV01.1.gbff.gff JAACXV01.2.gbff.gff \`
`--runThreadN 4 --genomeChrBinNbits 13`
