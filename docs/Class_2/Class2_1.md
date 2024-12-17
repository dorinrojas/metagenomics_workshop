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

> Recomendations:
> Remember three file are required to make the pipeline work: `accessions.txt`, `batch.sh`, and `.slurm`. You can go back to the previous class to use the template provided for the slurm header and batch file.
> The accession file consist of only the accessions in separate lines. You can create the file with `nano` and copy paste the id from the beginning of this class.
> Some tools create output directories if they do not exist (`fasterq-dump` does). However, I recommend to create these in advance, meaning `mkdir 1-data` should be the first command to be run in this example.
> Personally, I like to name slumr files as the tools that is running. For instance, this slurm would be called `fasterq-dump.slurm`. However, you can give it the name you want.
> All files and commands are deposited in the path `/home/public/met-workshop` and at the end of this github page. If you have doubts regarding if you file have any error you can ask the instructor or revise the file.
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

Great part of the pipeline is performed throught the tool [metaWRAP](https://github.com/bxlab/metaWRAP). This is a metagenomic wrapper made as a easy-to-use suite for analysis. This means it alone does not perform any analysis but it comprises several tools (which will be explained) and used them to perform the job of interest. Hence, metaWRAP basically writes a command that runs all the other tools within its environment.

There a vast variety of way to perform metagenomics analysis. For instance, you could use individual tools and different flags and parameters. However, metaWRAP servers as an parameterizer that allows a greater reproducibility. As the wrapper uses general databases and tools, it favors the analysis of different types of microbiomes (e.g. gut, water, soil) as demonstrated in the [benchmark paper](https://microbiomejournal.biomedcentral.com/articles/10.1186/s40168-018-0541-1).

> During the workshop, we'll use various modules from this wrapper. Each of the module is stand-alone, therefore we can use only the modules that we required rather than running the complete metaWRAP pipeline.

Firstly, we are going to explore the read_qc module to conduct the quality control and filtering of the metagenomics data.

This module consist of a set of tools that enable the pre-process, trimming, and filtering of

### Running metawrap read_qc

## Task solutions

**accession.txt file:**

```console
[dorian.rojas@accessnode test]$ cat accessions.txt
SRR9988196
SRR8555091
```

**batch.sh file:**

```console
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
