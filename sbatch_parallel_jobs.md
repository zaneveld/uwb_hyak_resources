#Running Embarassingly Parallel Jobs on the Cluster


## Embarassingly Parallel Problems 
In cluster lingo 'embarassingly parallel' jobs means tasks that don't depend on one another at all.
In a real-world analogy, let's say you ordered two chairs from Ikea. Perhaps it would take you 20 minutes to build a chair.
If you had a friend to help, each of you could build one chair, and it should take half the time. 
If you needed to build 1000 chairs and you had 1000 friends, then in theory you could build all 1000 chairs still just in 20 minutes.
That's an 'embarassingly parallel' task, and a great type of task to use a cluster to speed up.

On the other hand, let's say you are writing a novel. Although in theory you could divide up the chapters among multiple people, it might not really work, since what happens in each 
chapter likely depends on foreshadowing, character arcs, and plot set up in previous chapters. A biological example might be many types of tree inference.

Note that setting up very large parallel runs is *annoying* so if it's not really needed for your tasks, it's fine just to run tasks sequentially. 

That said, if you have some time to invest, learning how to run jobs in parallel can open up new horizongs in terms of what you even consider possible as a project.
For example, once you see you *could* run some python script on 1000 different genomes, you might get ideas for analysis that involve larger scale comparisons tha you would have previously considered.

## How to build up to running a parallel task on the cluster

Running large tasks on a computing cluster is a fairly high-level task. There are a few steps you want to go through before attempting it.
Here is one overall process you can use:
0. Set up a folder organization for your project. I use something like this:
   input/
   output/
   procedure/
   apptainers/

   input just holds input data
   output just holds results calculated from input data
   procedure holds your scripts (.sh files, python scripts, and slurm job templates)
   apptainers holds the files that define how to set up a working environment for your analysis (e.g. install miniconda, use miniconda to install software, etc)
   The apptainer itself (i.e. the `.sif` file) can live here once built. (I put it into a special `sifs` folder that can be ignored during GitHub check ins so as to keep it in a predictable place for scripts but not check in the large .sif file itself to GitHub).

1. Set up software in an Apptainer
  1a. Look up software documentation
  1b. Try out the tutorial to make sure it does what you want.
  1c. Write out an Apptainer `.def` file that installs the software
  1d. Build the Apptainer (with apptainer build)
  1e. Get an interactive node, apptainer shell into your apptainer, make sure all software needed works
   (at minimum run help)
   
2. Use a small test dataset to interactively test that things work and debug any issues

3. Using the example from your small data set, write code to generate all the commands you need to run. Write them to one or more .txt files that end in _01.sh, _02.sh, etc.

4. Generate an overall slurm job file that describes what partition to run in, how many nodes you need etc. The filenames for the folder with all your commands
   may be helpful for figuring out how many nodes you need. These are all set with #SBATCH commands. Look up sbatch example to find templates you can use.

   You may need to check `hyakalloc` to see how many nodes are available to you to help plan what resources you will need. It will print out how many CPUs/GPUs are available in each partition you have access to.
   Note the name of the partition, since this gets used in the slurm script.

   Note that on Hyak for very large jobs that can be started & stopped the ckpt (checkpoint) partition has a huge number of CPUs, though it may take a while for your job to get priority to run.

   Once you know what resources you need, you can then write that into a slurm script. Basically this has a bunch of lines that say #SBATCH and then set some valued parameter

   so for example a line that says
   #SBATCH --job-name Orthofinder_DIAMOND

   means set the job-name valued parameter for this job to Orthofinder-Diamond.

   It often really helps to have an example. Remember that just like when you tested things out interactively, it's a very good idea to test a smaller run first.
   For example, if you want to try analyzing 1000 genomes on a cluster, try a job where you analyze, say, 4, then maybe 10 then maybe 100 before jumping to 1000.
   This will let you estimate how run time is scaling and identify any issues before you have to wait a long time and use tons of resources.

   But if everything looks good, don't be afraid to go for it and run your big analysis.

   Pay special attention to the --array parameter. It generates a number that you can access with ${SLURM_ARRAY_TASK_ID} in your commands down below.

   So in the example below, this is used so each process being sent out to a different node uses a different input file with different commands in it:
   <pre>
   input_file="../output/slurm_command_groups/commands_for_slurm_${SLURM_ARRAY_TASK_ID}.sh"

     
   </pre>

   
   If you like, you can modify this example when writing your slurm array job:
   <pre>
