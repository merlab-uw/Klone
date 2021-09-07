# Notes about using MerLab's HPCC Node on Klone

## Background information on the node

The MerLab has purchased a dedicated node on Klone, the latest of UW's High Powered Computer Cluster (HPCC) systems. The other two HPCC systems are Hyak and Mox. All three systems are independent of each other. Because Hyak was the first of UW's HPCC systems, sometimes Klone is refered to as Hyak. Merlab has never had a node on the actual first generation Hyak- so if you hear Hyak in our lab, they're refering to Klone. (The [tutorial by Nam](https://github.com/merlab-uw/Klone/blob/main/Using_Hyak_tutorial_by_Nam_Pho.md) is an example where the name Hyak is used for the UW's HPCC system as a whole and also when referring specifically to Klone.) 

For a great overview of High Performance Computing, watch [Introduction to HPC Computing: a practical tutorial](https://youtu.be/fkpofukvGeg)

These are the specifications of our node:

| Model                      |        CPU           | Total Cores                     | RAM                 |
|:---------------------------|:--------------------:|:-------------------------------:|:-------------------:|
| Large memory  | Intel Xeon Gold 6230 | 40 (20 cores * 2 chips) |              768 GB |



The organization and documentation of our node is based on those from the Roberts lab. [This is the Roberts lab github wiki for their HPCC node](https://github.com/RobertsLab/hyak_mox/wiki)

## Bioinformatics Tutorials

There are some PDF tutorials in our [MerLab google drive, in the Tutorials & Workshop Materials / Bioinformatics folder](https://drive.google.com/drive/u/1/folders/1TAkpMu3b0eNANPtQv67VoiDA7DF5ji6m)


## UW's Klone Documentation
### To learn how to use Klone, start with the UW's documentation. They will walk you through every step of the process, from making sure you have an account to transfering your files and setting up your first scripts. The information on our github is meant to be supplemental to the UW's documentation, focusing on tips and tricks we've learned and examples of our own code that has successfully run on Klone. 

* [Updated documentation](https://uwrc.github.io/docs/)


## Node Storage Capacity

### User-specific storage

* Storage allocation: 10GB (or 250,000 files)
* Located on your login node (e.g. /mmfs1/home/UWnetID)
* For personal data, scripts, and other small files, or files you don't want potentially changed by others.

### Group-specific storage

* Storage allocation: 1 TB (or 2,475,000 files)
* Located: /gscratch/merlab/
* Shared by all merlab members
* Contains folder with genomes
* Contains folder with software

### Temporary storage (Scrubbed Drive)

* Storage allocation: 200TB (or 200,000,000 files).
* Located: /gscratch/scrubbed/
* Shared by all Hyak (klone) users.
* Files are automatically deleted 21 days after last accessed: the file must be read/opened/used within 21 days or it is deleted

## Directory Structure and Organization

### Individual Project Storage

 A project should be considered individual if just one person is working on it. Individual projects in our group-specific storage are named by the person working on the project (/gscratch/merlab/your_uw_id). Within that directory, create subdirectories that suit your needs. Some examples:

* _jobs_ where job scripts live. These could be named based on date. For example Steven Roberts uses _MMDD-HHMM.sh_ format.
* _data_ data you are acting on.
* _analyses_ where output goes. Steven uses MMDD subdirectory naming structure. This is where he points to a working directory in the job script header.
* _blastdb_ where blast databases live.

### Shared project Storage

A shared project is one that two or more lab members are accessing or will access. Shared projects should be named by the project, for instance: pcod_zp3 or pollock_wgs. The subdirectories should be named intuitively.

* _jobs_ where job scripts are kept
* _data_ data you are acting on
* _analyses_ where output goes
* _blastdb_ where blast databases are

### Other folders within our shared merlab home directory

* _genomes_ where genomes are kept that are used in multiple projects
* _software_ where we can install software that is used by many projects
* _singularity_ where singularity images are kept

## HPCC resource etiquette

Don’t fill up our shared home directory with data or output from analyses, use the /gscratch/scrubbed/ storage instead.
*** Set a limit for the amount of storage allocated to each person/project directory in the shared merlab home directory, and stick to it.

With a single dedicated node and many active projects, it is imperative that you only request the resources you require to run the job so that others can share the remaining resources. For this reason, also check in with other lab members if you are starting a particularly large job that will tie up the majority of the resources for a couple of days.

Here is an example of a slurm command to run a job for 7 hours using 30 nodes, but only 100G of memory

```bash
srun -A merlab -p compute-hugemem --time=7:00:00 -n 40 --mem=100G --pty $0
```

To see what resources are currently in use, use any of these slurm commands

```bash
sinfo -p ckpt -O partition,nodehost,cpus,memory
sinfo -p ckpt -O partition,nodehost,CPUsState,freemem,memory
```

And you can run some variant of this command and make sure that ReqCPUs adds up to < 40 and ReqMem adds up to < 768G before your merlab account / compute-hugemem partition jobs start to pend. 

```bash
sacct -a -X -o user,jobid,elapsed,reqcpus,reqmem,nnodes,account,partition,state,node -A merlab -s running,pending
```

Which will give you an output like this

```bash 
User JobID Elapsed ReqCPUS ReqMem NNodes Account Partition State NodeList
---------------------------------------------------------------------------------
elpetrou 1037  00:22:40 24  20Gn   1     merlab compute-h+    RUNNING     n3077
```
## Installing programs on Klone
The [tutorial by Nam](https://github.com/merlab-uw/Klone/blob/main/Using_Hyak_tutorial_by_Nam_Pho.md) gives an example of how to download and install programs as well as how to use singularities and programs that are already installed on Klone as modules. The github page [HowToUseSingularities](https://github.com/merlab-uw/Klone/blob/main/HowToUseSingularities.md) gives more information on how to use singularities to run programs on Klone. 

### Using the miniconda programs that are already installed on Klone
We have already installed some programs on Klone using miniconda3, and others are installed as environments. Here is a list of the conda enviroments that are available (as of Sep 2021).
```admixture_env  bwa_env    multiqc_env  
pcangsd   picard_env  samtools_env
angsd_env   gatk3_env  ngsLD_env    
pcangsd_env  plink_env   stacks_env
```

The miniconda3 installation is located in /gscratch/merlab/software. 
In order to use those programs or environments, you must add the miniconda path to your environment. 
From a terminal that is logged into Klone, open your .bash_profile using the program vim. Vim is a text editor that allows you to make changes to text files and save them within the terminal. 

```bash
vim ~/.bash_profile
```
Type the lowercase letter *i* to be able to edit the document. Use the arrow keys on your keyboard to navigate in the document, and add the line <export PATH=$PATH:/gscratch/merlab/software/miniconda3/bin/> to the end of the document, so your /.bash_profile looks like this one: 

```bash
# .bash_profile

# Get the aliases and functions
if [ -f ~/.bashrc ]; then
        . ~/.bashrc
fi

# YOUR environment and startup programs
export PATH=$PATH:/gscratch/merlab/software/miniconda3/bin/
```

To save your changes and exit, hit your *esc* key and then type *:wq!* to write the edits to the file and close the document. Test to see if the changes worked by typing *bowtie2* into the terminal. If you get the help files for the program, the conda path was successfully added to your environment. If not, log out and log back into Klone and see if it worked, or look at your /.bash_profile to make sure the path was successfully added there. 

After adding the path to the conda installation to your /.bash_profile, you can run the programs that are installed as environments using the following set of commands. First you will have to configure conda for your terminal. Use the command:

```bash 
conda init bash
```
You will get this message *==> For changes to take effect, close and re-open your current shell. <==* Close and re-open a new terminal and try to activate a conda environment to see if it worked. I'm testing to see if I can activate the *samtools* conda environment and run samtools.

``` bash
conda activate samtools_env
```
You should get the name of the environment before the tag of your NetID on the command line, if it worked. It should look like this: 

``` bash 
(base) [ctarpey@klone1 ctarpey]$ conda activate samtools_env
(samtools_env) [ctarpey@klone1 ctarpey]$ 
```
Test out that you can run samtools by typing *samtools* in the command line; it should pull up the samtools help pages. 

### Running programs from a conda environment in an sbatch script

For more general information on sbatch scripts, see the page [HowToUseSlurm.md](https://github.com/merlab-uw/Klone/blob/main/HowToUseSlurm.md)
To use the programs that were installed using conda and are now conda environments, you should *source* the installation of conda in your sbatch script. Then activate the conda environment that you want to use. You can do that with these two commands: 

```bash 
source /gscratch/merlab/software/miniconda3/etc/profile.d/conda.sh
conda activate <name_of_conda_environment>
```

When you have finished with the conda environment, deactivate it with the command *conda deactivate*

This is what running a program using a conda environment would look like in an sbatch script. Here, Eleni has specified the directories and other inputs with shell variables, which are specified in the section *ENVIRONMENT SETUP* and later called in the script with the prefix *$*. She makes sure that nothing else is running with the command *module purge*, then sources the conda installation, and finally activates the conda environment she wants to run, *samtools_env*. Once the samtools environment is loaded, she moves to the working directory and runs the commands she wants for her analysis with samtools. When the analysis is complete, the output files are moved to a separate directory and the conda environment is deactivated. 

```bash
#!/bin/bash
#SBATCH --job-name=pollock_samtools
#SBATCH --account=merlab
#SBATCH --partition=compute-hugemem
#SBATCH --nodes=1
#SBATCH --ntasks-per-node=16
## Walltime (days-hours:minutes:seconds format)
#SBATCH --time=6-12:00:00
## Memory per node
#SBATCH --mem=100G
#SBATCH --mail-type=ALL
#SBATCH --mail-user=elpetrou@uw.edu

##### ENVIRONMENT SETUP ##########
## Specify the directory containing data
DATADIR=/gscratch/scrubbed/awray130/sam #directory with sam files
SUFFIX1=.sam #file suffix
OUTDIR=/gscratch/scrubbed/awray130/bam # where to store output files
MYCONDA=/gscratch/merlab/software/miniconda3/etc/profile.d/conda.sh # path to conda installation on our Klone node. Do NOT change this.
MYENV=samtools_env #name of the conda environment containing samtools software. 

## Activate the conda environment:
## start with clean slate
module purge

## This is the filepath to our conda installation on Klone. Source command will allow us to execute commands from a file in the current shell
source $MYCONDA

## activate the conda environment
conda activate $MYENV

## Move into the working directory and run script
cd $DATADIR

## Run samtools commands. 

for MYSAMPLEFILE in *$SUFFIX1
do
    echo $MYSAMPLEFILE
    MYBASE=`basename --suffix=$SUFFIX1 $MYSAMPLEFILE`
    samtools view -bS -F 4 $MYBASE'.sam' > $MYBASE'.bam'
    samtools view -h -q 20 $MYBASE'.bam' | samtools view -buS - | samtools sort -o $MYBASE'_minq20_sorted.bam'
    samtools index $MYBASE'_minq20_sorted.bam'
done

## Flag explanations for samtools view:
## -b       output BAM
## -h       include header in SAM output
## -q INT   only include reads with mapping quality >= INT [0]
##-F INT   only include reads with none of the bits set in INT set in FLAG [0] (aka when this is set to 4, you remove unmapped reads)

# Move all of the bam files to the output directory
mv *'.bam' $OUTDIR
mv *'.bai' $OUTDIR

## deactivate the conda environment
conda deactivate
```


## Other Klone Specific Information

If the lab node resources are all in use, you can run your job on resources beyond our dedicated capacity. You'll be using other lab group's unused hardware or a set of nodes that are owned by UW and shared for this reason.  Jobs run this way are subject to pre-emption. That means that your job may never finish or it may be interrupted and not be able restart properly. Usually a job that is run on ckpt will be interrupted every 4 hours to allow other users who have submitted jobs to run theirs for a while. If your program will take more than 4 hours to finish, write the script to keep this in mind, and either have it write the output continuously or have a way to pick up where it was when it was killed.

To run a job on a partition that we do not own, use the same account (-A merlab) but change the partion from the name of our node to ckpt (i.e., -p ckpt instead of -p compute-hugemem). 

If you want to try to run on the ckpt partition, use this command to see how many nodes are idling

```bash
sinfo -p ckpt -O partition,nodehost,CPUsState,freemem,memory
```
