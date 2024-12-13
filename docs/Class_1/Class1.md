# Class 1: Basic Unix commands and workshop methodology

- - - -

## Using Unix through the terminal

Unix shell is a command-line interface (CLI) platform and scripting language initially developed to construct other softwares. For example, the iOS softwares is written in a derivated of Unix language. This is systems is efficient is an universal operating systems used in many computers. In addition, it allows to run several softwares and small programs.

There are different types of Unix shells. However, the most popular among computers is Bash (Bourne Again SHell).Our computational cluster (and most of them) is based on Bash. Therefore, it is important to understand the main commands of the CLI.

We are used to interact with computer in our daily life through a graphical user interface (GUI). Here, we provide instructions by clicking in folders and other directions from the menu that are learned intuitively. The shell lacks the presentation of the directories and works as a command line.

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

Notice how the `ls` command does not output anything? It is because your account is empty at the moment. Subsequently you create a new directory named 'test' (`code`) and the move to that directory (`cd test`). See the name of prior the $ changes? it indicated the directory in which you are currently. To obtain the full path to the current directory use `pwd`

```console
[dorian.rojas@accessnode test]$ pwd
/home/dorian.rojas/curso/test
```

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

Explore some other basic commands individually by completing the following task. The answers or code samples to solve each task are at the final section of the page.

1. Go back to the parent directory
2. Print the complete path where you are located
3. Change the name of the directory to 'metagenomics'
4. Delete the 'metagenomics' directory
5. List the current running jobs

To continue, we will explore the use of text files commands, which are relevant to work with slurm programs. There are a lot of text editor for use (e.g. vi, vim, nano, sublime). We'll use 'nano', which is newer, simpler, and easier than other editor like vim. Let's open a nano file:

```console
nano note.txt
```

This command opens a black screen where you can write in different lines. In the lower section, you have the main commands to work with the nano file. The most relevant function is to save and close the document. For this, you have to press `ctrl + x`. If the document is empty, it won't save anything in your directory (check with `ls`).

Now, open again a nano file and write some line with your name multiple times and try to exit. You'll see the document ask for another command: "Save modified buffer". This basically asks if you want to save the changes make to the document. To continue press `y`. This will require to input the file name, but as we already test the name at the beginning, just press `enter`.

In summary, to save a nano document with modification the command `ctrl + x + y + enter` is required. Check that the document was saved in your current directory with `ls`.

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

Finally, these are another relevant commands useful to work in Unix systems

Command|Function|Command|Function
-------|--------|-------|--------
`gzip`|Compresses into .gzip|`zip`|Compresses into .zip
`gunzip`|Decompresses a .gzip|`unzip`|Decompresses a .zip
`tar -cf <dir>`|Compresses dir into .tar|`tar -x <.tar>`|Decompresses a .tar
`wget <URL>`|Downloads file|`top`|Shows activity
`scp <server>:<path> <path>`|Copies files between computers|`exit`|Exits remote connection

Feel free to try and run these commands yourself. Be careful and ask question pertinent question to your instructur if required.

## Workshop methodology and slurm files

[]

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
