Using Klone tutorial by UW's Nam Pho

Nam Pho gave MerLab an introduction to UW's HPC (high powered computing) during a lab meeting in September of 2020. We had just purchased a node, but it had not been delivered yet and we wanted to get a basic understanding of how HPC works at UW.

The first HPC system at UW was called Hyak, so Hyak is the name often associated with HPC at UW regardless of the actual system. Hyak coupld mean Mox or Klone, or just HPC at UW in general. 

When Nam gave us this tutorial we were expecting to work on the Mox system- which is UW's second generation HPC. Later we would learn that our node would be one of the first in the third generation- Klone. There are some slight differences between how Mox and Klone are set up, but I have tried to update this tutorial where I can with those changes. If something is not working, it could be that the command is slightly different on Klone (some of the node names have changed, as well as the logic around partitions and accounts). All the third party programs such as slurm, singularity etc will not have changed, so check online documentation or the manuals for more information.

## Background on HPCC at UW

![](/_resources/9b1833d735c1d2e3b6c404ee2ebdd988.png)

High Performance Computing (HPC) at the UW is a condo ownership model super computer. What that means is that typically at large National Labs the government will give millions of dollars and the lab will just buy a big machine and then all they need to worry about is how to make it more efficient and which groups need to use it. At the UW we instead have a condo ownership model and every lab that wants to use the HPC can use it. If you need computing resources you buy the resources at cost. We distill the service catalog down to three or four server models (or nodes, we call a server a node) and then the lab will buy the node model they want to use at cost- that means no tax, no overhead or anything like that. The Hyak computing team will accept the node you buy and install it in the data center in the UW tower. The nodes are installed in the server rack (pictured above) where they are maintained. At it's most basic, Hyak is a collection of nodes for high powered computing that have been purchased by labs that we integrate into a useable platform.

Because your lab has contributed to Hyak by investing in a node, that means is you have a guaranteed slice of the resources available. UW members who want to use Hyak but are not part of a lab that owns a node on the HPC have to wait until there are resources available to use. For the most part, they are waiting to use the nodes that are idle when a lab that has purchased a node is not using their resource. That is the main incentive for buying a node; you have access to your resources whenever you need them. Because you bought a node when anyone from your lab submits a job with a request for an amount of resources that are available from your node (that means the request is for an amount of resources currently available on your node) the job will start immediately. When your lab is not using your node's resources other folks can use them.  It's likely that with this condo model labs end up buying less computing resources than they would probably need if  they were building the platform themselves. Typically there is a flurry of computing activity around a project but there is a natural ebb and flow to a project cycle. This condo model means that when the resources you bought would be sitting unused they can be used by other researchers which benefits the UW community.

The high power computing center at UW supports all computational research and provides the maintenance on the system. They accept the hardware, figure out the best configuration, provide security and do all the basic basic life support for the node.  They support everyone from super savvy researchers who just need to have the system set up, to those users who need the computational resources but do not know much about the command line. If you need help compiling software they can do that. It isn't technically what they want to be responsible for because they want the researcher to drive the research, but if you have questions you can always reach out. For grant writing, if your funding institution requires a description of the University's computational capacity, they will also provide a blurb about the cluster and the team that runs it to hopefully make the proposal more competitive. For any questions email [help@uw.edu](mailto:help@uw.edu). In the subject line please include the word Hyak and be descriptive, with plenty of detail in the body of the email.

## Walk through of how you would use Hyak for computational research

![](/_resources/c7dfc3407d3be03fc93d1a4b2bf44c69.png)

Using this paper from google scholar as an example, Nam walked us through the analysis the researchers did, using all the programs they used, but on Hyak. The authors used RAD seq and archived the data on NCBI , then used STACKs for genotyping, BayesScan and some R packages.

Nam made a pdf for the presentation that has a lot of links to the programs and their documentation. There are a couple stills from the video that show the PDF, but the original is included in the folder with this document, and linked here:

[SAFS Genomics.pdf](/_resources/dbd6035ab136ac2d1f025d7cb2ab3cfa.pdf)

![](/_resources/0ea12895efd2fbba1b1445ed65af5f9c.png)

### Log into Hyak using a terminal

Use "terminal" which is built in on a Mac system, or use something similar on Windows. There are other options for logging into Hyak, but this one is the easiest.

