# Using the Slurm Job Scheduler on Klone

## Slurm Basics

Slurm is a job scheduler used to provide access to compute nodes on the Klone supercomputer. 
As a cluster workload manager, Slurm has three key functions. First, it allocates exclusive and/or non-exclusive access to resources (compute nodes) to users for some duration of time so they can perform work. Second, it provides a framework for starting, executing, and monitoring work (normally a parallel job) on the set of allocated nodes. Finally, it arbitrates contention for resources by managing a queue of pending work.

- For a quick and useful overview of basic Slurm commands, watch [this great YouTube video](https://www.youtube.com/watch?v=49DzPT9HFJM&list=LL&index=2) from the Center for High Performance Computing at the University of Utah. 

## Slurm commands
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

## See Slurm commands in action

To see some examples of how these commands are used, check out the [QuickStart User Guide to Slurm](https://slurm.schedmd.com/quickstart.html)

