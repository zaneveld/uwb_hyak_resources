# Using GitHub on the Server

To use github on the Hyak server, you need to do a couple steps to connect your local git to your GitHub account.

## General Git and GitHub information
This tutorial assumes you already have command line git setup locally and are familiar with using it. That's probably annoying as you may not be. 
For more detailed background on using git and GitHub generally, including an illustrated workflow for a standard project update cycle, check out the GitHub
chapter in Full Spectrum Bioinformatics: https://github.com/zaneveld/full_spectrum_bioinformatics/blob/master/content/05_project_design/working_collaboratively_with_github.ipynb

## Telling git who you are.

GitHub needs to know who you are so it can tell whether you have permission to commit changes to projects in your online account. 
The main way GitHub is able to connect the user who is logged into a compute cluster with a GitHub user is using public and private keys.
There is a private-key public key pair that you generate on the server and protect with a password. These are just very short text files.
The public half then gets uploaded to GitHub.

## Steps 



1. Set up a GitHub account if you haven't already
2. Log into the server
3. Set your username in git on the server to be your GitHub username
`git config --global user.name "zaneveld"`
4. Set your email on the server to be your GitHub email
`git config --global user.email "your_email_here@gmail.com"`
5. Generate public and private keys on the server using ssh-keygen
`ssh-keygen -t ed25519 -C "your_email@example.com"`
6. Start ssh-agent
`eval "$(ssh-agent -s)"`
7. Add your keys to ssh-agent. Note they by default show up in a hidden .ssh folder on mmfs1 in your home directory.
`ssh-add ~/.ssh/id_ed25519`
8. Download the *public* key you generated **to your local computer**. Replace the username and path with your username.
`scp zaneveld@klone.hyak.uw.edu:/mmfs1/home/zaneveld/.ssh/id_ed25519.pub ./`
9. Copy the contents of the file to your clipboard:
`cat id_ed25519.pub`
(You might have to manually copy any text that appears)
9. Go to GitHub, log in. Go to your account by clicking on your picture in the upper right then clicking on Settings. 
Go to SSH and GPG keys. Hit New SSH Key and paste in your public key
8. Back on the server, use Git Clone to grab your project
`git clone git@github.com:zaneveld/Holobiont_Models.git`
9. Now you're ready to go! Follow the instructions here to practice doing one project change: 
make sure everything is up to date,
then create a new named branch, 
add files you change, 
commit changes, 
push to GitHub to generate a pull request,
merge the pull request on GitHub.

See graphical summary here:
https://github.com/zaneveld/full_spectrum_bioinformatics/blob/master/content/05_project_design/working_collaboratively_with_github.ipynb



