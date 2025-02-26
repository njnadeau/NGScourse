*APS Advanced Stats Delivery*
# Introduction to the HPC and NGS data - Day 1
#### Nicola Nadeau, Alison Wright

The aim of this practical is to get you started using the HPC and looking at some NGS data

## 1. Logging in and getting started
If you are working on a Windows machine For Windows PCs, we recommend the use of Windows Terminal which is available on the University’s managed desktops by default. If not already installed on your device, Windows Terminal can be found on the Microsoft Store. If you are using a different operating system there are instructions [here](https://docs.hpc.shef.ac.uk/en/latest/hpc/connecting.html#suggested-ssh-clients&gsc.tab=0). Start by opening the terminal, then run the following command to log in to bessemer
```
ssh -X YOUR_USERNAME@bessemer.shef.ac.uk
```
specify your username. You will then be prompted for dual-factor authentication.

Access a worker node:
```bash
srun --pty bash -l
```
You should always start by doing this. No work should ever be done on the head node! If you are on a head node you will see someting like this in your command line prompt:
```
[bo1nn@bessemer-login1 ~]$
```
This node is just a gateway to the worker nodes. If you are on a worker node you will see the name of the node, eg.
```
[bo1nn@bessemer-node004 ~]$
```

## 2. Getting to grips with Linux and setting up your bash profile to access the software repository
First lets get used to finding your way around. So where are you now in the file system? 
```bash
pwd
```
Every Unix operating system has a root folder simply called ```/```. Let’s see what’s in it using the command to list information about files.
```bash
ls /
```
Try pressing the up arrow key. What does this do?

List the files in the current directory, i. e. your home directory: 
```bash
ls
```
Your home directory might look empty, but is it? 
```bash
ls -a
```
The ```-a``` switch makes ```ls``` show hidden files, which start with a dot in their file name.
You might notice that the contents of the directory you are currently in are also displayed on the right hand panel in MobaXterm and you can use the icons and address bar to navigate the directories in bessemer. 

We are going to edit the file ```.bash_profile``` to configure your account so that you have access to the Genomics Software Repository that we will be using. The Genomics Software Repository is basically a collection of ready-to-use programs for NGS and population genetic/genomic analysis in a shared folder. Its main advantage is that it removes the necessity for the user to install commonly used programs in their home folder.

You can have access by adding the following to the end of your bash profile. The .bash_profile is a configuration file for configuring user environments e.g. it contains all the startup configuration and preferences for your command line interface.

```
if [[ -e '/usr/local/extras/Genomics' ]]; 
then
    source /usr/local/extras/Genomics/.bashrc
fi
```
To do this we will use the program ```nano```, which is a simple text editor that can be used from the command line
```bash
nano .bash_profile
```
You will see something like
```
# .bash_profile

# Get the aliases and functions
if [ -f ~/.bashrc ]; then
        . ~/.bashrc
fi

# User specific environment and startup programs

PATH=$PATH:$HOME/bin

```
Copy the text you want to insert from above and paste it at the end. You will need to use the arrow buttons to move the cursor within the file (the mouse doesn't work here) and the right mouse button can be used to paste to the command line (^V doesn't work in Linux). Usefully, you will see there is information at the bottom of the screen that tells you how to save the file (ctrl o, save it with the same name) and exit (ctrl x).

Another way of viewing the content of a text file is with ```less```. Unlike ```nano``` less is just a viewer, not an editor. It is particularly useful for viewing very large files, because it only shows them a few lines at a time. To check that your edit has worked and your ```.bash_profile``` file looks OK type in
```bash
less .bash_profile
```
Check that the Genomics Software Repository path is there in the file (you may need to use the arrow keys to move to the end of the file). It should look something like this
```
# .bash_profile

# Get the aliases and functions
if [ -f ~/.bashrc ]; then
        . ~/.bashrc
fi

# User specific environment and startup programs

PATH=$PATH:$HOME/bin

if [[ -e '/usr/local/extras/Genomics' ]];
then
    source /usr/local/extras/Genomics/.bashrc
fi
```
Type ```q``` to quit less.
There are many options that can be used with ```less``` in particular to control how many, and which, lines are shown and for searching the text. You can see what these are using
```bash
man less
```
Adding ```man``` before most Linux commands will show you the manual page. The manual itself is opened using ```less``` so you can try out some of the options while you're there. 
What does ```z``` do?  
#### Table of useful ```less``` options

| Keyboard | What it does |
| ------- | ---- | 
| up arrow | one line up |
| down arrow | one line down |
| f or Space | one screen size down |
| b | one screen size up |
| d | half a screen size down |
| u | half a screen size up |
| G | jump to end of file |
| g | jump to beginning of file |
| h | get help |
| q | exit help |
| q | exit less |


To check that the setup worked, we will need to log out of the hpc and then log in again. Log out of the working node first by typing ```logout``` and then ```logout``` again to exit bessemer. 

## 3. Navigating, creating directories and moving files
Log in again (starting a new ssh session) and start a new interactive session (```srun --pty bash -l```). You should then see
```
  Your account is set up to use the Genomics Software Repository
```
You should now see this every time you log in and start a new interactive session.

We are going to create a working directory in the /fastdata directory on bessemer for you to work within during this practical. The fastdata directory is useful because there are no limits to the amount of data you can put there and data there can also be accessed faster (by the machine) than elsewhere on the cluster. However, it is not meant for long term storage of your data and everything on fastdata is automatically deleted after 3 months. It is also good practice for you to delete any data you no longer need, so as not to clog up fastdata for everyone else. If you are working with large data sets for your research project, your supervisor will probably be able to give you access to their group storage area (in ```/shared```) and you also have ```/data``` directory with a file limit of 100GB. Read more about filestores here:
https://docs.hpc.shef.ac.uk/en/latest/hpc/filestore.html

Move to the fastdata directory:
```bash
cd /fastdata
```
Why is the ```/``` there? What happens if you put ```cd fastdata``` instead? Why is this? What happens if you enter ```cd /fast``` and then press Tab? This is called tab completion and is extremely useful, particularly for long file or folder names!

Create a new directory here that is named your username (ie. boxxx, replacing the xxx)
```bash
mkdir boXXX
```
Navigate into your new folder (hint ```cd```). The data that you are going to be using is in the folder ```/shared/genomicsdb2/shared/workshops/NGS_AdvSta_2024/day1```. View the contents of this folder (hint ```ls```). You will see there is a folder here called ```raw```, view the contents of this folder. We want to copy the whole of the ```day1``` directory to your fastdata folder. This may take a couple of minutes, don't expect it to finish immediately.
```bash
cp -r /shared/genomicsdb2/shared/workshops/NGS_AdvSta_2024/day1 .
```
The ```.``` here simply means "use the exisiting names". Because we were in the directory where we wanted to copy the files, we didn't need to specify the path but this would do the same thing: 
```bash
cp -r /shared/genomicsdb2/shared/workshops/NGS_AdvSta_2024/day1 /fastdata/boXXX/.
```
Why do we need the ```-r``` (hint check ```man cp```)? What happens if you leave it off?

Navigate into the directory you just copied (```day1```) that should now be in your fastdata folder. This contains a folder called ```raw```, which contains folders containing mutliple sequence read files generated from 4 samplea (```60A```,```60I```,```61A```,```61I```). Sequence data is  often generated like this and you may well get multiple read files back for each sample you send off for sequencing. To move back up a level
```bash
cd ../
```
How would you move up 2 levels? What is another way of doing this?
## 4. Verifying the intergrity of your files and submitting batch jobs to the cluster
As well as the sequence data files (which end in ```.sanfastq.gz```) there are also files that end in ```.md5```. List just the sequence files:
```bash
ls *.sanfastq.gz
```
What does the ```*``` do here?

The ```.md5``` files were generated with the command ```md5sum``` by the sequencing centre. This generates a unique string of characters based to the characteristics of the file. You can view these with ```less```. If we regenerate this code (hash) then it should be exactly the same as the code generated by the sequencing centre. If it is different then this indicates that the file has become corrupted or not copied completely, which can happen when it is being moved or dowloaded. 

We are going to create a job to submit to the cluster that will calculate the md5 hash for all 6 data files for this individual. 

You will find a file called ```MD5calc.sh``` in the ```day1``` folder.
Open it with ```nano```
```
#!/bin/bash
# Rename the job's name
#SBATCH --job-name=MD5.1
# Change the name of the output log file.
#SBATCH --output=MD5.txt
#request 1 node
#SBATCH --nodes=1
#run 1 task per node
#SBATCH --ntasks-per-node=1
# Request 1 core
#SBATCH --cpus-per-task=1
# Request 2 gigabytes of real memory (RAM)
#SBATCH --mem-per-cpu=2GB
#set runtime to max 2 hours
#SBATCH --time=2:00:00
# Email notifications to user@sheffield.ac.uk
#SBATCH --mail-user=username@sheffield.ac.uk
# Email notifications if the job fails
#SBATCH --mail-type=FAIL

#insert your email address in the field above you will then receive email notifications if the script fails for any reason

#the script below will run md5sum on all files ending in .sanfastq.gz in the directory raw/60A (within the directory you are currently in)

 for i in raw/60A/*.sanfastq.gz; do md5sum $i; done
```
The lines starting with ```#SBATCH``` set various options deterning how the job will run in the cluster. Check what these mean here (bessemer uses SLURM Commands):
[https://docs.hpc.shef.ac.uk/en/latest/hpc/scheduler/index.html#batch-jobs](https://docs.hpc.shef.ac.uk/en/latest/hpc/scheduler/index.html#gsc.tab=0)

The ```#SBATCH --mail-user``` option mean that it will send an email when the script starts, stops or aborts. You need to add your email address after the ```#SBATCH --mail-user=``` option. The save the file and exit.

What does this part do ```for i in raw/60A/*.sanfastq.gz; do md5sum $i; done```?

*Answer* It runs a ```for``` loop, where for every value of ```raw/60A/*.sanfastq.gz``` (same as running ```ls raw/60A/*.sanfastq.gz```, try this if you're not sure what it means) it runs ```md5sum```. This script runs the commands in serial, where each command is run on a file one after the other. This works OK here where the command is fairly quick to run, but if we were running something  longer it would take too long to run one after the other. In the next section we will look at running things in parallel, where each command (or file/sample in our case) runs on a different node on the cluster.

Run the script:
```bash
sbatch MD5calc.sh
```
Check the jobs you have running
```bash
squeue --me
```
You should see something like this
```
             JOBID PARTITION     NAME     USER ST       TIME  NODES NODELIST(REASON)
           3465904 interacti     bash    bo1nn  R      23:35      1 bessemer-node001
           3465915 sheffield    MD5.1    bo1nn  R       0:06      1 bessemer-node004
```
The first job is your interactive session, the second is the job you just submitted. ```R``` is good and means it is running. Check the manual page for ```squeue``` to see what the other state codes mean. You should also receive an email to tell you that your job has started (end when it has ended). 

Once the job has ended you should find that you have a new file in your directory. View the contents of this (hint ```less```). Hopefully you will have one called ```MD5.txt``` that contains the calculated md5 sum values for each sequence file. 

If you start a job running but find a mistake or decide you want to cancel it, you can cancel jobs with ```scancel job-id```

You can compare the values you calculated to the ones calculated by the sequencing centre. The easiest way to do this might be to download your ```MD5.txt``` file so you can open on your computer and view it side-by-side with the values in the ```.md5``` files. You can do this using use [SCP](https://docs.hpc.shef.ac.uk/en/latest/hpc/transferring-files.html) from the terminal. Open a new terminal window. Use SCP to copy the ```MD5.txt``` from your fastdata directory onto a directory on your computer (e.g. if you have made a folder called "NGScourse in your "Documents" directory, replacing USERNAME with your username):
```bash
scp USERNAME@bessemer.shef.ac.uk:/fastdata/USERNAME/day1/MD5.txt /Users/USERNAME/Documents/NGScourse

```
You will get a password and Duo prompt.

### Exercise
Try to use the command ```cat``` to view all the ```.md5``` files in ```raw/60A``` at once (hint, you can use ```*``` to save typing out every file name)


Return to the main course page:
https://github.com/njnadeau/AdvDataAna-introNGS
