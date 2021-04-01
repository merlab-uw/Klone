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

  - Before running the script, I typed `sinfo` into the terminal to see what partitions were available. 
  - After running the script, I typed `squeue --account merlab` to see information about the job and see its job id
  - I also typed `scontrol show job <jobid>` to see very detailed information about the job I was running.



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

Good luck with your sbatch scripting!! 
