# How to run RStudio on Klone

## Background information

This pipeline enables the user to run RStudio on Hyak/Klone. This works by using an RStudio singularity (rstudio_4.1.0.sif), which opens RStudio in a web browser on your local computer - while using the resources provided by the HPC nodes. This is particularly useful for paralellizing computationally intensive scripts (like individual-based models). It seems that this method does **not** work much faster for a given core than R runs on your local computer - but, it gives you access to 40 cores instead of 8, 16, or whatever you have on your local computer. Note, installing packages can take a very long time, and it may take some fiddling to get them to download to the correct location. 

This tutorial was originally followed to do this: [here](https://gist.github.com/mwanek/a67154d4eee66ba17aa4890d5814c263) 

## Login to Klone:

`ssh USERNAME@klone.hyak.uw.edu`

## Get the .sif file for RStudio

Sam has a copy of this in his merlab folder on Klone, which you can copy to whatever directory you want to stash your files in (likely the scrubbed folder)

```
cd /gscratch/merlab/smay1
cp rstudio_4.1.0.sif /gscratch/scrubbed/USERNAME
```

Alternatively, you can pull this directly from docker. You can also update the RStudio version you want, here is 4.1.3:

```
salloc -A merlab -p ckpt -N 1
module load apptainer
cd /gscratch/scrubbed/USERNAME
apptainer pull docker://rocker/rstudio:4.1.3
```

## Get the .job file

Again, Sam has a copy of rstudio-server.job in his merlab folder. There is also a copy in the merlab Klone directory here(), or you can get the original from [here](https://gist.github.com/mwanek/f9006b864569264474bb3f4985a6c5b4)

##Modify the .job file

Modify the SBATCH fields to specify details for your job:

```
#SBATCH --account=merlab
#SBATCH --partition=compute-hugemem
#SBATCH --time=12:00:00
#SBATCH --signal=USR2
#SBATCH --ntasks=20
#SBATCH --mem=200G
#SBATCH --output=/mmfs1/gscratch/scrubbed/smay1/rstudio-server.job.%j
```

Also ensure these paths are correct:

```
GSCRATCH="/mmfs1/gscratch/scrubbed/USERNAME"
RSTUDIO_SIF="rstudio_4.1.0.sif"
RSTUDIO_SIF_PATH="${GSCRATCH}/${RSTUDIO_SIF}"
```

## Submit your job to the scheduler

`sbatch rstudio-server.job`

## Read the job output

After the job starts, read the output file that was just created. This provides specific instructions, including an SSH port forwarding command that needs to be run on your local computer (not on the cluster). It will also provide a job-specific password that you will need to use.

`cat rstudio-server.job.123456`

This file should look like this:

```
1. SSH tunnel from your workstation using the following command:
   ssh -N -L 8787:n3020:34607 USERNAME@klone.hyak.uw.edu
   and point your web browser to http://localhost:8787
2. log in to RStudio Server using the following credentials:
   user: USERNAME
   password: BEEXNr7eYayouCa3Fsnl
When done using RStudio Server, terminate the job by:
1. Exit the RStudio Session ("power" button in the top right corner of the RStudio window)
2. Issue the following command on the login node:
      scancel -f 11414386
INFO:    underlay of /etc/rstudio/rsession.sh required more than 50 (120) bind mounts
```

## Back to your local computer

Keep the terminal you are working in open, and also open a *new* terminal window, if you are using windows subsystem for linux, open a second Ubuntu terminal. Copy and paste the above commands into this new terminal window.

`ssh -N -L 8787:n3020:34607 smay1@klone.hyak.uw.edu`

Open a web browser (i.e., Chrome) and navigate to http://localhost:8787

If you've done everything correctly, this will prompt you to login to RStudio. Use the username and password provided in the above instructions.

You can now use RStudio just as you would on your desktop. It will only have access to as much memory and cores as you specified in your SBATCH script. You can save .R files via RStudio, and they will save to the working directory where your .sif file is located. Be sure to copy these out of the scubbed folder if you want to keep them long-term. 

## Paraellelizing in R 

There are several different ways to parellelize R scripts. I use the doParallel and foreach R packages. This requires that your script is written in such a way that it will output a unique file (importantly, with a unique file name) for each different combination of input parameters. Otherwise, your outputs will just write over each other. Then you can use several different input parameters to run the script with all unique combinations, like this example from the bottom of my IBM script  [here](https://github.com/SMay1/Assortative_Mating_QG_IBM/blob/main/Assortative_Mating_QG_IBM.R).

```
clust<-makeCluster(8)
registerDoParallel(cl = clust, cores = 8) #Run on 8 cores

foreach(iterz = 1:100, .packages = c("dplyr","rockchalk","TruncatedNormal")) %:%
  foreach(rhoz = c(0,-0.3,-0.6)) %dopar% {
    Assortative_Mating_IBM(G = 10,
                           OMEGA = 2,
                           iteration = iterz,
                           variance_entry_day = 20,
                           variance_RLS = 20,
                           rho = rhoz,
                           assortative = T,
                           var_env_entry = 20,
                           var_env_RLS = 1,
                           PATH = "output/Trait_Correlations/gen_data_")}

stopImplicitCluster() #closes unused processor connections after running foreach
```

