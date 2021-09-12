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

request compute node (activate screen (https://linuxize.com/post/how-to-use-linux-screen/) first; it doesn't seem like it can be activated from a compute node):  
srun -A zaneveld -p compute-bigmem --time=[hours:minutes:seconds] --mem=[memory amount]G --pty /bin/bash

view your active compute nodes:  
squeue -u [netid]

view all active compute nodes:
squeue -A zaneveld

rejoin compute node already allocated:  
sattach [jobid].0
