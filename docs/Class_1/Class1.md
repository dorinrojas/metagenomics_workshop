# Class 1: Basic Unix shell Bash commands and workshop methodology

- - - -

## Using Unix through the terminal

Unix shell is a command-line interface (CLI) platform and scripting language initially developed to construct other softwares. For example, the iOS softwares is written in a derivated of Unix language. This is systems is efficient is an universal operating systems used in many computers. In addition, it allows to run several softwares and small programs.

There are different types of Unix shells. However, the most popular among computers is Bash (Bourne Again SHell).Our computational cluster (and most of them) is based on Bash. Therefore, it is important to understand the main commands of the CLI.

We are used to interact with computer in our daily life through a graphical user interface (GUI). Here, we provide instructions by clicking in folders and other directions from the menu that are learned intuitively. The shell lacks the presentation of the directories and works as a command line.

### Accessing the Unix shell

The shell is a more efficient way to communicate and work with a computer. It can be accessed through the "terminal" (also called "Command prompt"). Once you have opened a terminal, it initiates with the `$` symbol. This called a prompt and it is an indicator of the shell waiting for an input (the same reason it is also called "Command prompt"). Prior the dollar symbol, it is common to found the username of your computer.

```console
[dorian.rojas@accessnode test]$
```

Contratry to Apple computers, Windows is built under a different system; thus, the commands we are going to explore do not work in the windowns shell. In this case, the first line of the shell should look something like this:

```console
C:\Users\rojas>
```

In order to be working on the same systems, we are going to enter the computational cluster through a SSH client. This client is a sofware programs that enables us to connect to remote computers (in this case, our cluster) by applying a secure shell protocol. Most computers have the SSH client installed within the terminal. However, in some cases, Windows users have to use and external terminal.

