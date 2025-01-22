## Getting started

The most common way of using Hyak is via Secure Shell (SSH). If you're on Mac or Linux, it should already be installed on the terminal. If you're using Windows, things are a little more complicated. You'lll either want to install Git or the Windows Subsystem for Linux (WSL).

The basic command to access Hyak is

`ssh [netid]@klone.hyak.uw.edu`

(Square brackets [ ] mean "replace this with the thing inside the brackets"; here you log in with your UW username)

Once you've authenticated with your password and 2-factor authentication (it will ask you for this every time), you'll be in the login node. The login node is the starting place for your work on Hyak, but anything computationally intensive should not be run here (more on that in the Compute Node section). Mostly the login node is used to move files around, command line text editing, or asking for compute nodes. We'll go through these one at a time.

Your starting point is your home directory. You can find the full filepath to any directory with the command `pwd`. Running that here should show `/mmfs1/home/[netid]`. This contains about 10GB of storage and is yours to play with as you need.

Let's navigate to the UWB Biology directory:

`cd /gscratch/bbiosci`

The storage here is substantially increased (1TB) but is shared between all of UWB Biology.

Let's make a test folder, and a text document inside of it.

`mkdir test_folder`  
`cd test_folder`

Two of the command line text editors are Vim and Nano. Vim is generally considered more powerful, more common, and less user-friendly. Nano is a little more kind, and works fine for light text editing.

`nano new_document.txt`

Type whatever you want, then [ctrl/cmd]+O to save and [ctrl/cmd]+X to quit.

`wget [url]` will download something to your current directory with the same filename as it had on the internet. If you want it to have a new name, or place it somewhere else, you can do that too: `wget [url] -O [path/to/destination/filename]`

We'll clean up our work so that someone else can do this from scratch:

`rm new_document.txt` deletes the text file we created  
`cd ../` moves us out of the test_folder directory  
`rmdir test_folder` deletes the empty folder  

There's no undo or recycling bin on Hyak, so if you're not 100% sure you'll never need it again, it's safer to keep things around until the end of the project. It's also a good reminder that you should have backups of critical files in case something goes wrong with Hyak. To copy files to and from your personal computer to Hyak, you can use `scp [path/to/source/file] [path/to/destination]`  

It's more straightforward to do this from your own computer rather than trying to manage things from Hyak (your own computer probably isn't set up for ssh sessions (and if it is, you don't need my help)).

`exit` will close the login node and disconnect you from hyak.  

scp can access Hyak and place files for you with a little extra syntax:

`scp [path/to/source/file] [netid]@klone.hyak.uw.edu:[path/to/destination/in/hyak]`  

Each time you do this, you'll have to authenticate with your password and 2FA.

## Requesting a compute node

There are two main ways to use compute nodes, either interactively or with a batch command. Interactive is exactly what it sounds like - you tell Hyak the commands you want to run, it runs them, and you can see the results and submit new commands as needed. This is the easiest way to test things out, because you get immediate feedback if you typed something incorrectly.

### Checking which partitions you have access to

The `hyakalloc` command will show you a table of partitions and accounts that you have access to, as well as how many CPUs and GPUs are available on each.

### Starting an interactive session

To get an interactive session with a compute node:

`salloc -A [account] -p [partition] --mem=[memory] --time=[duration]`

