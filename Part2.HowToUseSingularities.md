# Notes about using singularities on Klone

Sometimes scientists need to package up all of the software needed for their research, and deliver this package to a second scientist so that they can reproduce the work. In High Performance Computing (HPC), this package is known as a software container or a singularity. Singularities are neat because they allow us to easily install bioinformatics software on the Klone supercomputer.

- To watch an online lecture that introduces singularities and software containers for HPC, [see this YouTube video](https://www.youtube.com/watch?v=vEjLuX0ClN0&t=1276s)

This tutorial will show you how to download and use the bioinformatics program [VCFtools](https://vcftools.github.io/man_latest.html) as a singularity on Klone.

You will learn how to:

1. Transfer a vcf file containing genotype data from your local computer
2. Download and use a singularity containing the software *VCFtools* from the website *Singularity Hub*
3. Filter the vcf file using the *VCFtools*, and copy the filtered vcf back to your local computer.

## Step 1. Transfer a vcf file to Klone from your local computer

First we need to transfer some data to Klone, so we can manipulate it. In this example, I transfer a vcf file containing genotypes.

``` bash

# Open a UNIX terminal and log into klone using secure shell (ssh). 
# To do this, you will need your UW netID, UW password, and DUO two-factor authentication. 
# Here is how I (elpetrou) do this:

ssh elpetrou@klone.hyak.uw.edu

# Transfer a vcf file to Klone using the secure copy command (scp). 
# scp copies files between hosts on a network, using secure shell (ssh) for data transfer. 
# First, specify the directories and file names as arguments 

DIR1=/media/ubuntu/Herring_aDNA/hybridization_capture/merged_analyses/variants_filtered #where the file lives on my local computer
FILE=herring.vcf #the file to be copied
DIR2=elpetrou@klone.hyak.uw.edu:/gscratch/merlab/elpetrou #where I want the file to go on Klone

# Copy the files to Klone with scp

scp $DIR1'/'$FILE \
$DIR2

```

## Step 2. Download a singularity from *Singularity Hub* 

Next, we will download a singularity containing the software VCFtools from [Singularity Hub](https://singularity-hub.org/). This is a website that contains many singularities with commonly used bioinformatics software.

 - To learn more about Singularity Hub, [read this manual](https://singularityhub.github.io/singularityhub-docs/#pancakes-getting-started)
 - Nota Bene: To search for and download singularities from Singularity Hub, you will need to sign in to Singularity Hub using your GitHub account.

In this example, we will download VCFTools from Singularity Hub from a user named TomHarrop. To do this, we will use the *singularity pull* command, and the singularity's Uniform Resource Identifier (URI): TomHarrop/singularity-containers:vcftools_0.1.16

``` bash

# Navigate to folder where you want to save the singularity in (.sif file)
cd /gscratch/merlab/singularity_sif

# Load singularity module
module load singularity

# Pull the singularity from the web (this example has vcftools)
singularity pull shub://TomHarrop/singularity-containers:vcftools_0.1.16

```
Congratulations! You have downloaded the *VCFtools* singularity to Klone. It is saved to the directory /gscratch/merlab/singularity_sif and it has this file name:
  
  singularity-containers_vcftools_0.1.16.sif

## Step 3. Use the VCFtools singularity

To use the VCFtools singularity you just downloaded, log into a Klone terminal and navigate to your personal gscratch directory on Klone (/gscratch/merlab/<username>). 
For example, my directory is /gscratch/merlab/elpetrou, so this is how I nanavigate to it:

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



If there are free compute nodes, submit a request for an interactive session on one of them.
This will be done using the *srun* command. The -p argument specifies the partition name (refer to previous step), 
while the -A argument is our group's name (merlab) on Klone.

``` bash

srun -p compute-hugemem -A merlab --nodes=1 \
--ntasks-per-node=1 --time=01:00:00 \
--mem=20G --pty /bin/bash
```
Note that when you are on a compute node, your username in the terminal will appear to be <UWnetid>@<nodename>, like this for example: elpetrou@n3077 

Next, load the singularity module  so you can use the VCFtools singularity that you just downloaded.

```
module load singularity
```
### Exercise #1: Using the VCFtools singularity, have VCFtools print its version to the terminal

The exec Singularity sub-command allows you to spawn an arbitrary command within your container image as if it were running directly on the host system
USAGE: singularity [...] exec [exec options...] <container path> <command>

``` bash

MY_SINGULARITY=/gscratch/merlab/singularity_sif/singularity-containers_vcftools_0.1.16.sif # specify the path to the singularity you want to run

singularity exec \ # use the exec command to call the singularity and run commands that are specific to the VCFTools software
$MY_SINGULARITY \
vcftools --version 

```
In the terminal, you should see this: VCFtools (0.1.16). Yay! You are using VCFtools on Klone!


### Exercise #2: Using the VCFTools singularity, filter & retain one individual in your vcf file

This exercise demonstrates how to use VCFtools as a singularity. We will use the vcftools --indv command to specify one sample that we want to retain in vcf file.

```
MY_SINGULARITY=/gscratch/merlab/singularity_sif/singularity-containers_vcftools_0.1.16.sif # name of singularity that I want to use
MY_DIR=/gscratch/merlab/elpetrou # path to directory where I have saved the vcf file
IN_VCF=herring.vcf # name of input vcf file
OUT_VCF=herring.filt # base name of output vcf (without .vcf file extendion)

cd $MY_FOLDER

singularity exec \
$MY_SINGULARITY \
vcftools --vcf $MY_DIR'/'$IN_VCF \
--indv 2B_13 \ # name of individual to keep in vcf file
--recode --recode-INFO-all \
--out $MY_DIR'/'$OUT_VCF

```

## Step 4. Transfer files back to local computer from klone using the secure copy command (scp)
 
Nota Bene: you have to type these commands into a terminal on your local computer and NOT in a Klone terminal. So open up a new terminal and type:

```
# Specify directories and file names

DIR1=elpetrou@klone.hyak.uw.edu:/gscratch/merlab/elpetrou # Path to directory containing the files to be copied
FILE=herring.filt.vcf # file to be copied
DIR2=/media/ubuntu/Herring_aDNA/hybridization_capture/merged_analyses/variants_filtered # Path to target directory

# Move file from supercomputer to local computer:
scp $DIR1'/'$FILE \
$DIR2

```




