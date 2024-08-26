
# Getting Started with Hyak

This is a quickstart guide to getting up and running in a QIIME2 environment via SSH to Hyak. Deeper explanations of various aspects of this setup can be found elsewhere.

### Setup SSH
The primary way to interact with Hyak is through Secure Shell, or ssh, which lets us access the command line on a remote machine.

For Windows users, you can install [Git](https://git-scm.com/downloads). This will come with Git BASH, a command line BASH interface, which includes `ssh`.

If you're on Mac or Linux, ssh should already be available in your terminal.

### Login

In order to work on Hyak you'll need to log in. From within either Terminal (MacOSX/Linux) or Git Bash (Windows) type

`ssh [netid]@klone.hyak.uw.edu`

and hit `enter`. [brackets] mean replace the brackets and the text inside with the thing being referenced, so to login use your netid (used to log in to Canvas)

Similar to Canvas, Hyak is protected by 2-factor authentication, so you'll likely need your phone handy to confirm your login (and Hyak will prompt you every time you login).


### Request an interactive compute node

When you log into Hyak, you're interacting with the login node. If you want to do more than move some files around, you probably want a compute node.

request interactive compute node (activate screen ([https://linuxize.com/post/how-to-use-linux-screen/](https://linuxize.com/post/how-to-use-linux-screen/)) first; it doesn't seem like it can be activated from a compute node):  
salloc -A zaneveld -p compute-bigmem --time=[hours:minutes:seconds OR days-hours] --mem=[memory amount]G

_Examples_: salloc -A zaneveld -p compute-bigmem --time=3:00:00 --mem=15G

salloc -A bbiosci -p compute --time=3:00:00 --mem=15G

Use this for short-term tasks

_Notes:_ After getting a node, your cursor will change to say your `username` then `@` then `n` and the node number. For example: `[zaneveld@n3099 bbio495_2022]`

### Activate QIIME2
TK - activate container

### Download a file

Download a file called GSMP_metadata.txt from the zaneveld account on klone.

To download a file, from a command line interface with scp installed (e.g. Terminal on MacOSX or Git Bash on Windows), you can use the scp command _on your local computer_. The format is `scp` [username]@[server]:[filepath] [destination] where each thing in brackets gets replaced by the username, server address, filepath, or local destination based on your account and what file you want to copy.

_Example_ scp [zaneveld@klone.hyak.uw.edu](mailto:zaneveld@klone.hyak.uw.edu):/gscratch/zaneveld/bbio495_2022/GSMP_sponge_data/GSMP_metadata.txt ./GSMP_metadata.txt

_Notes_ This is useful for downloading .qzv files you generate with qiime2.

### Upload file

[](https://github.com/sonettd/uwb_hyak_resources/tree/main#upload-file)

copy file to server from local directory (with large files, I got better speeds by using wget to grab files from the web rather than transferring from local. UW network was also faster than home wifi (by an order of magnitude)):  
scp [source filepath] [destination directory]

scp ./GSMP_metadata.txt [zaneveld@klone.hyak.uw.edu](mailto:zaneveld@klone.hyak.uw.edu):/gscratch/zaneveld/bbio495_2022/GSMP_sponge_data

## General Notes

[](https://github.com/sonettd/uwb_hyak_resources/tree/main#general-notes)

### Request nodes and run a script (for longer scripts)

[](https://github.com/sonettd/uwb_hyak_resources/tree/main#request-nodes-and-run-a-script-for-longer-scripts)

You can also write out commands into a file and run them using slurm. sbatch [filepath].slurm  
instructions for .slurm file here ([https://hyak.uw.edu/docs/compute/scheduling-jobs#batch-jobs](https://hyak.uw.edu/docs/compute/scheduling-jobs#batch-jobs)). Only thing to note is that if you're using qiime2, you'll need to activate the conda environment within the script by adding these lines above your command line or python instructions:  
eval "$(conda shell.bash hook)"  
conda activate [qiime2 environment]


### Using containers


#Conda, the package manager used by QIIME2 to collect the dozens of packages needed, generates a truly massive amount of small files, which causes headaches for the Hyak administrators. To get around this, we use a container, which (among other more important things) collects all those files into a single object as far as Hyak is concerned.  
#We've built a container with the current build of QIIME2 and jupyter notebook located at /gscratch/zaneveld/conda/qiime2-2023.5-jupyter.sif. Basically, anytime that you need access to qiime2, instead of activate qiime2, you instead launch the container with  
apptainer shell /gscratch/zaneveld/conda/q2-2023.5+3.sif

#activating the conda environment is built into the container, so you should be dropped in, ready to go

#when you want a non-interactive container (say, sbatch or jupyter notebook, below) you can use  
apptainer exec --bind /gscratch /gscratch/zaneveld/conda/q2-2023.5+.sif [commands to run in the container]

#in this manner, you can build an sbatch script to activate the container and then run a shell or python file #with qiime2 commands