#!/bin/bash
#
# ==============================================================================
# OrthoFinder with DIAMOND SLURM Script
# ==============================================================================
#
# This script runs OrthoFinder using DIAMOND for sequence homology searches.
# Adjust the resource requests based on your dataset size.
#
# ------------------------------------------------------------------------------
# SLURM Directives (adjust as needed)
# ------------------------------------------------------------------------------
#
# Define job name
#SBATCH --job-name=OrthoFinder_DIAMOND
#
# Specify the queue or partition
#SBATCH --partition=ckpt
#
# Request total memory for the job
#SBATCH --mem=32G
#
# Request number of CPU cores
#SBATCH --nodes=100
#SBATCH --ntasks-per-node=10
#SBATCH --cpus-per-task=4
#
# Set max runtime (e.g., 24 hours = 24:00:00)
#SBATCH --time=24:00:00
#
# Send email notifications for job status
#SBATCH --mail-type=ALL
#SBATCH --mail-user=zaneveld@uw.edu
#
# Define output and error file paths
#SBATCH --output=../output/slurm_logs/orthofinder_%j.out
#SBATCH --error=../output/slurm_logs/orthofinder_%j.err
#
##BATCH --chdir=/gscratch/zaneveld/jesse/Holobiont_Models/analysis/endozoicomonas_tree/procedure
#
#
#SBATCH --array=0-99
# ------------------------------------------------------------------------------
# Main Script
# ------------------------------------------------------------------------------


#Comment out if you want to keep old slurm logs
#rm -rf ../output/slurm_logs/*

#Set input file of commands we want to run on the cluster
#Note that ${SLURM_ARRAY_TASK_ID} is just a number 0,1,2,3,4 etc
#It will be set on each job. So ahead of time we make commands_for_slurm_1.sh, commands_for_slurm_2.sh, etc and each will be run as a separate job
input_file="../output/slurm_command_groups/commands_for_slurm_${SLURM_ARRAY_TASK_ID}.sh"
echo "Running Slurm script in the following file ${input_file}"
</pre>

5. Submit your job with sbatch

sbatch my_slurm_job.job

6. Check priority to see when your big job will run:
   squeue --me --start

7. Check progress
   squeue -u $USER
   
## Set up software in an Apptainer
First, make sure you have a stable environment to run your tasks in. We're currently using Apptainers to provide a frozen, unchanging operating system with all 
tools needed for a particular task. Rather than ever being manually modified these are always recreated directly from a .def file that says how to install all the relevant software.

Second, you want to generate a small dataset that you can test interactively on a single node. At first, you may encounter a lot of errors. It's much better to be able to see these very quickly, rather than sorting them out after a long wait
when trying to submit to the cluster. For example, if you want to run QC on a giant sequence file, you could run tests on the first 100 entries in that file. 
Just make sure if you cut down a file for testing you keep the proper file format.


So if you made an Apptainer with your software, you'd want to get an interactive node on the server (see other pages in this GitHub archive for more details).
Then you would run Apptainer shell to enter the apptainer, and activate any relevant conda virtual environments you set up.

## 

## Dividing up tasks into different files to use with slurm Array jobs

To run parallel jobs, the recommended method is to use Slurm Array jobs.

Basically the idea is that you divide up commands you want to run into multiple files.
genome_analysis_commands_0.sh
genome_analysis_commands_1.sh
genome_analysis_commands_2.sh
etc

Each file ends with a number in the exact same, predictable place.