Type in the following command, with your netID, and hit enter. You will be prompted to put in your password, which is your UW netID password.

ssh <NETID>@klone.hyak.uw.edu

Then you will get a DUO prompt, which you can send to your phone. Once that is approved, you're in.

![Screen Shot 2021-04-29 at 6.40.20 PM.png](/_resources/9bb216619130392ee7fe56b128c983dc.png)

To be a user on Hyak, make sure you are part of your lab's group. Check out the website https://groups.uw.edu , and at the top ribbon, click on My Groups.

![Screen Shot 2021-04-29 at 6.43.08 PM.png](/_resources/16f333aba4aea8cde275fc1b4f6f81d1.png)

If you are not in a group, no groups will show up, but if you are part of a group, it will be shown on the next page. Click on the group and go to membership (a link near the top) to see the members of the group. Here you can add new members by their net ID.

### Once you are logged in

You will be taken to the log in node- think of it as the lobby of the cluster. The log in node is your launch point for starting jobs, transferring data, basically doing everything except computation. Beyond  the log in node are hundreds and hundreds of compute nodes. You can use slurm on the log in node to look at all the jobs that are being run.

## What are the different types of nodes?

There are three, all have access to the internet.

**Log in node:** We already saw the log in node, where you end up when you log in. You just use that for running commands to get resources for jobs or transferring data.

**Computer node: **the nodes where you do all your computational work.

**Build node:** the best node for trying things out, downloading data etc. You can't download data from the log in node.  And you don't want to tie up the resources on your compute node (the dedicated one your lab bought) running a very low resources, but persistent job like downloading data.

Use the command
sinfo

shows you all the labs (leftmost column) and all the nodes that are backed to that account (bought by that account) and what is roughly in use.

when you log into a new node, you can use the command of a single letter w
w

to see who else is on that node. This will show you all the people in the log in node, but if you get a build node, you will not see anyone because it is a dedicated node just for you.

## Storage on Hyak

Every user on hyak  gets 10 Gb of personal storage in their home directory.

The lab has 500Gb of shared storage on the lab home directory. That is shared between all members of the lab group, and it is separate from an individual's 10 Gb of storage. Everyone in the lab has access and can see what is in the shared lab storage. The path is /gscratch/merlab/ 10$ for a TB per month to buy more. This space is good for persistent storage like genomes that the whole lab will want to use.

Scrubbed - a free for all space. The path is /gscratch/scrubbed/ if you want to write a lot data out and you don't need it to last forever, this is the place to put it. You can make any folder you want on here. The caveat is that if you don't touch or access the files you put on scrubbed within 21 days, they are automatically deleted to clean up the drive.

## Downloading data from NCBI

We want to download the data that they used in this example paper. So go to the NCBI page that has the accession number for the sequence data

![Screen Shot 2021-04-29 at 7.16.59 PM.png](/_resources/2eb6e6926dda4118da37e701f1905c1b.png)

To download the data, you'll need SRA toolkit. You can download that yourself to Hyak . Hyak runs CentOS Linux architecture, so if you're choosing the version to install, use that one. Here Nam copies the link to the version he wants to download to Hyak.  He's right clicking on the link and copying the link location, or the full link path, so that he can paste it into the command line on Hyak.

![Screen Shot 2021-04-29 at 7.19.12 PM.png](/_resources/6bdede130ae8279645a23533cadd9e26.png)

Then he requests time on a build node  using the command srun, which is a slurm command that requests resources. Here he is asking for 4 hours on the build node using the account genpool (we would substitute merlab), with 4 cores and 30G of memory. The last part --pty $0 indicates that the language he wants to use in the command line is bash, but with colors. Typically you would use /bin/bash there.

![Screen Shot 2021-04-29 at 7.57.16 PM.png](/_resources/086c8cf78d86cf84e92eb4e4fd77b407.png)

To learn more about srun, read the manual page about it, which you can get using the command

man srun

This is not specific to Hyak, this is a universal program so any documentation on the web is valid.

* In the following screenshots of his work on Hyak, Nam is not actually using a build node to download the data, he was using a log in node. For that reason, the download was actually blocked- it was too big. But when he moved to a build node he was able to download the data.

And uses cd to navigate  into his folder on the scrubbed directory where he wants to download and install the SRA toolkit. He does it there because he knows he doesn't care if it gets deleted. He uses the command wget followed by the link to the software.

