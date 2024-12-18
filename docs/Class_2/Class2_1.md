# Class 2.1: Quality control and filtering of metagenomics sequences

- - - -

We will be working using two samples. These come from a previous study performed by [Meziti et al. 2021](https://journals.asm.org/doi/10.1128/aem.02593-20). The data came from the project EcoZUR (_E. coli_ in Urban and Rural Areas). This was a case-control study of diarreha carried out in northern Ecuadro during April 2014 - April 2015 ([Peña-González et al. 2019](https://journals.asm.org/doi/full/10.1128/aem.01820-19)). All the data generated from this project was deposited under the BioProject ID [PRJNA486009](https://www.ncbi.nlm.nih.gov/bioproject/?term=PRJNA486009).

For the aim of this workshop we will be using two of the complete dataset. These correspond to the samples specificed in the following table.

Accession|Condition|File size
:--------|:--------|---------
SRR9988196|Diarrhea|2.6 Gb (each)
SRR8555091|Control|2.6 Gb (each)

> Throughout the complete workshop, all the data and intermediate files of the pipeline are already stored in the following path `/home/public/met-workshop`. The idea of the workshop is for you to write your own codes and use you own output file. However, considering some of these analysis take a great amount of time to be completed and there would be ~30 jobs running at the same time. In some cases you will be required to call (or copy) the input files from this path. Feel free to take a look at the directory for you to locate all the data.

## Downloading the data (fasterq-dump)

The first step towards starting the analysis is downloading the data. For our workshop, we will be using previously generated data stored in the NCBI and will perform the downloading using a tool called `fasterq-dump`.

> If you were to use the analysis under your own data, normally after sequecing you'll have to upload the read sequences to the cluster. For this, the command `scp` we mentioned in the previou class would be required. If you have any doubt regarding the utilization of this command, ask your instructor.

`fasterq-dump` is a optimized version of an older tool called `fastq-dump` (you saw an example of how to run this tool in last class). This allosw to extract data in .fastq or .fasta format frm SRA accessions of the NCBI. Althought this tool is a successor, the options are different from the prior software. Hence, it is not a replacement and both tools can still be used. Particularly `fasterq-dump` is faster and easier to use than `fastq-dump`.

This tool is part of the [sra-toolkit](https://github.com/ncbi/sra-tools). Therefore, in order to be used the toolkit must be installed.

Now, as you might remeber from the introduction lecture to the cluster, it works with singularity containers located in the path `/opt/ohpc/pub/containers/BIO/`. So, let's first explore which tools are already installed in the cluster.

```console
[dorian.rojas@accessnode test]$ ls /opt/ohpc/pub/containers/BIO/
abyss-2.3.10.sif                freesurfer-7.4.1.sif     picrust2-2.5.3.sif
admixture-1.3.0.sif             funannotate-1.8.17.sif   pilon-1.24.sif
antismash-7.1.0.sif             gatk4-4.5.0.0.sif        platon-1.7.sif
anvio-7.sif                     gatk4-4.6.0.0.sif        plink-1.9.sif
bactopia-3.1.0.sif              genflow.sif              plink-2.0.sif
bakta-1.9.4.sif                 gtdbtk-2.4.0.sif         prodigal-2.6.3.sif
bandage-0.8.1.sif               gunc-1.0.6.sif           prokka-1.14.6.sif
barrnap-0.9.sif                 hisat2-2.2.1.sif         qiime2-amplicon-2024.5.sif
bbmap-39.10.sif                 hmmer-3.4.sif            qiime2-metagenome-2024.5.sif
bcftools-1.21.sif               htslib-1.21.sif          qualimap-2.3.sif
bedtools-2.3.1.sif              hybracter-0.9.1.sif      quast-5.2.0.sif
biostars.sif                    idops-0.2.2.sif          rfmix-2.03.sif
blast-2.16.0.sif                impute2-2.3.2.sif        rgi-6.0.3.sif
blobtoolkit-4.3.9.sif           instrain-1.9.0.sif       saige-1.3.0.sif
bowtie-1.3.1.sif                kofamscan-1.3.0.sif      sambamba-1.0.1.sif
bowtie2-2.5.4.sif               longqc-1.2.0c.sif        samtools-1.21.sif
busco-5.8.0.sif                 masurca-4.1.1.sif        seqtk-1.4.sif
bwa-0.7.18.sif                  mdmcleaner-0.8.2.sif     shapeit-2.r837.sif
canu-2.2.sif                    medaka-2.0.1.sif         smartdenovo-1.0.0.sif
checkm2-1.0.2.sif               medusa-1.6.sif           sourmash-4.8.11.sif
checkm-genome-1.2.3.sif         megahit-1.2.9.sif        spades-4.0.0.sif
clustalo-1.2.4.sif              metabat2-2.17.sif        sra-toolkit-2.9.3.sif
cogclassifier-1.0.3.sif         metawrap-1.2.sif         sra-toolkit-3.1.0.sif
cutadapt-4.9.sif                multiqc-1.25.1.sif       sra-tools-3.1.1.sif
diamond-2.1.9.sif               nanofilt-2.8.0.sif       star-2.7.11b.sif
diamond_add_taxonomy-0.1.2.sif  nanopack.sif             structure-2.3.4.sif
ecami.sif                       nanoplot-1.43.0.sif      trimmomatic-0.38.sif
ensembl-vep-112.0.sif           necat-0.0.1.sif          unicycler-0.5.1.sif
fastp-0.23.3.sif                neptune-1.2.5.sif        vep-86.sif
fastqc-0.12.1.sif               nextpolish-1.4.1.sif     vsearch-2.29.0.sif
filtlong-0.2.1.sif              orthofinder-3.0.1b1.sif  wengan-0.2.sif
flye-2.9.5.sif                  pgap-7555.sif
```

Note that there are several versions of the sra-toolkit, for the purpose of this workshop we will use the latest `sra-toolkit-3.1.1.sif` container.

The first step at starting new to any pipeline is understand how the tool works and what are the require commands to run it correctly. This can be achieved in two different from by calling the tool in the command line and add the `-h` tool or search the documentation in the internet. In most cases, the information of how to run these tools is located in github repositiories. For instance, this is the github page of [`fasterq-dump`](https://github.com/ncbi/sra-tools/wiki/HowTo:-fasterq-dump).

Additionally, when you are working with software you are not familiar with it is relevant to read and understand what's behind the program. During this workshop, we'll be exploring the utilization of each tools using the command line while in the current github will be summarize the workflow of each tool.

### Running fasterq-dump

Briefly, `fasterq-dump` is a tool that connect directly to the sra accession provided as argument in the command line. Accessions are not files, but containers of files. Therefore, depending on the id more than one fill can be downloaded. In our case, we are using paired-end reads; thus, `fasterq-dump` will created two files in the working directory.

Now, let's start by calling the tools and exploring its options.

```console
[dorian.rojas@accessnode test]$ /opt/ohpc/pub/containers/BIO/sra-tools-3.1.1.sif fasterq-dump -h

Usage:
  fasterq-dump <path> [options]
  fasterq-dump <accession> [options]

Options:
  -F|--format                      format (special, fastq, default=fastq)
  -o|--outfile                     output-file
  -O|--outdir                      output-dir
  -b|--bufsize                     size of file-buffer dflt=1MB
  -c|--curcache                    size of cursor-cache dflt=10MB
  -m|--mem                         memory limit for sorting dflt=100MB
  -t|--temp                        where to put temp. files dflt=curr dir
  -e|--threads                     how many thread dflt=6
  -p|--progress                    show progress
  -x|--details                     print details
  -s|--split-spot                  split spots into reads
  -S|--split-files                 write reads into different files
  -3|--split-3                     writes single reads in special file
  --concatenate-reads              writes whole spots into one file
  -Z|--stdout                      print output to stdout
  -f|--force                       force to overwrite existing file(s)
  --skip-technical                 skip technical reads
  --include-technical              include technical reads
  -M|--min-read-len                filter by sequence-len
  --table                          which seq-table to use in case of pacbio
  -B|--bases                       filter by bases
  -A|--append                      append to output-file
  --fasta                          produce FASTA output
  --fasta-unsorted                 produce FASTA output, unsorted
  --fasta-ref-tbl                  produce FASTA output from REFERENCE tbl
  --fasta-concat-all               concatenate all rows and produce FASTA
  --internal-ref                   extract only internal REFERENCEs
  --external-ref                   extract only external REFERENCEs
  --ref-name                       extract only these REFERENCEs
  --ref-report                     enumerate references
  --use-name                       print name instead of seq-id
  --seq-defline                    custom defline for sequence:  $ac=accession,
                                   $sn=spot-name,  $sg=spot-group, $si=spot-id,
                                   $ri=read-id, $rl=read-length
  --qual-defline                   custom defline for qualities:  same as
                                   seq-defline
  -U|--only-unaligned              process only unaligned reads
  -a|--only-aligned                process only aligned reads
  --disk-limit                     explicitly set disk-limit
  --disk-limit-tmp                 explicitly set disk-limit for temp. files
  --size-check                     switch to control: on=perform size-check
                                   (default),  off=do not perform size-check,
                                   only=perform size-check only
  --ngc <PATH>                     PATH to ngc file

  -h|--help                        Output brief explanation for the program.
  -V|--version                     Display the version of the program then
                                   quit.
  -L|--log-level <level>           Logging level as number or enum string. One
                                   of (fatal|sys|int|err|warn|info|debug) or
                                   (0-6) Current/default is warn.
  -v|--verbose                     Increase the verbosity of the program
                                   status messages. Use multiple times for more
                                   verbosity. Negates quiet.
  -q|--quiet                       Turn off all status messages for the
                                   program. Negated by verbose.
  --option-file <file>             Read more options and parameters from the
                                   file.
for more information visit:
   https://github.com/ncbi/sra-tools/wiki/HowTo:-fasterq-dump
   https://github.com/ncbi/sra-tools/wiki/08.-prefetch-and-fasterq-dump

fasterq-dump : 3.1.1
```

You'll see the tool holds a great variety of options to download data. However, the only required argument is the accession name. We need to perform a simple download of the accession provided above in a directory called `1-data`. In this directory, all the raw data will be downloaded for the purpose of this workshop. Read the "How to run" information displayed in the terminal and try to create the files required to download the data.

**Recomendations:**

1. Remember three file are required to make the pipeline work: `accessions.txt`, `batch.sh`, and `.slurm`. You can go back to the previous class to use the template provided for the slurm header and batch file.
2. The accession file consist of only the accessions in separate lines. You can create the file with `nano` and copy paste the id from the beginning of this class.
3. Some tools create output directories if they do not exist (`fasterq-dump` does). However, I recommend to create these in advance, meaning `mkdir 1-data` should be the first command to be run in this example.
4. Personally, I like to name slumr files as the tools that is running. For instance, this slurm would be called `fasterq-dump.slurm`. However, you can give it the name you want.
5. All files and commands are deposited in the path `/home/public/met-workshop` and at the end of this github page. If you have doubts regarding if you file have any error you can ask the instructor or revise the file.

> I do not advice to copy and paste the template in your files. The idea of the workshop is for you to get familiar with writing and understanding the commands. This might sound archaic, but try and write them from scratch!

Once you have the files. You can send the jobs through the `batch.sh` file. For this use the command:

```console
[dorian.rojas@accessnode test]$ sh batch.sh
Submitted batch job 1934
Submitted batch job 1935
```

Note that the job number will chance. You can also check that the job is running using the `squeue` command:

```console
[dorian.rojas@accessnode test]$ squeue
             JOBID PARTITION     NAME     USER ST       TIME  NODES NODELIST(REASON)
              1926  parallel     bash tatiana.  R    1:22:06      1 cn001
              1931  parallel      job luis.arr  R      17:32      6 cn[002-007]
              1934  parallel  fasterq dorian.r  R       0:02      1 cn008
              1935  parallel  fasterq dorian.r  R       0:02      1 cn009
```

You will see two files in you working directory corresponding to the `.o` and `.e` output documents from the slurm job. These files are important to check in case you job had an issue while running the job.

This might take a file while running (around 15 to 20 minutes per job). So you can either take a break or explore and ask questions regarding the other flags of the tool. Pay attention to the email to set in the slurm file to check whereas your job has ended or failed.

This is probably the simplest command we'll run throughout the workshop, but don't be scared! You'll see the first one is also the hardest :)

After the job had ended, you can check the `1-data` directory and see four files corresponding to the paired end files of each accessions. Once you revise the tool worked correctly, you can delete the log files (`.o`, `.e`). However, I personally recommend to make a new directory to store this documents. They might come handy at some point throught the pipeline.

```console
[dorian.rojas@accessnode test]$ ll 1-data/
total 10968184
-rw-rw-r-- 1 dorian.rojas dorian.rojas 2917900668 dic 17 13:48 SRR8555091_1.fastq
-rw-rw-r-- 1 dorian.rojas dorian.rojas 2917455268 dic 17 13:48 SRR8555091_2.fastq
-rw-rw-r-- 1 dorian.rojas dorian.rojas 2698077902 dic  6 12:02 SRR9988196_1.fastq
-rw-rw-r-- 1 dorian.rojas dorian.rojas 2697976540 dic  6 12:02 SRR9988196_2.fastq
[dorian.rojas@accessnode test]$ mkdir 00-logs
[dorian.rojas@accessnode test]$ mv zz-read_qc-193* 00-logs/
[dorian.rojas@accessnode test]$ ls 00-logs/
zz-read_qc-1934.e  zz-read_qc-1934.o  zz-read_qc-1935.e  zz-read_qc-1935.o
```

This would complete the first part of this class. Next we will explore the quality control and filtering of the recently downloaded data.

## Quality control and filtering of metagenomics data (metaWRAP read_qc module)

Sequencing reads are commonly found as `.fastq` files, which contain the information regarding the basecalling quality score. This quality is named Phred and it is depicted in ASCII format developed for Sanger sequencing technologies. The quality (Q) is the probability (P) of an incorrect basecalling, mathematically represented as follow:

$Q = -10 log_{10}(P)$

The ASCII displays a great variety of characters representing numbers from one to 42 (for Phred base 33, which is the most common code currently). The quality score is interpreted as a logaritmic expression. Therefore, a Q10 would be 1 error per each 10bp, Q20 means 1 error each 100bp, and so forth.

![alt text](qscores_image.gif)

Quality score can vary widely depending on the sequencing technique. Long reads (e.g. PacBio, Nanopore) tend to present lower qualities than short reads (e.g. Illumina). In addition, it is common to see decresing qualities in the latest bases due to polymerase exhaustation. It is agree high quality bases have a Q score of >30, medium quality is between Q30 and Q20, and low quality scores are below Q20.

Qualities can be presented in different ways to the viewer. However, the most common way is boxplots similar to those found in the FastQC results. For instance, in this example all bases present a very high quality with a mean average >35. Those bases with lesser quality at the beganing of the read might be related to the presence of adapters, which are frequently removed during trimming and filtering.

![alt text](qscore_example.png)

### MetaWRAP read_qc module

Great part of the pipeline is performed throught the tool [metaWRAP](https://github.com/bxlab/metaWRAP). This is a metagenomic wrapper made as a easy-to-use suite for analysis. This means it alone does not perform any analysis but it comprises several tools (which will be explained) and used them to perform the job of interest. Hence, metaWRAP basically writes a command that runs all the other tools within its environment.

There a vast variety of way to perform metagenomics analysis. For instance, you could use individual tools and different flags and parameters. However, metaWRAP servers as an parameterizer that allows a greater reproducibility. As the wrapper uses general databases and tools, it favors the analysis of different types of microbiomes (e.g. gut, water, soil) as demonstrated in the [benchmark paper](https://microbiomejournal.biomedcentral.com/articles/10.1186/s40168-018-0541-1).

> During the workshop, we'll use various modules from this wrapper. Each module is stand-alone, therefore we can use only the modules that we required rather than running the complete metaWRAP pipeline.

Firstly, we are going to explore the read_qc module to conduct the quality control and filtering of the metagenomics data.

This module consist of a set of tools that enable the pre-process, trimming, and filtering of metagenomic data. For this, the workflow employs FastQC, TrimGalore, and BMTagger.

[FatsQC](https://github.com/s-andrews/FastQC) is a program that spots potential problems in sequencing datasets. It can analyze multiple files in `.fastq`, `.sam`, or `.bam` format and provides a quick overview the quality metrics. This software works on any kind of data (e.g. small reads, long reads, RNA sequecing); however, it is mostly optimized for sequencing data from Illumina Technologies. Interestingly, this tool was developed to work offline in an application that runs on java and as a software for computational clusters. The main result of FastQC is the `.html` file that summarises the quality metrics. It is important to remark that this tools only checks the quality of the reads, and does not performs any modification. [Here](https://www.bioinformatics.babraham.ac.uk/projects/fastqc/) is the webpage in which FastQC was published.

[TrimGalore](https://github.com/FelixKrueger/TrimGalore) is a wrapper tool that involves [Cutadapt](https://github.com/marcelm/cutadapt) and FastQC to perform trimming of `.fastq` files. This trimming removes low quality bases/reads, adapters, primers, short reads, among other undesirable features that could be present through Cutadapt. For this, an initial trimming of low quality bases in the 3' end is conducted prior the adapter removal. Adapters are autodetected based on one million sequences of the first file, looking for the 12bp or 13bp standard adapters (from Illumina, Small RNA Nextera). If adapter can't be autodetected, `--illumina` is used as default, and if there is a tie between Nextera and Small RNA `--nextera` is default. However, adapter sequences can also be provided in the standalone wrapper. Finally, TrimGalore removes short sequences (default: <20bp) that might result from the trimming. In addition, it performs a quality check of the data using FastQC.  

[BMTagger](https://bioconda.github.io/recipes/bmtagger/README.html) (Best Match Tagger) is a tool that removes the human reads (contamination) from the metagenomic datatsets. This software discriminates based on the provided index file created with bmtools (which might be a [little complex to create](https://www.hmpdacc.org/doc/HumanSequenceRemoval_SOP.pdf)). Therefore, it avoids the time-consuming and computational-demanding aligments other tools require. In the case of metaWRAP, this wappers uses BMTagger with the human genome reference hg38 index already included. For this, this tool compares the query through 18mers to the human genome. However, if the comparison fails, an aligment is performed to removel the sequences with up to two errors.

### Running metawrap read_qc

Due to the installation process, metaWRAP runs a little different. It does not use a singularity container but a miniforge3 environment. This environment must be activated in both your terminal and the slurm file you are going to use. In order to activate it this command must be run and written right after the working directory is set in the `.slurm` file.

```console
. /home/dorian.rojas/bin/miniforge3/bin/activate metawrap-env

[dorian.rojas@accessnode test]$ . /home/dorian.rojas/bin/miniforge3/bin/activate metawrap-env
(metawrap-env) [dorian.rojas@accessnode test]$
```

When you run it in the terminal, the name of the environment `metawrap-env` appears in front of your username. This indicates the env is activated and metaWRAP can be call normally in the terminal. In the slurm file, the environment is activated within the job and metaWRAP will work correctly.

The first step prior writting the command is understading how the module works. This is what we have already explore in the previous section. So, now we need to call the `-h` command on metaWRAP.

```console
(metawrap-env) [dorian.rojas@accessnode test]$ metawrap -h

MetaWRAP v=1.3.2
Usage: metaWRAP [module]

        Modules:
        read_qc         Raw read QC module (read trimming and contamination removal)
        assembly        Assembly module (metagenomic assembly)
        kraken          KRAKEN module (taxonomy annotation of reads and assemblies)
        kraken2         KRAKEN2 module (taxonomy annotation of reads and assemblies)
        blobology       Blobology module (GC vs Abund plots of contigs and bins)

        binning         Binning module (metabat, maxbin, or concoct)
        bin_refinement  Refinement of bins from binning module
        reassemble_bins Reassemble bins using metagenomic reads
        quant_bins      Quantify the abundance of each bin across samples
        classify_bins   Assign taxonomy to genomic bins
        annotate_bins   Functional annotation of draft genomes

        --help | -h             show this help message
        --version | -v  show metaWRAP version
        --show-config   show where the metawrap configuration files are stored
```

This shows all the modules of metawrap. We are currently interested in running the `read_qc` module. Hence, the `-h` command must be used specifically for that module.

```console
(metawrap-env) [dorian.rojas@accessnode test]$ metawrap read_qc -h
metawrap read_qc -h

Usage: metaWRAP read_qc [options] -1 reads_1.fastq -2 reads_2.fastq -o output_dir
Note: the read files have to be named in the name_1.fastq/name_2.fastq convention.
Options:

        -1 STR          forward fastq reads
        -2 STR          reverse fastq reads
        -o STR          output directory
        -t INT          number of threads (default=1)
        -x STR          prefix of host index in bmtagger database folder (default=hg38)

        --skip-bmtagger         dont remove human sequences with bmtagger
        --skip-trimming         dont trim sequences with trimgalore
        --skip-pre-qc-report    dont make FastQC report of input sequences
        --skip-post-qc-report   dont make FastQC report of final sequences


real    0m0,015s
user    0m0,005s
sys     0m0,008s
```

This now displays the usage of this module. Go ahead and write the `read_qc.slurm` file taking in consideration what's here read about the quality control. My template can be found at the end of this page.

Before running the `batch.sh` file, remember to change the name of the `.slurm` after the `sbatch` command. Otherwise, you will be sending the `fasterq-dump.slurm` job again :)

```console
#!/bin/bash

sample1="$(sed -n '1p' accessions.txt)"
sample2="$(sed -n '2p' accessions.txt)"

sbatch read_qc.slurm $sample1
sbatch read_qc.slurm $sample2
```

### Quality control interpretation

The outputs from read_qc module are four files and two directories with the FastQC reports pre- and post-trimming, in each sample directory created in the command.

```console
(metawrap-env) [dorian.rojas@accessnode 2-read_qc]$ ls -F *
SRR8555091:
final_pure_reads_1.fastq  host_reads_1.fastq  post-QC_report/
final_pure_reads_2.fastq  host_reads_2.fastq  pre-QC_report/

SRR9988196:
final_pure_reads_1.fastq  host_reads_1.fastq  post-QC_report/
final_pure_reads_2.fastq  host_reads_2.fastq  pre-QC_report/
```

The `final_pure_reads_?.fastq` files are the final filtered reads, meanwhile `host_reads_?.fastq` are the human sequences remove by the BMTagger. These formers are the ones we are going to continue using throughout the pipeline.

Although the trimmed files are ready to continue their analysis, it is highly important to check the FastQC reports. This is mostly performed in order to check the correct funtioning of the filtering process and ensuring the quantity of the sequences remove does not significantly affect the sequencing depth. Considering the reports are `.html` files, they must be download into our computer to visualize them. For this, using the command `scp` is essential.

The `scp` command must be run in our own terminal, not inside the computational cluster. Its required arguments are the location, path to the file you are downloading, and where in your computer they should be save. For instance, considering my location `dorian.rojas@172.16.24.2`, the path `/home/dorian.rojas/test/2-read_qc/SRR8555091/*/*.html`, and the destination my desktop folder in my computer, the command in my windowns terminal is:

```console 
C:\Users\rojas\OneDrive\Desktop> scp dorian.rojas@172.16.24.2:/home/dorian.rojas/test/2-read_qc/SRR8555091/*/*.html .
```

Note the usage of the wildcard `*`. This command indicates `scp` to copy all the `.html` files found in all the directories of the `home/dorian.rojas/test/2-read_qc/SRR8555091` path. To facilitate the command, I moved within my terminal to the my computer desktop and indicate through a period `.` the destionation. In the terminal, the `.` is a 'secret' folder that represents the current directory. This is similar as how the directory `..` means the parent folder.

It is throughly important that you have analyzed what you are downloading from your the cluster. The all output from metaWRAP have the same name regardless of the sample (or at least for the `post-QC_report`). Hence, you have to download each of the samples separately to avoid overwritting the files. I recommend you download one sample, create a directory in your desktop with the name of that sample to move the reports, and proceed to download the next sample. So, the end product in you desktop is something similar to this:

```bash
C:\Users\rojas\OneDrive\Desktop\docs_curso\read_qc>tree /f
Folder PATH listing for volume Windows
Volume serial number is 90F6-E25D
C:.
├───SRR8555091
│       final_pure_reads_1_fastqc.html
│       final_pure_reads_2_fastqc.html
│       SRR8555091_1_fastqc.html
│       SRR8555091_2_fastqc.html
│
└───SRR9988196
        final_pure_reads_1_fastqc.html
        final_pure_reads_2_fastqc.html
        SRR9988196_1_fastqc.html
        SRR9988196_2_fastqc.html
```

Now, let's open each of the files and analyze them as html.

## Task solutions

**accession.txt file:**

```ps
[dorian.rojas@accessnode test]$ cat accessions.txt
SRR9988196
SRR8555091
```

**batch.sh file:**

```vim
#!/bin/bash

sample1="$(sed -n '1p' accessions.txt)"
sample2="$(sed -n '2p' accessions.txt)"

sbatch read_qc.slurm $sample1
sbatch read_qc.slurm $sample2
```

**fasterq-dump.slurm:**

```console
#!/bin/bash
#SBATCH --partition=parallel
#SBATCH --account=parallel-24h
#SBATCH --time=24:00:00
#SBATCH --nodes=1
#SBATCH --ntasks-per-node=64
#SBATCH --job-name="fasterq"
#SBATCH -o zz-%x-%j.o
#SBATCH -e zz-%x-%j.e
#SBATCH --mail-user=dorian.rojas@ucr.ac.cr
#SBATCH --mail-type=END,FAIL

cd /home/dorian.rojas/test

CTN_PATH=/opt/ohpc/pub/containers/BIO/
$CTN_PATH/sra-tools-3.1.1.sif fasterq-dump --version

for id in $@; do

echo "Downloading " $id
$CTN_PATH/sra-tools-3.1.1.sif fasterq-dump -O ./1-data $id
echo $id " downloaded"

done

date
time
```

**read_qc.slurm:**

```console
#!/bin/bash
#SBATCH --partition=parallel
#SBATCH --account=parallel-24h
#SBATCH --time=24:00:00
#SBATCH --nodes=1
#SBATCH --ntasks-per-node=64
#SBATCH --job-name="read_qc"
#SBATCH -o zz-%x-%j.o
#SBATCH -e zz-%x-%j.e
#SBATCH --mail-user=dorian.rojas@ucr.ac.cr
#SBATCH --mail-type=END,FAIL

cd /home/dorian.rojas/test

. ~/bin/miniforge3/bin/activate metawrap-env

for sample in $@; do

echo "Working on " $sample

mkdir 2-read_qc/$sample

metawrap read_qc -1 1-data/${sample}_1.fastq \
        -2 1-data/${sample}_2.fastq \
        -t 64 -o 2-read_qc/$sample

echo $sample " done"

done

date
time
```
