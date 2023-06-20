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


## For folks in the Zaneveld lab / BBIO495 who are using QIIME2

### Head to the class directory
```
cd /gscratch/zaneveld/bbio495_2022
```
#### Activate qiime2

For BBIO495, I've added an activate_qiime.sh script. It's just a text file with two commands in it.
You can either source this script or run the two commands yourself one by one. 
```
source activate_qiime2.sh
```
*or*
```
export PATH=$PATH:/gscratch/zaneveld/miniconda3/bin:/gscratch/zaneveld/miniconda3/condabin
source activate qiime2-2021.4
```
*Notes:*
-The export PATH command tells the machine where to look to find conda, a program for managing different pieces of installed software/libraries and creating virtual environments like the one QIIME2 uses
- the source activate qiime2-2021.4 command uses conda to activate qiime2 and start up its virtual environment
- After running, you should see something like `(qiime2-2021.4) [zaneveld@klone1 bbio495_2022]$` as your cursor

### View available nodes
**view available resources:**  
hyakalloc

### Request an interactive node
request interactive compute node (activate screen (https://linuxize.com/post/how-to-use-linux-screen/) first; it doesn't seem like it can be activated from a compute node):  
salloc -A zaneveld -p compute-bigmem --time=[hours:minutes:seconds OR days-hours] --mem=[memory amount]G

*Examples*:
salloc -A zaneveld -p compute-bigmem --time=3:00:00 --mem=15G

salloc -A bbiosci -p compute --time=3:00:00 --mem=15G

Use this for short-term tasks

*Notes:*
After getting a node, your cursor will change to say your `username` then `@` then `n` and the node number. For example: `[zaneveld@n3099 bbio495_2022]`

### List which qiime2 plugins are available

qiime --help

### List the methods and visualizers available for a particular plugin
The general format is qiime [plugin name] --help.

**Example**
qiime diversity --help

### List the input and output paths, as well as other parameters needed to run a qiime2 method or visualizer
The general format is qiime [plugin_name] [method_name] --help

**Example**
qiime diversity beta --help

### Tell a qiime method or visualizer where a file is
This is usually done by supplying the *relative path* to that file (e.g. how to get from your current working directory to the location of that file).
The help for qiime feature-table filter-samples describes  the --i-table parameter like this:
```
 --i-table ARTIFACT FeatureTable[Frequency¹ | RelativeFrequency² |
    PresenceAbsence³ | Composition⁴]
                       The feature table from which samples should be
                       filtered.                                    [required]
```
This means that as part of the command we will write `--i-table` then a space then the relative path to whatever feature table `.qza` file we want to input into that method (which depends on our specific analysis and what we want to accomplish). Here's an example of how this might look within a full command:
```
qiime feature-table filter-samples --i-table ../input/feature_table_decon_all_1000.qza --m-metadata-file ../input/GCMP_EMP_map_r29.txt --p-where "[sample_type_EMP]='coral'" --o-filtered-table ../output/feature_table_decon_all_1000_coral_only.qza
```                       
                  

### Download a file
Download a file called GSMP_metadata.txt from the zaneveld account on klone.

To download a file, from a command line interface with scp installed (e.g. Terminal on MacOSX or Git Bash on Windows), you can use the scp command *on your local computer*. The format is `scp` [username]@[server]:[filepath] [destination] where each thing in brackets gets replaced by the username, server address, filepath, or local destination based on your account and what file you want to copy. 

*Example*
scp zaneveld@klone.hyak.uw.edu:/gscratch/zaneveld/bbio495_2022/GSMP_sponge_data/GSMP_metadata.txt ./GSMP_metadata.txt 

*Notes*
This is useful for downloading .qzv files you generate with qiime2.

### Upload file
copy file to server from local directory (with large files, I got better speeds by using wget to grab files from the web rather than transferring from local. UW network was also faster than home wifi (by an order of magnitude)):  
scp [source filepath] [destination directory]  

scp ./GSMP_metadata.txt zaneveld@klone.hyak.uw.edu:/gscratch/zaneveld/bbio495_2022/GSMP_sponge_data

## General Notes

### Request nodes and run a script (for longer scripts)
You can also write out commands into a file and run them using slurm. 
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

# CRITICAL NOTE: DO NOT RUN THE QIIME2 TUTORIAL DIRECTLY ON THE LOGIN NODE. You **MUST** first get an interactive node using salloc.

### view your active compute nodes  
squeue -u [netid]

### view all active compute nodes
squeue -A zaneveld

### rejoin compute node already allocated  
sattach [jobid].0


### Using containers  
#Conda, the package manager used by QIIME2 to collect the dozens of packages needed, generates a truly massive amount of small files, which causes headaches for the Hyak administrators. To get around this, we use a container, which (among other more important things) collects all those files into a single object as far as Hyak is concerned.  
#We've built a container with the current build of QIIME2 and jupyter notebook located at /gscratch/zaneveld/conda/qiime2-2023.5-jupyter.sif. Basically, anytime that you need access to qiime2, instead of activate qiime2, you instead launch the container with  
apptainer shell –bind /gscratch /gscratch/zaneveld/conda/qiime2-2023.5+.sif  

#activating the conda environment is built into the container, so you should be dropped in, ready to go  

#when you want a non-interactive container (say, sbatch or jupyter notebook, below) you can use  
apptainer exec --bind /gscratch /gscratch/zaneveld/conda/q2-2023.5+.sif [commands to run in the container]  

#in this manner, you can build an sbatch script to activate the container and then run a shell or python file
#with qiime2 commands

### working with Jupyter Lab / Jupyter Notebook  
#The Hyak docs have a reasonable tutorial here: https://hyak.uw.edu/docs/tools/jupyter
#I've modified things a little bit to streamline them and make them work with our container

#Briefly:  
#Request a compute node with salloc:  
salloc -p compute-bigmem --time=0-4 --mem=10G  

#(1st time) Generate a config file to reduce the work you have to do each time to start the server:  

#activate the container to gain access to the jupyter notebook installation:  
apptainer exec –bind /gscratch /gscratch/zaneveld/conda/qiime2-2023.5+.sif  

#set a password so you don't have to keep track of authentication tokens
jupyter-notebook password  

#you can also edit the config rather than specifying these parameters every time you launch your server:  
jupyter-notebook --generate-config  
vim ~/.jupyter/jupyter_notebook_config.py  

#uncomment and change these variables
c.NotebookApp.port = 9195  
c.NotebookApp.ip = '0.0.0.0'  
c.NotebookApp.notebook_dir = '[starting_directory]'  


#exit the container, you're done with the setup stuff
exit

#in the compute node, activate the container, but pass the jupyter notebook start command with the container command.  
#the port number can be any unused port on your computer; the tutorial suggests 9195. If you need multiple servers for some  
#reason, you can increment the port to avoid collisions  

apptainer exec –-bind /gscratch /gscratch/zaneveld/conda/q2-2023.5+.sif jupyter notebook --no-browser --ip 0.0.0.0 --notebook-dir [startind_directory] --port [port_number]  

#stdout gives you a bunch of info. You're interested in the address at which Jupyter Notebook is running:  
#"Jupyter Notebook 6.4.5 is running at:"  
#"http://n3358:9195/"  
#for instance. if this address has a "token=[long hex string]" at the end, you haven't set a password and this is the token  
#the address is just the node followed by the port  
  
#in a different terminal:  
ssh [netid]@klone.hyak.uw.edu -L [port]:[node]:[port]  
  
open a browser and jupyter notebook should be at http://localhost:[port]
