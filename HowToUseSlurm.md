# Using the Slurm Job Scheduler on Klone

## Slurm Basics

Slurm is a job scheduler used to provide access to compute nodes on the Klone supercomputer. 
As a cluster workload manager, Slurm has three key functions. First, it allocates exclusive and/or non-exclusive access to resources (compute nodes) to users for some duration of time so they can perform work. Second, it provides a framework for starting, executing, and monitoring work (normally a parallel job) on the set of allocated nodes. Finally, it arbitrates contention for resources by managing a queue of pending work.

- For a quick and useful overview of basic Slurm commands, watch the [Slurm Basics video](https://www.youtube.com/watch?v=49DzPT9HFJM&list=LL&index=2) from the Center for High Performance Computing at the University of Utah. 

### Slurm commands
Manual pages exist for all Slurm commands. To access the help page for slurm, open a Klone terminal and type the following:

```
slurm --help
```
Here is a subset of some useful slurm commands:

- `squeue` reports the state of jobs or job steps
- `sinfo` reports the state of partitions and nodes managed by Slurm.

- `srun` used to submit a job for execution or initiate job steps in real time. srun has a wide variety of options to specify resource requirements, including: minimum and maximum node count, processor count, specific nodes to use or not use, and specific node characteristics (so much memory, disk space, certain required features, etc.). A job can contain multiple job steps executing sequentially or in parallel on independent or shared resources within the job's node allocation.

- `sbatch` used to submit a job script for later execution. The script will typically contain one or more srun commands to launch parallel tasks.

- `scancel` used to cancel a pending or running job or job step. It can also be used to send an arbitrary signal to all processes associated with a running job or job step.

### See Slurm commands in action

To see some examples of how these commands are used, check out the [QuickStart User Guide to Slurm](https://slurm.schedmd.com/quickstart.html)


## Slurm batch scripting

Slurm batch scripting is used when one has a big job that should be run in parallel or there is a need for heavy-duty computation.

- For a great overview of slurm batch scripting, see the [Slurm Batch Scripting video](https://www.youtube.com/watch?v=LRJMQO7Ercw&list=LL&index=1) from the Center for High Performance Computing at the University of Utah.

### Contents of a slurm batch script

A batch script has these general components:


1. **sbatch directives**:
The first line of an sbatch script starts with `#!/bin/bash`, which tells the computer to execute the file using the Bash shell
Subsequent lines start with the `#SBATCH` and provide specific information to the slurm job scheduler, like:
     - how many nodes to use?
     - how many tasks per node?
     - how many cores per node?
     - what account to use?
     - what partition to use?
     - how long should the code be allowed to run?
     - what email account to send notification to when the code starts and ends?
     
2. **environment setup**:
This part of the script is where you:
    - load your modules
    - specify paths to your data
    - specify other path variables (to software, output folders, etc.)
    
3. **code for the job** :
This part of the script has the code that will do the analysis that you desire

### Example of a slurm batch script

Here is an example of a tiny batch script that allowed me to navigate to specific directory on Klone and unzip all of the files that ended in .fastq.gz

 

``` bash
#!/bin/bash
#SBATCH --job-name=elp_02
#SBATCH --account=merlab
#SBATCH --partition=compute-hugemem
#SBATCH --nodes=1
## Walltime (days-hours:minutes:seconds format)
#SBATCH --time=3-12:00:00
## Memory per node
#SBATCH --mem=100G
#SBATCH --mail-type=ALL
#SBATCH --mail-user=elpetrou@uw.edu


## ENVIRONMENT SETUP

DATADIR=/gscratch/scrubbed/elpetrou/fastq/WA_herring


## CODE FOR JOB

for FILE in $DATADIR'/'*.fastq.gz 
do
	gunzip $FILE
done

```

I saved this script as a .sh file called **gunzip.sh** and uploaded it to my home directory on Klone. Then, I ran it like this:

``` 
#Navigate to my home directory
cd ~ 

# Run the sbatch script
sbatch gunzip.sh
```

Some additional notes:

 - Before running the script, I typed `sinfo` into the terminal to see what partitions were available. 
 - After running the script, I typed `squeue --account merlab` to see information about the job and see its job id
 - I also typed `scontrol show job <jobid>` to see very detailed information about the job I was running.

### Example of a slurm batch script that runs software via a singularity

Here is a slightly more complicated example of a sbatch script. In this script, I use singularity to run the software FastQC over all files in a directory that end with the suffix ".fastq". All FastQC output files (which end in the suffixes "_fastqc", ".zip.", and ".html") are subsequently moved to a directory called "fastqc". 

``` bash
#!/bin/bash
#SBATCH --job-name=elp_07
#SBATCH --account=merlab
#SBATCH --partition=compute-hugemem
#SBATCH --nodes=1
## Walltime (days-hours:minutes:seconds format)
#SBATCH --time=8:00:00
## Memory per node
#SBATCH --mem=100G
#SBATCH --mail-type=ALL
#SBATCH --mail-user=elpetrou@uw.edu

##### ENVIRONMENT SETUP ##########
DATADIR=/mmfs1/gscratch/scrubbed/elpetrou/fastq/WA_herring
OUTDIR=/mmfs1/gscratch/scrubbed/elpetrou/fastqc
SAMPLELIST=fastq_list.txt
MYSINGULARITY=/gscratch/merlab/singularity_sif/singularity-fastqc_0.11.9.sif

## Load modules
module load singularity

#### CODE FOR JOB #####

## go into the data directory
cd $DATADIR 

## save list of fastq files in this directory to a text file (for looping later)
ls *.fastq > fastq_list.txt 

## Use the singularity exec command to run fastqc program. The for loop will run fastQC for all files in this directory

for SAMPLEFILE in `cat $SAMPLELIST`
do
	singularity exec \
	$MYSINGULARITY \
	fastqc -f fastq --extract \
	$SAMPLEFILE
done

## Move all the fastqc results files (ending in _fastqc, .zip, .html) to the output directory

for FILE in $DATADIR'/'*.html 
do
	mv $FILE $OUTDIR
done



for FILE in $DATADIR'/'*.zip 
do
	mv $FILE $OUTDIR
done



for FILE in $DATADIR'/'*_fastqc
do
	mv $FILE $OUTDIR
done

```

### How do you know how much memory or CPUs your job is using? 

While the job is running you can look at what resources were allocated (requested) for that job using the command sacct, followed by the job number. Use squeue -A merlab to find the job numbers you're interested in. Once the job has finished, the MaxRSS column will have the maximum memory that was used during the run. The output of the sacct command is not easy to read, so run this command to set the format as an evironmental variable, which makes the output readable. 

```
[ctarpey@klone1 scripts]$ export SACCT_FORMAT="JobID%20,JobName,User,Partition,NodeList,Elapsed,State,ExitCode,MaxRSS,AllocTRES%32"

[ctarpey@klone1 scripts]$ sacct -j 408063
               JobID    JobName      User  Partition        NodeList    Elapsed      State ExitCode     MaxRSS                        AllocTRES
-------------------- ---------- --------- ---------- --------------- ---------- ---------- -------- ---------- --------------------------------
              408063 pollock_b+  elpetrou compute-h+           n3016 2-04:49:28    RUNNING      0:0            billing=16,cpu=16,mem=200G,node+
        408063.batch      batch                                n3016 2-04:49:28    RUNNING      0:0                      cpu=16,mem=200G,node=1
       408063.extern     extern                                n3016 2-04:49:28    RUNNING      0:0            billing=16,cpu=16,mem=200G,node+
```

Another option is to ssh into the node that is running the job, and look at the resources in real time using the command top. Before you do this for the first time, use these commands on the log in node to set up your SSH key: 

```
ssh-keygen -C klone -t rsa -b 2048 -f ~/.ssh/id_rsa -q -N ""
cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys
chmod 600 ~/.ssh/authorized_keys
```
Once you have your key you can use ssh followed by the node number (find this number using squeue or sacct) to enter the node running your job: 

```
ssh n3000
top 
```
If there are many processes running, press the letter u followed by your netID to filter for your jobs. Below, you can see my jobs are not using nearly the amount of memory I requested, instead they are CPU limited. This can help you choose different CPU and MEM values next time!

```
[ctarpey@n3000 ~]$ top
top - 16:15:25 up 31 days,  3:18,  2 users,  load average: 58.45, 57.72, 50.65
Tasks: 542 total,  15 running, 527 sleeping,   0 stopped,   0 zombie
%Cpu(s): 16.8 us,  0.7 sy,  0.0 ni, 82.4 id,  0.0 wa,  0.0 hi,  0.0 si,  0.0 st
MiB Mem : 773706.2 total, 719563.8 free,  49268.1 used,   4874.2 buff/cache
MiB Swap:  22892.0 total,  22892.0 free,      0.0 used. 716916.4 avail Mem

  PID USER      PR  NI    VIRT    RES    SHR S  %CPU  %MEM     TIME+ COMMAND
68187 ctarpey   20   0 2559400   1.4g   3664 R 100.3   0.2  23:59.26 i_baypass
36025 ctarpey   20   0 2108840   1.2g   3432 R 100.0   0.2   1359:19 i_baypass
40524 ctarpey   20   0 2559396   1.4g   3680 R 100.0   0.2   1351:33 i_baypass
68212 ctarpey   20   0 2559396   1.4g   3584 R 100.0   0.2  23:54.50 i_baypass
68255 ctarpey   20   0 2108840   1.2g   3516 R  99.7   0.2  23:32.98 i_baypass
82770 ctarpey   20   0  294540   5968   4572 R   0.3   0.0   0:00.16 top
36018 ctarpey   20   0  229936   3172   2632 S   0.0   0.0   0:00.00 slurm_script
40517 ctarpey   20   0  229936   3232   2692 S   0.0   0.0   0:00.00 slurm_script
68180 ctarpey   20   0  229920   3160   2636 S   0.0   0.0   0:00.00 slurm_script
68205 ctarpey   20   0  229920   3124   2604 S   0.0   0.0   0:00.00 slurm_script
68248 ctarpey   20   0  229920   3196   2676 S   0.0   0.0   0:00.00 slurm_script
81812 ctarpey   20   0  170892   6648   4760 S   0.0   0.0   0:00.00 sshd
81813 ctarpey   20   0  255040   4836   3984 S   0.0   0.0   0:00.00 bash
```

Good luck with your sbatch scripting!! 
