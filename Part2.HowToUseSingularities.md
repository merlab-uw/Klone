# Notes about using software stored in singularities on Klone

To watch an online lecture that introduces singularities and software containers for high-powered computing, [see this YouTube video](https://www.youtube.com/watch?v=vEjLuX0ClN0&t=1276s)

This tutorial will show you how to download and use the bioinformatics program [VCFTools](https://vcftools.github.io/man_latest.html) as a singularity on Klone.

You will learn how to transfer a vcf file containing genotype data from your local computer, filter the vcf file using vcftools, and copy the filtered vcf back to your local computer.  

In the process, you will also learn how to download and use a singularity containing the software VCFTools from singularity hub.

## Step 1. Transfer a vcf file to klone from your local computer

``` bash
# open a UNIX terminal and log into klone using secure shell (ssh) and your UW netID

ssh elpetrou@klone.hyak.uw.edu

# transfer a vcf file to Klone using the secure copy command (scp). scp copies files between hosts on a network, using secure shell (ssh) for data transfer. 

# Specify the directories and file names as arguments 

DIR1=/media/ubuntu/Herring_aDNA/hybridization_capture/merged_analyses/variants_filtered #where the file lives on my local computer
FILE=0002.filt.HWE.tidy.snpid.recode.vcf #the file to be copied
DIR2=elpetrou@klone.hyak.uw.edu:/gscratch/merlab/elpetrou #where I want the file to go on Klone

# Copy the files to Klone with scp

scp $DIR1'/'$FILE \
$DIR2

```

## Step 2. Download an existing singularity (.sif file) from *Singularity Hub* 

[Singularity Hub](https://singularity-hub.org/) is a website that contains ready-made singularities with commonly used bioinformatics software that you can download.

The URI for this particular vcftools singularity is TomHarrop/singularity-containers:vcftools_0.1.16

``` bash

# Navigate to folder where you want to save the singularity in (.sif file)
cd /gscratch/merlab/singularity_sif

# Load singularity module
module load singularity

# Pull the singularity from the web (this example has vcftools)
singularity pull shub://TomHarrop/singularity-containers:vcftools_0.1.16

```

## Step 3. Use the singularity

Log into a Klone terminal and navigate to your personal gscratch directory on Klone, which will be /gscratch/merlab/<username>. For example, my directory is:

```
cd /gscratch/merlab/elpetrou
```
Using the *sinfo* command, take a peek at what partitions/compute nodes are available (and what their names are) on klone:

```
sinfo
```

Submit a request for an interactive session on one of Klone's compute nodes.
This will be done using the *srun* command. The -p argument specifies the partition name (refer to previous step), while the -A argument is our group's name (merlab) on Klone.

```
srun -p compute-hugemem -A merlab --nodes=1 \
--ntasks-per-node=1 --time=01:00:00 \
--mem=20G --pty /bin/bash
```

Note that When you are on a compute node, your username in the terminal will appear to be something like elpetrou@n3077 

Load the singularity module on Klone so you can use the VCFTools singularity that you just saved to Klone.

```
module load singularity
```
### Exercise #1: Using the VCFTools singularity, have VCFTools print its version to the terminal

The exec Singularity sub-command allows you to spawn an arbitrary command within your container image as if it were running directly on the host system
USAGE: singularity [...] exec [exec options...] <container path> <command>

```
MY_SINGULARITY=/gscratch/merlab/singularity_sif/singularity-containers_vcftools_0.1.16.sif

singularity exec \
$MY_SINGULARITY \
vcftools --version 

```

### Exercise #2: Using the VCFTools singularity, filter & retain a small number of individuals in your vcf file

You will use the vcftools --indv command to specify the names of individuals you want to retain in vcf file

```
MY_SINGULARITY=/gscratch/merlab/singularity_sif/singularity-containers_vcftools_0.1.16.sif # name of singularity that I want to use
MY_FOLDER=/gscratch/merlab/elpetrou # path to folder where I have saved the vcf file
IN_VCF=herring.vcf # name of input vcf file
OUT_VCF=herring.filt # base name of output vcf (without .vcf file extendion)

cd $MY_FOLDER

singularity exec \
$MY_SINGULARITY \
vcftools --vcf $MY_FOLDER'/'$IN_VCF \
--indv 2B_13 \ # name of individual to keep in vcf file
--indv 2B_08 \
--indv 2B_10 \
--indv 2B_12 \
--indv 2B_14 \
--indv 2B_19 \
--recode --recode-INFO-all \
--out $MY_FOLDER'/'$OUT_VCF

```

## Step 4. Transfer files back to local computer from klone using the secure copy command (scp)
 
Nota Bene: you have to type these commands into a terminal on your local computer and NOT in a Klone terminal

```
# Specify directories and file names

DIR1=elpetrou@klone.hyak.uw.edu:/gscratch/merlab/elpetrou
FILE=0002.filt.HWE.tidy.snpid.duplicates.recode.vcf
DIR2=/media/ubuntu/Herring_aDNA/hybridization_capture/merged_analyses/variants_filtered

# Move file from supercomputer to local computer:
scp $DIR1'/'$FILE \
$DIR2

```




