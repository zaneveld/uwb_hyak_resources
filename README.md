# UWB HYAK resources
Resources for users of the UWB Hyak nodes

## Official Hyak documentation
https://hyak.uw.edu/docs

## Cheat sheet

### Setup (skip if already familiar with and set up for SSH)
For Windows users, you can install [Git](https://git-scm.com/downloads). This will come with Git BASH, a command line BASH interface. Git BASH includes `ssh`, a command line program that lets you connect to the server.

### Login
In order to work on Hyak you'll need to log in. From within either Terminal (MacOSX) or Git Bash (Windows)

ssh netid@klone.hyak.uw.edu

(replace netid with your netid used to log in to Canvas)

You'll need your phone handy because the HYAK server will call your phone to confirm your login.

### Upload file
copy file to server from local directory (with large files, I got better speeds by using wget to grab files from the web rather than transferring from local. UW network was also faster than home wifi (by an order of magnitude)):  
scp [source filepath] [destination directory]  
scp /mnt/c/Users/Dylan/Documents/zaneveld/klone/gcmp/procedure/denoise.py /gscratch/zaneveld/sonettd/gcmp/procedure

## For folks in the Zaneveld lab / BBIO495 who are using QIIME2

### View available nodes
**view available resources:**  
hyakalloc

### Request interactive node
request interactive compute node (activate screen (https://linuxize.com/post/how-to-use-linux-screen/) first; it doesn't seem like it can be activated from a compute node):  
srun -A zaneveld -p compute-bigmem --time=[hours:minutes:seconds OR days-hours] --mem=[memory amount]G --pty /bin/bash

*Example*:
srun -A bbiosci -p compute --time=3:00:00 --mem=20G --pty /bin/bash

Use this for short-term tasks

### Request nodes and run a script 
sbatch [filepath].slurm  
instructions for .slurm file here (https://hyak.uw.edu/docs/compute/scheduling-jobs#batch-jobs). Only thing to note is that if you're using qiime2, you'll need to activate the conda environment within the script by adding these lines above your command line or python instructions:  
eval "$(conda shell.bash hook)"  
conda activate [qiime2 environment] 


### Update your PATH to include the lab installation of QIIME2
The PATH variable is a list of folders, each separated by a colon (`:`), that tells the machine where to look for programs when you type a program name into the command line interface. In our case, we have some installed software (QIIME2) that we want to run from the command line and share across accounts. Usually this would be added to your PATH when you install the software, but here we want to share a copy of the software across accounts, so we manually add it to PATH using the `export` command in BASH. This will let us run the `conda` package manager that contains a copy of the python programming language, as well as the QIIME2 software.

export PATH=$PATH:/gscratch/zaneveld/miniconda3/bin:/gscratch/zaneveld/miniconda3/condabin

### Activate your QIIME2 environment within the conda package manager
Once you have added conda to your path, you can then use it to activate QIIME2.

source activate qiime2-2021.4

# CRITICAL NOTE: DO NOT RUN THE QIIME2 TUTORIAL DIRECTLY ON THE LOGIN NODE. You **MUST** first get an interactive node using srun.

### view your active compute nodes  
squeue -u [netid]

### view all active compute nodes
squeue -A zaneveld

### rejoin compute node already allocated  
sattach [jobid].0
