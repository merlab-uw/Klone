# Notes about using MerLab's HPCC Node on Klone

## Background information on the node

The MerLab has purchased a dedicated node on Klone, the latest of UW's High Powered Computer Cluster (HPCC) systems. The other two are Hyak and Mox. All three systems are independent of each other.

For a great overview of High Performance Computing, watch [Introduction to HPC Computing: a practical tutorial](https://youtu.be/fkpofukvGeg)

These are the specifications of our node:

| Model                      |        CPU           | Total Cores                     | RAM                 |
|:---------------------------|:--------------------:|:-------------------------------:|:-------------------:|
| Large memory  | Intel Xeon Gold 6230 | 40 (20 cores * 2 chips) |              768 GB |



The organization and documentation of our node is based on those from the Roberts lab. [This is the Roberts lab github wiki for their HPCC node](https://github.com/RobertsLab/hyak_mox/wiki)

## Bioinformatics Tutorials

There are some PDF tutorials in our [MerLab google drive, in the Tutorials & Workshop Materials / Bioinformatics folder](https://drive.google.com/drive/u/1/folders/1TAkpMu3b0eNANPtQv67VoiDA7DF5ji6m)


## UW's Klone Documentation

* [Daily klone status page](https://uwrc.github.io/blog/2021/02/25/klone-soft-launch)
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

## Other Klone Specific Information

This is the rsync command you can use from within Klone to move your files from Mox.

```bash
rsync -azvr --progress mox.hyak.uw.edu:/gscratch/merlab/whatever /gscratch/merlab/whatever
```

If the lab node resources are all in use, you can run your job on resources beyond our dedicated capacity. You'll be using other labs group's unused hardware or a set of nodes that are owned by UW and shared for this reason.  Jobs run this way are subject to pre-emption. That means that your job may never finish or it may be interrupted and not be able restart properly. To run a job on a partition that we do not own, use the same account (-A merlab) but change the partion from the name of our node to ckpt (i.e., -p ckpt instead of -p compute-hugemem).

If you want to try to run on the ckpt partition, use this command to see how many nodes are idling

```bash
sinfo -p ckpt -O partition,nodehost,CPUsState,freemem,memory
```
