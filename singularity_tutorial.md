# Steps to Create a Custom Singularity Instance


# Log in 

ssh [your username here without the square brackets]@klone.hyak.uw.edu

# go to gscratch/zaneveld
cd /gscratch/zaneveld/

# allocate a node
salloc -A zaneveld -p compute-bigmem -N 1 -n 2 --mem=10G --time=1:00:00

## Create a basic_tools singularity definition file
#vim basic_tools.def


## In the file write the below:
```
#Definition File Starts Herre
Bootstrap: docker
From: ubuntu:16.04
%post
    apt -y update
    apt -y install curl git
    
```
## Build the apptainer
apptainer build --fakeroot /tmp/tools.sif ./basic_tools.def

##Move the apptainer to the current directory
mv /tmp/tools.sif .

##Launch the apptainer without external file access
apptainer shell tools.sif

## Launch the apptainer shell with external file access
apptainer shell --bind /gscratch tools.sif
