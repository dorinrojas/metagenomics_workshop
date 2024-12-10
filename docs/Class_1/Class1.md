Class 1: Basic Unix commands and workshop methodology<a name="TOP"></a>
=========

## Using Unix through the terminal

Unix is a platforms initially developed to construct other softwares. For example, the iOS softwares is written in a derivated of Unix language. This is systems is efficient is an universal operating systems currently used in many computer. It allows to run several softwares and small programs. 

The computational cluster (and most of them) that we are going to use in our workshop is based on Unix. Therefore, it is important to understand the main commands of the systems. 

First it is important to learn how to use the terminal. The terminal is a way to communicate with the computer. It is common to be found under the names "Command prompt" or "Terminal". Contratry to Apple computers, Windows is built under a different system; thus, the commands we are going to explore do not work in the terminal. 

In order for all of us to be working on the same systems, we are going to enter the computational cluster through a SSH client. This client is a sofware programs that enables us to connect to remote computers (in this case, our cluster) by applying a secure shell protocol. Most computers have the SSH client installed within the terminal. However, in some cases, Windows users have to use and external terminal.

Let's first try using the terminal for connecting to the cluster. For this, open the terminal and input the following command:

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
mkdir|Creates a dir|mv <file/dir> <new_name>|Changes name
nano <file>|Creates an empty file|sbatch <file.slurm>|Runs .slurm file
squeue|Shows running jobs|scancel <jobID>|Cancels job
top|Shows activity|module load|Loads module
module unload|Unloads module|module avail|Lists all avail modules
module list|Lists loaded modules||




dorian dorian
dorian 
dorian 
## siguiente texto 
/ 