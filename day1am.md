*APS Advanced Stats Delivery*
# Introduction to the HPC and NGS data - day 1 morning
#### Nicola Nadeau, Alison Wright, Victor Soria-Carrasco

The aim of this practical is to get you started using the HPC and looking at some NGS data

## 1. Logging in and getting started
We will be working on windows machines, which means that you need to use a program (ssh client) to access the cluster. We will be using MobXterm. Start by opening the program, if you have used it before to connect to sharc you may find "sharc.sheff.ac.uk" under "User sessions", in which case you can just double click on this to launch an ssh session on sharc. If not, click on "Session">"SSH" and enter
```
sharc.sheffield.ac.uk
```
in the "Remote host" box and specify your username (port should always be 22).

Request an interactive session:
```bash
qrsh
```
You should always start by doing this. No work should ever be done on the head node! If you are on a head node you will see someting like this in your command line prompt:
```
[bo1nn@sharc-login1 ~]$
```
This node is just a gateway to the worker nodes. If you are on a worker node you will see the name of the node, eg.
```
[bo1nn@sharc-node004 ~]$
```

## 2. Creating directories and moving around
First lets get used to finding your way around. So where are you now in the file system? 
```bash
pwd
```
Every Unix operating system has a root folder simply called ```/```. Let’s see what’s in it using the command to list information about files.
```bash
ls /
```
List the files in the current directory, i. e. your home directory: 
```bash
ls
```
Your home directory might look empty, but is it? 
```bash
ls -a
```
The ```-a``` switch makes ```ls``` show hidden files, which start with a dot in their file name.

We are going to create a working directory in the /fastdata directory on sharc for you to work within during this practical. The fastdata directory is useful because there are no limits to the amount of data you can put there and data there can also be accessed faster (by the machine) than elsewhere on the cluster. However, it is not meant for long term storage of your data and everything on fastdata is automatically deleted after 3 months. It is also good practice for you to delete any data you no longer need, so as not to clog up fatsdata for everyone else. If you are working with large data sets for your research project, your supervisor will probably be able to give you access to their group storage area (in /shared).

Move to the fastdata directory:
```bash
cd /fastdata
```
Why is the ```\``` there? What happens if you put ```cd fastdata``` instead? Why is this?

Create a new directory here that is named your username (ie. boxxx) replacing the xxx below
```bash
mkdir boXXX
```

