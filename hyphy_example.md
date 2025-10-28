# Running HyPhy 

HyPhy can be used to calculate dN/dS ratios. Here are some steps to run it on the Hyak klone server

## Log in to the server

ssh zaneveld@klone.hyak.uw.edu

## `cd` to whatver directory the data are in

cd /gscratch/zaneveld/jesse/hyphy/hyphy_tutorial_files

## access a node to run analysis on
NEVER run major analyses on the head node without grabbing a worker node

salloc -A zaneveld -p compute-bigmem -N 1 -n 2 --mem=10G --time=1:00:00

## access the apptainer shell
Start the apptainer, using --bind to give it access to the files on `/gscratch/`

apptainer shell --bind /gscratch ../hyphy.sif

## Start `hyphy` conda environment inside the apptainer shell
This loads the hyphy software and it's dependencies inside a custom environment (inside the apptainer)

conda activate hyphy

## Run Hyphy
There are *many* possible analyses within hyphy. Only some will be appropriate or meaningful for a given question.
To pick, among options for dN/dS analysis, we need to understand a few things:

- Do we have a lot of data (e.g. 100s or 1000s of sequences) or little data
- Do we just care about selection over a whole gene (easier to test) or do we need to test if it differs
  across sites within the gene
- Do we want to test for selection that is 'pervasive' (meaning continuous over a tree) or 'episodic' (meaning it only happens sometimes)
- Do we want to test for selection overall (e.g. on this tree there was positive selection) or only on certain branches.

The HyPhy website has suggestions for what method to use based on what you want to test and how much data you have:
https://stevenweaver.github.io/hyphy-site/getting-started/

More detailed explanations of selection tests are avialable here:
https://stevenweaver.github.io/hyphy-site/methods/selection-methods/#busted



Check out the HyPhy website and papers cited in hyphy help for more details (https://stevenweaver.github.io/hyphy-site/tutorials/current-release-tutorial/)

With all that said, here is a representative example of the general format for running a hyphy analysis. It depends on having a FASTA-format alignment file called CVV_G.fas and a NEXUS format CVV_G_GARD.nex file in your current working directory. If they were elsewhere, you'd have to replace the file name with the absolute path to their location:

`
hyphy slac --alignment CVV_G.fas --tree ./hyphy/hyphy_tutorial_files/CVV_G_GARD.nex
`

Note that error logs and results .json file may be output in your current working directory. 
As is probably clear, to run a SLAC analysis on data you cared about, you'd have to replace CVV_G.fas with your *aligned* fasta file for a set of homologous sequences and CVV_G_Gard.nex
with the name of your tree file. Basically just filling in the alignment and tree inputs using the `--alignment` and `--tree` command line parameters




