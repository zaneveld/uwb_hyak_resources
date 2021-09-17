# uwb_hyak_resources
Resources for users of the UWB Hyak nodes

Official Hyak documentation:
https://hyak.uw.edu/docs

Cheat sheet:

Login:  
ssh [netid]@klone.hyak.uw.edu
  
copy file to server from local directory (with large files, I got better speeds by using wget to grab files from the web rather than transferring from local. UW network was also faster than home wifi (by an order of magnitude)):  
scp [source filepath] [destination directory]  
scp /mnt/c/Users/Dylan/Documents/zaneveld/klone/gcmp/procedure/denoise.py /gscratch/zaneveld/sonettd/gcmp/procedure

view available resources:  
hyakalloc

request interactive compute node (activate screen (https://linuxize.com/post/how-to-use-linux-screen/) first; it doesn't seem like it can be activated from a compute node):  
srun -A zaneveld -p compute-bigmem --time=[hours:minutes:seconds OR days-hours] --mem=[memory amount]G --pty /bin/bash
Use this for short-term tasks

request nodes and run a script:  
sbatch [filepath].slurm  
instructions for .slurm file here (https://hyak.uw.edu/docs/compute/scheduling-jobs#batch-jobs). Only thing to note is that if you're using qiime2, you'll need to activate the conda environment within the script by adding these lines above your command line or python instructions:  
eval "$(conda shell.bash hook)"  
conda activate [qiime2 environment] 

view your active compute nodes:  
squeue -u [netid]

view all active compute nodes:
squeue -A zaneveld

rejoin compute node already allocated:  
sattach [jobid].0