> For those using Windows without the SSH client installed, we recommend installing the MobaXterm server. Please follow the instruction for downloading the software [here](https://mobaxterm.mobatek.net/download.html)

Let's connect to the cluster. For this, open the terminal and input the following command:

```console
ssh [user]@172.16.24.2
```

`[user]` must be change for the cluster username you were provided with. For example, my command would be:

```console
ssh dorian.rojas@172.16.24.2
```

After that, the terminal should ask for your password. Input it into the blank space. As a precaution, the terminal is not going to show what you are writting in the password. Following this, enter and you should see the welcome message of the HPC cluster of the University of Costa Rica

```console
Register this system with Red Hat Insights: insights-client --register
Create an account or view all your systems at https://red.ht/insights-dashboard
Last login: __
[dorian.rojas@accessnode ~]$
```

#### Bash command composition

A shell command is composed by the prompt (`$`), the command (`ls`), options (`-h`), and arguments (what the indications operates on; for instance, a directory or a file). Moreover, options that do not require require arguments can be placed together. In this regard, `ls -lh` holds two flags, the `-l` for listing `-h` for transforming data into human-readble. Here, `ls -l -h` is synonyms of `ls -lh`.

### Basic Unix commands

Now let's explore some of the main Unix commands and try some exercises to get comfortable with the "black screen". These are the main system commands. Remember that the Unix command line is case sensitive.

Command|Function|Command|Function
-------|--------|-------|--------
`pwd`|Returns path|`ls`|Lists directories and files
`cd <path>`|Changes dir|`ls -lh`|Lists with human-readable data
`cd ..`|Goes to parent dir|`cp <file> <file_copy>`|Copies a file
`rm <file>`|Removes a file|`cp -r <dir> <dir_copy>`|Copies a dir
`rm -r <dir>`|Removes a dir|`mv <file> <path>`|Moves file to path
`mkdir`|Creates a dir|`mv <file/dir> <new_name>`|Changes name
`nano <file>`|Creates an empty file|`sbatch <file.slurm>`|Runs .slurm file
`squeue`|Shows running jobs|`scancel <jobID>`|Cancels job

Give a try to some of the basic commands. Input these in your console:

```console
[dorian.rojas@accessnode curso]$ ls
[dorian.rojas@accessnode curso]$ mkdir test
[dorian.rojas@accessnode curso]$ cd test/
[dorian.rojas@accessnode test]$
```

Notice how the `ls` command does not output anything? It is because your account is empty at the moment. Subsequently you create a new directory named 'test' (`code`) and the move to that directory (`cd test`). See the name of prior the $ changes? it indicated the directory in which you are currently. To obtain the full path to the current directory use `pwd`.

```console
[dorian.rojas@accessnode test]$ pwd
/home/dorian.rojas/curso/test
```

> As mentioned above, the shell terminal is case sensitive and takes in consideration the spaces. Although in a GUI, we are use to use spaces, periods, among other special characters. Is it recommended that when working in Bash files and directories:
    1. Don't have spaces. This will complete change the meaning of the command.
    2. Don't start with - (dash).
    3. Stick to letter and numbers.
    4. If required, - (dash) or _ (underscore) can be used instead of spaces (e.g. `dorian_rojas` rather than `dorian rojas`).

Most command have a menu for the flags (or options) that can be used with them (e.g. `-r`). In order to see many you have to use the flag `--help` or `-h`.

```console
[dorian.rojas@accessnode test]$ ls --help
Usage: ls [OPTION]... [FILE]...
List information about the FILEs (the current directory by default).
Sort entries alphabetically if none of -cftuvSUX nor --sort is specified.

Mandatory arguments to long options are mandatory for short options too.
  -a, --all                  do not ignore entries starting with .
  -A, --almost-all           do not list implied . and ..
      --author               with -l, print the author of each file
  -b, --escape               print C-style escapes for nongraphic characters
      --block-size=SIZE      with -l, scale sizes by SIZE when printing them;
                               e.g., '--block-size=M'; see SIZE format below
  -B, --ignore-backups       do not list implied entries ending with ~
  -c                         with -lt: sort by, and show, ctime (time of last
                               modification of file status information);
                               with -l: show ctime and sort by name;
                               otherwise: sort by ctime, newest first
[help]
```

This command is essential for most tools, as it would allow to have an overview of the usage and requirements of the software prior to running. In bioinformatics, when we are trying out a new tools, this is very often used to access the menu on "How to run".

#### Removing command: careful while deleting files

Other relevant command is the remove command (`rm`). In Unix shell, deleting is forever and there is no way to recover a removed file or directory. An interesting approach to have a double confirmation when deleting is running the rm command as follow:

```console
[dorian.rojas@accessnode curso]$ rm -i note.txt
rm: remove regular file 'note.txt'? n
[dorian.rojas@accessnode curso]$ rm -ir test/
rm: remove directory 'test/'? n
[dorian.rojas@accessnode curso]$ ls -F
note.txt  test/
[dorian.rojas@accessnode curso]$
```

The `-i` option prompts a delete confirmation that has to be answer using `Y` or `N` to delete or cance, respectively. This gives the user a chance to check the files and directories being deleted in order to avoid commiting undesierable removals.

#### Basic Bash commands tasks

Explore some other basic commands individually by completing the following task. The answers or code samples to solve each task are at the final section of the page.

1. Go back to the parent directory
2. Print the complete path where you are located
3. Change the name of the directory to 'metagenomics'
4. Delete the 'metagenomics' directory
5. List the current running jobs

#### Text editing commands

To continue, we will explore the use of text files commands, which are relevant to work with slurm programs. There are a lot of text editor for use (e.g. vi, vim, nano, sublime). We'll use 'nano', which is newer, simpler, and easier than other editor like vim. Let's open a nano file:

```console
nano note.txt
```

This command opens a black screen where you can write in different lines. In the lower section, you have the main commands to work with the nano file. The most relevant function is to save and close the document. For this, you have to press `ctrl + x`. If the document is empty, it won't save anything in your directory (check with `ls`).

Now, open again a nano file and write some line with your name multiple times and try to exit. You'll see the document ask for another command: "Save modified buffer". This basically asks if you want to save the changes make to the document. To continue press `y`. This will require to input the file name, but as we already test the name at the beginning, just press `enter`.

In summary, to save a nano document with modification the command `ctrl + x + y + enter` is required. Check that the document was saved in your current directory with `ls`. You can also save the file while writing using `ctrl + o` and then exit.

```console
[dorian.rojas@accessnode curso]$ nano note.txt
[dorian.rojas@accessnode curso]$ ls
note.txt
[dorian.rojas@accessnode curso]$
```

Now let's explore other text file commands:

Command|Function|Command|Function
-------|--------|-------|--------
`cat <file>`|Prints complete file|`head <file>`|Prints the first lines of file
`tail <file>`|Prints last lines of file|`more <file>`|Prints little by little, exiting with `q`
`wc <file>`|Counts words in file|`wc -l`|Counts lines in file
`cmd > <file>`|Saves STDOUT in file|`cat <file1> <file2> > <file3>`|Concatenates files into file 3
`grep "pattern" <file>`|Prints lines matching pattern|`awk`|Very diverse command for text manipulation

#### Text commands tasks

Using the previously created file, we are going to explore these commands through the following tasks:

1. Reopen the file and write 10 lines of different cities
2. Print the complete document
3. Print the last lines
4. Print the first lines
5. Try printing the lines little by little
6. Create a new document with the 10 different names
7. Concatenate the cities document with the names document
8. Print only one line corresponding to one of the names in the document
9. Count the number of lines in the concatenated document

#### Other useful Bash commands

These are another relevant commands useful to work in Unix systems

Command|Function|Command|Function
-------|--------|-------|--------
`gzip`|Compresses into .gzip|`zip`|Compresses into .zip
`gunzip`|Decompresses a .gzip|`unzip`|Decompresses a .zip
`tar -cf <dir>`|Compresses dir into .tar|`tar -x <.tar>`|Decompresses a .tar
`wget <URL>`|Downloads file|`top`|Shows activity
`scp <server>:<path> <path>`|Copies files between computers|`exit`|Exits remote connection

Feel free to try and run these commands yourself. Be careful and ask question pertinent question to your instructur if required.

#### Wildcards

Finally, Unix shell have the chance to use wildcard to select multiple files at the same times. The two most important wildcards are `*` and `?`. The asterik `*` represent zero or more characters, whereas `?` represents exactly one character. For a better understading, set a directory with the files `methane.txt` and `ethane.txt`. For instance, `*ethane.txt` would indicate both files, while `?ethane.txt` only `methane.txt`.

```console
[dorian.rojas@accessnode curso]$ ls *ethane.txt
ethane.txt  methane.txt
[dorian.rojas@accessnode curso]$ ls ?ethane.txt
methane.txt
```

Wildcards are relevant as they help to manage large set of data. For example, if you own a directory full of .fastq files and want to concatenate them into a single file, the command `cat *.fastq` would be efficient to get the job done. If not using wildcards, each name of the files would have to be input individually, which might becomes easily exhaustive if there are more than 5 files with large and complex names.

## Workshop methodology and slurm files

### Slurm files configuration and template

For this workshop we will be working with slurm files. Slurm is a workload manager that enables the correct organization of commands and jobs running in the computational cluster. Slurm files are defined with `.slurm` extentions. They are normal text files with a characteristic header

```console
#!/bin/bash
#SBATCH --partition=parallel
#SBATCH --account=parallel-24h
#SBATCH --time=24:00:00
#SBATCH --nodes=1
#SBATCH --ntasks-per-node=64
#SBATCH --job-name="fastq"
#SBATCH -o zz-%x-%j.o
#SBATCH -e zz-%x-%j.e
#SBATCH --mail-user=dorian.rojas@ucr.ac.cr
#SBATCH --mail-type=END,FAIL
```

Let's break it down for a better understanding. The first line `#!/bin/bash` is the command that allows the computer to recognize the document as a .slurm file. The folliwing lines `#SBATCH --partition=parallel` and `#SBATCH --account=parallel-24h` represent cluster/specific configurations. It is important to remark that all computational cluster are organized different. Therefore, the introduction to the cluster is essential for its correct usage.

```console
#SBATCH --time=24:00:00
#SBATCH --nodes=1
#SBATCH --ntasks-per-node=64
```

Represent the maximum time the job is allow to run for, the number of computation nodes to use, and the number of threats.

```console
#SBATCH --job-name="fastq"
#SBATCH -o zz-%x-%j.o
#SBATCH -e zz-%x-%j.e
```

These are the job name and the name of the output files. Due to personal preferences, I separate the error file (.e) and output file (.o). So, if there is an error with the command, it can be easily accessed.

```console
#SBATCH --mail-user=dorian.rojas@ucr.ac.cr
#SBATCH --mail-type=END,FAIL
```

Finally, the config are to allow the Slurm manager to send email directly to my inbox indicating when the job has ended or failed. You can also indicate it to send a "began" email by adding `BEGIN` or changing the options to `ALL`.

Note that most of these are personal configurations, the slurm file have a lot of `#SBATCH` options that can be modified as you prefer. If you have already worked with slurm and prefer other type of options, feel free to modify the header accordingly.

Following in the .slurm file, it's a change directory to working directory (`cd <path>`) and the calling of an object contaning the containers path (`CTN_PATH`).

```console
cd /home/dorian.rojas/
CTN_PATH=/opt/ohpc/pub/containers/BIO/
```

After this comes the command to run that specific job. Let's take the example of a template for `fastq-dump` tool. This tool allows to download data using SRA accession from the NCBI. This would be the complete slurm file.

```console
#!/bin/bash
#SBATCH --partition=parallel
#SBATCH --account=parallel-24h
#SBATCH --time=24:00:00
#SBATCH --nodes=1
#SBATCH --ntasks-per-node=64
#SBATCH --job-name="fastq"
#SBATCH -o zz-%x-%j.o
#SBATCH -e zz-%x-%j.e
#SBATCH --mail-user=dorian.rojas@ucr.ac.cr
#SBATCH --mail-type=END,FAIL

cd ./1-data/

CTN_PATH=/opt/ohpc/pub/containers/BIO/

$CTN_PATH/sra-tools-3.1.1.sif fastq-dump --version

for id in $@; do

echo "Downloading " $id
$CTN_PATH/sra-tools-3.1.1.sif fastq-dump --gzip \
        --skip-technical --readids --dumpbase --split-3 \
        --clip -O . $id

echo $id " downloaded"

done

date
time
```

### Workship methodology

During this workshop we will work with two samples to ensure the optimization of the computational resources and avoid saturating the cluster. In order to have a better automatization of the pipeline, the slurm files are written under a simple interconection of different files. Note automatization of workflow can be performed using other more efficient software (e.g. Nextflow, Snakemake). However, these require prior knowlegde on how to write the automatized pipeline. Therefore, we will use this Bash commands.

For this we use three files: a `accessions.txt`, a `batch.sh`, and a `.slurm` file. The first file contains only the sample's accession in separate lines. The second file extract the accession from the first file using a `sed` command and indicates the `.slurm` to run using that accession.

```console
[dorian.rojas@accessnode test]$ cat accessions.txt
SRR9988196
SRR8555091

[dorian.rojas@accessnode test]$ cat batch.sh
#!/bin/bash

sample1="$(sed -n '1p' accessions.txt)"
sample2="$(sed -n '2p' accessions.txt)"

sbatch read_qc.slurm $sample1
sbatch read_qc.slurm $sample2
```

Finally, the slurm is the job we want to run. In order to use the accession sent from the `batch.sh` command we code based on a _for loop_. Loops are programming structure that allow the iteration of the inside command a specific number of time. In our case, the loop runs for the number of accession indicated in the `batch.sh` file. Note we use `sed -n '1p' accessions.txt`, only the first line of the .txt, meaning one single accession. However, this could be modified for any number of accessions. For instance, `sed -n '1,8p' accessions.txt` would run eight accessions. We have coded our `batch.sh` file for only one accession due to the limited number of sample we are analyzing.

Additionally, the `batch.sh` file indicates to the _for loop_ to use the sample accessions as sample name (`sbatch read_qc.slurm $sample1`). Therefore, in the `.slurm` file, the command `for id in $@; do` indicates to run the code for the number of ids (`id`, an object) present in `$sample1` (`$@`). In Bash, to call the object we are require to set the prompt prior the name (e.g. `$id`, `$CTN_PATH`).

```console
for id in $@; do

echo "Downloading " $id
$CTN_PATH/sra-tools-3.1.1.sif fastq-dump --gzip \
        --skip-technical --readids --dumpbase --split-3 \
        --clip -O . $id

echo $id " downloaded"

done
```

It is also important to mentione that enclosing the object in braces adds the following string to the object name in the code lecture. For example:

```console
id = sample1
${id}.fastq = sample1.fastq
```

This will be handy in following codes when extensions and output names are required. See below an example using the quality control module of metawrap (`metawrap read_qc`). Here the input requires the extension of each paired reads file (`${sample}_1.fastq`,`${sample}_2.fastq`).

```console
for id in $@; do

echo "Working on " $id

mkdir 2-read_qc/$sample

metawrap read_qc -1 1-data/${sample}_1.fastq \
        -2 1-data/${sample}_2.fastq \
        -t 64 -o 2-read_qc/$sample

echo $id " done"

done
```

This name selection based on the `accession.txt` and `batch.sh` files allows the `.slurm` command to run throughout multiple sample, regardless of the name. In addition, if the same code is required for a complete different dataset, creating an `accession.txt` and `batch.sh` files with their respective new information would be enough to allow to slurm command to run correctly. This permits to analyze samples more efficiently as the main `.slurm` command will only have to be created from scratch once. Some small modifications might still be needed (e.g. changing the working directory path, files paths).

Feel free to ask any questions regarding this methodology. It is essential to understand how this interplay works to complete the workshop. However, through practice you will get a better understanding of these commands.

## Task solutions

**Basic commands tasks:**

1. Go back to the parent directory

    ```console
    cd ..
    ```

2. Print the complete path where you are located

    ```console
    pwd
    ```

3. Change the name of the directory to 'metagenomics'

    ```console
    mv test metagenomics
    ```

4. Delete the 'metagenomics' directory

    ```console
    rm -r metagenomics
    ```

5. List the current running jobs

    ```console
    squeue
    ```

**Text file commands task:**

1. Reopen the file and write 10 lines of different cities

    ```console
    nano note.txt
    ```

2. Print the complete document

    ```console
    cat note.txt
    ```

3. Print the last lines

    ```console
    tail note.txt
    ```

4. Print the first lines

    ```console
    head note.txt
    ```

5. Try printing the lines little by little

    ```console
    more note.txt
    ```

6. Create a new document with the 10 different names

    ```console
    nano names.txt
    ```

7. Concatenate the cities document with the names document

    ```console
    cat note.txt names.txt > concat.txt
    ```

8. Print only one line corresponding to one of the names in the document

    ```console
    grep "dorian" concat.txt
    ```

9. Count the number of lines in the concatenated document

    ```console
    wc -l concat.txt
    ```