![Screen Shot 2021-04-29 at 7.29.36 PM.png](/_resources/dc75da1e2f5d0be1ee54ee823d5ef7d3.png)
Once it is downloaded, unzip it with the command tar xzvf <name_of_file>
![Screen Shot 2021-04-29 at 7.36.23 PM.png](/_resources/44a01f3b690ee12e8128cd22b91b673e.png)

It unpacks all the tools inside the tar file. When it has finished, use the command cd to go into the directory that was just created for the software and you can see all the files that have been downloaded for SRA toolkit.

![Screen Shot 2021-04-29 at 7.39.08 PM.png](/_resources/92f5b5e78cdf3f0b5849823911dbc797.png)

For more information on how to download data from NCBI using the SRA toolkit, go to their website with the documentation- its pretty well explained there: [www.ncbi/nlm.nih.gov/sra/docs/sradownload/](http://www.ncbi/nlm.nih.gov/sra/docs/sradownload/)

The fastest way is to use the command fasterq-dump --split-files <SRR_NUMBER>
![Screen Shot 2021-04-29 at 7.46.23 PM.png](/_resources/f788003fa5fb8820763e6268066f6760.png)

So put that command in the command line, and use the SRR number that you got from the NCBI page of the sample or data you want to download.

![Screen Shot 2021-04-29 at 7.48.33 PM.png](/_resources/9abbd0ea92b28978a9b35d81e044576c.png)

This is the page on NCBI for the data we want to download, and the SRR number is down at the bottom in a little table. Copy that number to put it at the end of the fasterq-dump command.

![Screen Shot 2021-04-29 at 7.48.50 PM.png](/_resources/3fc19646f9b72e807dacd18efc3343d5.png)

This is what it looks like when the command was run in a build node. For this to work you have to run it from within the folder that contains the program fasterq-dump, and you call that program with the ./  in the start of the program name to say look in this folder for the file.

![Screen Shot 2021-04-29 at 8.05.55 PM.png](/_resources/6ea9c618b5c97c426a82491b17c1763e.png)
When it finishes, it looks like this:
![Screen Shot 2021-04-29 at 8.13.03 PM.png](/_resources/d27bc0381993811750e3cdf8011d919e.png)
and you can take a peek at the fastq that was downloaded using the head command
![Screen Shot 2021-04-29 at 8.14.12 PM.png](/_resources/a990ed7cc804c7d03503c584283b2010.png)

And find out how many reads are in the file using the command wc -l <Name_of_file>

![Screen Shot 2021-04-29 at 8.15.26 PM.png](/_resources/534a0a1a4eabab89fb54d9014011a238.png)

The output shows that there are 15312468 lines in the file, and since we peeked at the file we know each sequence is 2 lines. Some basic arithmetic gives us 7,656,234 sequences in this 1.1 Gb fastq file

## Installing and compiling Stacks

Download and install this program on Hyak the same way that we did SRAtoolkit. First go to the website with the download, and try to copy the link for downloading the file (right click the download button). For stacks the link is broken, so Nam right clicked on the button and chose "Inspect Element" then went into the code and found the download link

![Screen Shot 2021-04-29 at 8.10.17 PM.png](/_resources/deb8569a1231952b55b205a905ac5684.png)

Once you have the link copied, use the command wget from within the folder you want to install the program, and once it has finished downloading, unzip it with tar xzvf <TAR_FILE_NAME>

![Screen Shot 2021-04-29 at 8.23.06 PM.png](/_resources/a5d6a1ac2db639bde049efbf400b1b91.png)

Once that is unzipped, go into the directory. This program is written in C, and those programs usually have a configure and make file in the tar that you unzip. You want to run the configure file so that you can compile this program into something that is useful. To do that, use the command ./configure. The slash is how you tell unix that you want to run the program.

![Screen Shot 2021-04-29 at 8.25.32 PM.png](/_resources/3b5b0942b1e9e247e48ebc41a2606044.png)
This configuration fails with the following error:
![Screen Shot 2021-04-29 at 8.27.12 PM.png](/_resources/dcf244efae2f363a7981c2d746cd8904.png)

This error means that the compiler (the program on Hyak that does the compiling using the compile file) that we were using is not one that is required for the job. The team that maintains Hyak tries to keep the super computer as efficient and clean as possible without extraneous programs. The operating system is very lean, and anything you might need beyond the most basic basic functionality will have to be loaded as a module or installed in your lab space. In this case, we need to load a version of gcc that is at least as recent as 4.9.0 in order to compile Stacks.

The easiest way to do that is to check to see if someone has already installed a gcc we can use as a module on Hyak. To see what modules are available use the command

module avail

and you will get a list  of all the modules that are on hyak- much like an App store. Anyone can use any of the available modules. Anyone on Hyak can build or make their own module, so any of the modules that start with the prefix contrib/ were built by someone and contributed, and those come with no guarantees. The modules without that prefix were built by the Hyak IT team, and they stand behind their work.  They take care of the compilers, and programs that a lot of people want to use like R.

To search for a program that you know the name of,  you can combine the module avail and grep commands to search specifically for modules with that name in the module avail list

module avail | gcc

The module list now has the gcc highlighted in the names, making it a little easier to find what you want. We are going to use a module built by the Hyak team (not starting with contrib/) and we'll use the latest one that is available. gcc/8.2.1

![Screen Shot 2021-04-29 at 8.38.48 PM.png](/_resources/74b1a6ec0c50cfc7ca956e9ba87c893b.png)

to load the module you want, use the command
module load <Name-of_module>

using the following command tells us which modules are currently loaded
module list

and we can test the version of the gcc that is loaded with
gcc --version

![Screen Shot 2021-04-29 at 8.41.35 PM.png](/_resources/695ca6bac3e11ff55c98342280df3f63.png)

Once we have a newer version of gcc loaded, we can try the configure command again:

![Screen Shot 2021-04-29 at 8.43.23 PM.png](/_resources/7bdf8b8861a89b276343268198e0e440.png)

This is very typical for compiling a program that was written in c. First you run configure, then you run make. When you run configure, your computer is scouring the environment to make sure that it has everything that it needs to compile the program. When it finds them all it puts them in the make file, which you then run to build the actual program. When the ./configure command has finished, run the command make (no ./) and it will build the program.

![Screen Shot 2021-04-29 at 8.47.29 PM.png](/_resources/1226b7efa1e4f54aa19947cfbcc0e007.png)

Stacks is a program that is probably used enough by the lab and a little tedious to install and compile that it would be worth it to have someone make a module from it that everyone in the lab could use. but we are not covering how to build modules here. On of the benefits of building a module is that it doesn't count against your lab quota.

When it has finally finished compiling, you can test that it is working by calling the program and requesting the help file:

![Screen Shot 2021-04-29 at 9.50.16 PM.png](/_resources/ad972ecca9a54f762e270378ebe32a21.png)

##

## Running programs with Singularity

Singularity is a containerization program or method. Most simply, a singularity container is  a container to put software in that you want to run, and you want to keep working no matter how much your computer changes.  Scientific and other types of computing suffer from the issue of keeping up with dependencies. Someone might write a piece of software to do some analysis, and it works wonderfully on their computer, but they can't get it to run after an update, or when moved to a new computer or different operating system. A singularity container can hold all the programs that you need and a version of the required or preferred operating system and the container is like a little virtual machine that can run on any computer (that has singularity installed).

Sigularity is super portable, but it does take a lot of space. Singularity is mostly for science. Here is the website where you can search for singularity containers:

https://singularity-hub.org/search

Docker is the same thing, but that is used mostly by non-research, non-scientific computing needs. Though there may be Docker containers for programs that you want like R and Rstudio, its best to find them as Singularity containers because that is the program that they use on Hyak.

So go to singularity hub, search for your program name. We are searching for BayesScan and we found a good hit from a user that appears to be a bioinformatician, since she has built a lot of singularities for commonly used bioinformatics programs.

![Screen Shot 2021-04-29 at 9.06.06 PM.png](/_resources/085244633dfabca513f6505b7109b125.png)

When we click on the button next to the BayeScan singularity, it takes us to the page where the singularity is described in detail.

![Screen Shot 2021-04-29 at 9.06.23 PM.png](/_resources/ca4eeb9f4f1d4493912ba0877d217c1c.png)

They provied a recipe so you can audit how the singularity was built. That is the code on the left hand side. When we are satisfied that we want to use this singularity, we copy the name of it. That is the part a the top:

![Screen Shot 2021-04-29 at 9.11.04 PM.png](/_resources/7f4e9787612a9e128f6dcd04511ba463.png)

Going back to hyak, we are in a build node and we are going to load the module singularity. This is the program that runs the singularity containers. So use the module load command and the module list command:

![Screen Shot 2021-04-29 at 9.12.14 PM.png](/_resources/3b4e570a3a7770e6437a7f3db669554e.png)

Then you run singularity using the single command singularity to make sure that it is loaded correctly. We didn't give it any arguments, so it will give us a little help page to get us started.

![Screen Shot 2021-04-29 at 9.13.43 PM.png](/_resources/00a6b43b5e55665097e42f2874479070.png)

To download the singularity container that we want to use, use the command. Shub is the prefix for singularity container hub, and then the rest is the name of the container we want.

singularity pull shub://<NAME_OF_MAKER/NAME_OF_CONTAINER:VERSION>

![Screen Shot 2021-04-29 at 9.17.56 PM.png](/_resources/a77e29d9d1256e0ca2058d2f794f1b16.png)

Once that has downloaded, you can check to see how big the file is with ls -alh

![Screen Shot 2021-04-29 at 9.18.49 PM.png](/_resources/95c798a19b421756ad4656bb876a591e.png)

The file is 169 MB which is not huge, but it is something and it will add up over time. Stacks only took 12 MB. The tradeoff with singularities is that yes they are amazing and very easy to use and install, but they take up a lot of space, and space is at a premium.

Once the singularity is downloaded, it will be a .sif file, and to run it you use the command singularity run <name_of_sif>

![Screen Shot 2021-04-29 at 9.22.40 PM.png](/_resources/ea146eef51a71333a70568f0ece5aaec.png)

This uses singularity to open the container, and it will run the program BayScan. The recipe file that we found for the singularity should tell us what is packaged inside the singularity container and which programs will begin to run when you run the .sif file. Here it is giving us the start up page (help page) for BayeScan because we didn't give the program any inputs.

![Screen Shot 2021-04-29 at 9.25.36 PM.png](/_resources/e7c5d613c3de682d39704a5e94f70ef1.png)

##

## Using Singularity for a Docker Container version of R

Sometimes software is not available as a singularity container and we have to look for it on Docker, where 99% of the time we can get it to work, but we have to convert it first.

Search for the program that you want to use in docker hub https://hub.docker.com/

We found R base 4.0.2 on Docker and want to use that. Here is the page that has 4.0.2 listed, and the name of the docker container and the version are on the right hand side of the page. Copy that.

![Screen Shot 2021-04-29 at 9.31.38 PM.png](/_resources/7caa9aa75cda30ba92b70bd7f6155884.png)

We have to use singularity to run this because that is the only thing that we have on Hyak.

Check to make sure that the module singularity is loaded, and load it if it is not (module load <Module_name>

module list

Then run singularity and pull the docker container instead of the singularity container we used before.

![Screen Shot 2021-04-29 at 9.34.48 PM.png](/_resources/a96e992c4f21ee58809cb40173563d9a.png)

That command will tell singularity that it needs to pull the container from docker and that it also needs to convert it to a singularity format. That will take a long time, then take a look and see how big it is.

![Screen Shot 2021-04-29 at 9.35.46 PM.png](/_resources/51931b21829b0094f7f0776a7c8cd1e6.png)

You want to be a bit judicious about what you are using as singularity and storing on Hyak and what you are compiling yourself, which will be smaller. But you could sotre this on your home direcotry, it is only about 5GB.

To run this singularity container, you do the same thing- singularity run <name_of_the_sif>

![Screen Shot 2021-04-29 at 9.38.33 PM.png](/_resources/5358c77aca6faa863d9a6744117b78c2.png)

##

## Installing R packages

Once you have an instance of R running, to install R packages is fairly simple (as long as the package is compatible with the R version) .

If the package is on CRAN, it is really easy, just make sure you are running an instance of   R and then the typical command install.packages("PACKAGENAME")

![Screen Shot 2021-04-29 at 9.43.12 PM.png](/_resources/1b31a15e84d0c7e312e858323eab7cdd.png)

Here we can see that it installed the packages in the same directory that we're in, and the same directory where we have R installed (or the sif image) and are running R from. If you want to change the location where the packages are installed, you can do that, but you look at the documentation online for install.packages() to pick out the commands needed.

## Using Visual Studio Code to SSH into Hyak and run programs

Visual Studio Code is
![Screen Shot 2021-04-29 at 9.55.01 PM.png](/_resources/dfccba5172e67ce4c52514da91700e7b.png)

a text editor on steroids made by Microsoft  that has a lot of plug-ins that give it a lot of functionality. One of the things it can do is connect to a remote server.  To do this, click on the red button in the very bottom left corner of the screen with the arrows facing each other. Choose "Connect to Host" and put it the path or server name: [klone.hyak.uw.edu](http://klone.hyak.uw.edu). Follow all the log in instructions, which are the same as for connecting through the terminal.

![Screen Shot 2021-04-29 at 10.00.36 PM.png](/_resources/13182f6ffba44a5480b9f33b67e0a05a.png)

Finally, you'll be in, and you can navigate using command line in the terminal within Visual Studio Code. You still have to use unix commands to move around and to start jobs, but if you were to run an R script that has a print to file function for the plots it was generating, you could look at those plots in the Visual Studio Code.

The left hand side of the screen has an icon that looks like a computer screen, this is the remote explorer where you can look at the file structure.

The terminal in Visual Studio Code is the same as what you would do from within the command line in Hyak, it's just a terminal and a text editor combined. And it has the added bonus of being located on your desktop.  You can use it with existing files (like R scripts) if they are not on the cluster, but you have to either upload them to the cluster, or make a new file on the cluster through Visual Studio Code and copy and paste the code into that file.

![Screen Shot 2021-04-29 at 10.10.27 PM.png](/_resources/7f767bfa0ff7b13150bf1af7084aee4c.png)

To look at the folders on Hyak, go to the left hand side menu and under the heading NO FOLDER OPEN choose the option OPEN FOLDER. Then you can choose the folder you want from the search bar.

![Screen Shot 2021-04-29 at 10.11.12 PM.png](/_resources/fa7c7842a97c246a9d2e032c56dee059.png)

Here we have our folder loaded we were just installing R packages in, and you can see all the files are the same. If you start a new R script at the top of Studio Visual Code, you can also start a build node and get R loaded in the terminal portion where you can run your R script.

![Screen Shot 2021-04-29 at 10.12.55 PM.png](/_resources/e6aa1fb4637819ad2247476c2b828c14.png)

Here we have written an R script to plot a simple plot, and save it to a file. In the terminal use the singularity to start R and also run our R script.

![Screen Shot 2021-04-29 at 10.17.58 PM.png](/_resources/40c6cbfb567fe372b9ad7bc650d1c6a8.png)

It runs, and when we refresh our menu on the left side of Visual Studio code, we can see that the jpeg is there. Click on it to see your plot!

![Screen Shot 2021-04-29 at 10.19.32 PM.png](/_resources/a671351fa1681b42ffd5abb3020a1740.png)

# Other information

## Our Node

40 cores, 768 G of memory

You can have one user hogging all 40 cores, or you could split it up into as many as 40 users using 1 core.  In a build node you are not using our dedicated resources, you're using general resources, so that is why you should do some task there if you have people who need to compute on the computer node.

CKPT are a pool of nodes that are not being used right now, you can use them. The downside is that your job can get cancelled for any reason, the main one being that the owner of the resource wants to use their node again. If your workflow is amenable to being interrupted at any point, ckpt might be for you.

## Requesting just the resources that we need

When requesting a job, use the slurm command to request just the number of cores and memory that your job will need, this will allow other lab members to use the core.

srun -A merlab -p computer-hugemem -n=4 --mem=30G <name_of_script_> OR <commands>

## Building your own module

If we build a module, you build it in the directory /sw/contrib/ In that directory you  make your own folder and download the software, make the module file to make it a module and build whatever you want. Nam encourages us not to use the modules that are made by contrib/ and not to try to make our own modules, just to use Singularities.

## Optimization

Optimize by not duplicating large files that everyone uses, don't split hairs about file size for programs that everyone uses- these are good things to have in the shared directory.

## MISC

To delete a directory that has files you don't need anymore, use the command
rm -vfr <Name_of_directory>

###
