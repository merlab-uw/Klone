# Notes about using singularities on Klone

Sometimes scientists need to package up all of the software needed for their research, and deliver this package to a second scientist so that they can reproduce the work. In High Performance Computing (HPC), this package is known as a software container or a singularity. Singularities are neat because they allow us to easily install bioinformatics software on the Klone supercomputer.

- To watch an online lecture that introduces singularities and software containers for HPC, [see this YouTube video](https://www.youtube.com/watch?v=vEjLuX0ClN0&t=1276s)

This tutorial will show you how to download and use the bioinformatics program [VCFtools](https://vcftools.github.io/man_latest.html) as a singularity on Klone.

You will learn how to:

1. Transfer a vcf file containing genotype data from your local computer
2. Download and use a singularity containing the software *VCFtools* from the website *Singularity Hub*
3. Filter the vcf file using the *VCFtools*, and copy the filtered vcf back to your local computer.

## Step 1. Transfer a vcf file to Klone from your local computer

Let's transfer some data to Klone, so we can manipulate it. In this example, we will transfer the vcf file [herring.vcf](herring.vcf) to a directory on Klone.
First, save [herring.vcf](herring.vcf) to a folder on your local computer.
Then, open a UNIX terminal and transfer the vcf file to Klone using the secure copy command (scp).

``` bash

# scp copies files between hosts on a network, using secure shell (ssh) for data transfer. 
# First, specify the directories and file names as arguments 

DIR1=/mnt/hgfs/D # where the vcf file is saved on my local computer
FILE=herring.vcf # the name of file to be copied
DIR2=elpetrou@klone.hyak.uw.edu:/gscratch/merlab/elpetrou # where I want the file to go on Klone

# Copy the files to Klone with scp

scp $DIR1'/'$FILE \
$DIR2

```
After you type in the commands above, you will be prompted to authenticate yourself as a user on Klone.
To do this, you will need to type in your UW Netid password and use DUO two-factor authentication. 


## Step 2. Log into Klone terminal and download a singularity from *Singularity Hub* 

Next, we will into Klone using the secure shell (ssh) command. The syntax for ssh is: ssh <username>@klone.hyak.uw.edu. 
For example, this is what I type into a terminal:

```
ssh elpetrou@klone.hyak.uw.edu
```
 
 We will download a singularity containing the software *VCFtools* from [Singularity Hub](https://singularity-hub.org/). This is a website that contains many singularities with commonly used bioinformatics software.

 - To learn more about Singularity Hub, [read this manual](https://singularityhub.github.io/singularityhub-docs/#pancakes-getting-started)
 - Nota Bene: To search for and download singularities from Singularity Hub, you will need to sign into Singularity Hub using your GitHub account.

In this example, we will download VCFtools from Singularity Hub from a user named TomHarrop. To do this, we will use the *singularity pull* command, and the singularity's Uniform Resource Identifier (URI): TomHarrop/singularity-containers:vcftools_0.1.16

``` bash

# Navigate to folder where you want to save the singularity in (.sif file)
cd /gscratch/merlab/singularity_sif

# Load singularity module
module load singularity

# Pull the singularity from the web (this example has vcftools)
singularity pull shub://TomHarrop/singularity-containers:vcftools_0.1.16

```
Congratulations! You have downloaded the *VCFtools* singularity to Klone. It is saved to the directory /gscratch/merlab/singularity_sif 
and it has this file name: *singularity-containers_vcftools_0.1.16.sif*

## Step 3. Use the VCFtools singularity

To use the VCFtools singularity you just downloaded, log into a Klone terminal and navigate to your personal gscratch directory on Klone (/gscratch/merlab/<username>). 
For example, my directory is /gscratch/merlab/elpetrou, so this is how I navigate to it:

```
cd /gscratch/merlab/elpetrou
```
Using the *sinfo* command, take a peek at what partitions and compute nodes are currently available (and what their names are) on klone:

```
sinfo
```
You will see something like:

| PARTITION                  |        AVAIL         | TIMELIMIT                     | NODES                 | STATE               | NODELIST          |
|:--------------------------:|:--------------------:|:-----------------------------:|:---------------------:|:-------------------:|:-------------------:|
| compute-hugemem            | up                   | infinite                      | 16                    |idle                  | n[3000-3007,3016-3021,3065,3067]|



If there are idle compute nodes, submit a request for an interactive session on one of them.
This will be done using the *srun* command. The -p argument specifies the partition name (refer to previous step), 
while the -A argument is our group's name (merlab) on Klone.

``` bash

srun -p compute-hugemem -A merlab --nodes=1 \
--ntasks-per-node=1 --time=01:00:00 \
--mem=20G --pty /bin/bash
```
Note that when you are on a compute node, your username in the terminal will appear to be <UWnetid>@<nodename>, like this for example: elpetrou@n3077 


### Exercise #1: Using the VCFtools singularity, have VCFtools print its software version to the terminal

The exec Singularity sub-command allows you to spawn an arbitrary command within your container image as if it were running directly on the host system
USAGE: singularity [...] exec [exec options...] <container path> <command>

``` bash

# Specify the path and the name of the singularity you want to use
MYSINGULARITY=/gscratch/merlab/singularity_sif/singularity-containers_vcftools_0.1.16.sif # specify the path to the singularity you want to run

# Load the singularity module
module load singularity

# Use the singularity exec command to use the singularity and run commands that are specific to the software it contains (VCFtools, in this case)

singularity exec \ 
$MYSINGULARITY \
vcftools --version 
```
In the terminal, you should see this print: 

VCFtools (0.1.16). 

Yay! You just connected to VCFtools using a singularity!


### Exercise #2: Using the VCFTools singularity, filter & retain one individual in your vcf file

We will use the vcftools --indv command to specify one sample that we want to retain in vcf file.

``` bash

# Specify the directory and file names
MYSINGULARITY=/gscratch/merlab/singularity_sif/singularity-containers_vcftools_0.1.16.sif # name of singularity that I want to use
MYDIR=/gscratch/merlab/elpetrou # path to directory where I have saved the vcf file
INVCF=herring.vcf # name of input vcf file
OUTVCF=herring.filt # base name of output vcf (without .vcf file extendion)
KEEPINDIV=Case07_001 # name of individual sample to keep in vcf file

# Go into directory with your vcf file
cd $MYDIR

# Run VCFtools
singularity exec \
$MYSINGULARITY \
vcftools --vcf $INVCF \
--indv $KEEPINDIV \
--recode --recode-INFO-all \
--out $OUTVCF

```
If you type ls you will see that there is a file called *herring.filt.recode.vcf* in your folder!

## Step 4. Transfer files back to local computer from klone using *scp*
 
Open up a new terminal in your local computer and type something like this:

``` bash

# Specify directories and file names
DIR1=elpetrou@klone.hyak.uw.edu:/gscratch/merlab/elpetrou # Path to directory on Klone containing the files to be copied
FILE=herring.filt.recode.vcf # name of file to be copied
DIR2=/mnt/hgfs/D # Path to target directory on local computer

# Move file from supercomputer to local computer:
scp $DIR1'/'$FILE \
$DIR2

```

You should have been able to transfer the file *herring.filt.recode.vcf* to your local computer. Hurrah!


