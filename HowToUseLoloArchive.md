# Notes about using UW's Lolo Archive 
[UW's documentation page on lolo](https://wiki.cac.washington.edu/display/DataBackupsandArchives/lolo+User+Documentation)

The UW's documentation covers both the "collaboration" and the "archive"; we do not have any space on the "collaboration" servers, so ignore that part!

## Background information on Lolo

The Merlab has purchased dedicated space on the UW's Lolo Archive, a long term storage space for large datasets. Lolo is tape based, which means it is intended for the long term storage of data sets that do not change- this is perfect for the storage of our raw sequence data. The archive should not be used for interactive access which means you should not plan on accessing the data for interactive analyses, it is just a very secure back up for our large data sets. To work with the data you will need to move it to another location like the UW's super computer Klone. Moving the data to Klone can easily be done through ssh using rsync in the login node of Klone. 

## Lolo capacity

Lolo is designed for large files, it does not handle small files well. To store smaller files on lolo, combine them with other smaller files and combine them into a tar file or zip them together. To keep lolo efficient, there is a quota to the number of files that can be stored on the service; each 1TB of space on lolo allows 1,000 files. 

Space in the archive is purchased in 1TB chunks and billed monthly to the lab group's cost center. We have 9TB of space (current May 2021) and we can easily increase our limit through a request to UW-IT. Of that 9TB, the Naish lab has 6TB of space and the Hauser lab has 3TB. 

## What should you store on Lolo? 
It is up to each group to decide what they want to prioritize archiving, but the Hauser lab has decided to back up raw sequence data for any project that does not have the data available on a publicly archive such as [Dryad](https://datadryad.org/stash). Large intermediate files that are not easily reproducible will also be stored on lolo until the project is published and/or the files are no longer relevant. These intermediate files may be, for example, alignments, or any portion of a bioinformatics pipeline that would take more than a week or so to recreate. 

## Logging in to Lolo

You can connect to Lolo Archive the same way that you do to get on Klone, with ssh. 
``` bash
ssh <your netid>@lolo.uw.edu
```

## Lolo structure

Much like Klone, each user on Lolo has a home directory. The quota for that directory is only 50MB, and should just be used to store shell scripts. 

All files should be stored in the merlab shared directory which is located at /archive/merlab

The file structure of the merlab shared directory should mirror the structure of the shared directory on Klone. Files should thoughtfully and informatively named, and either be stored in folders with the researcher's name or for shared work, a folder with the name of the project. Interior file structure is at the discression and to suit the needs of the researcher. 

## Transfering files to Lolo
This is taken directly from the UW's lolo documentation: 
Using rsync

_Transferring files using rsync over ssh is a supported option. There is one important caveat: when using rsync you must always use the -W or --whole-file option. This option disables the rsync transfer checksum algorithm which normally would speed the transfer of changed files by only sending changed bytes. On lolo, this algorithm will cause a tape recall of every file that already exists in order to have the file contents available to calculate the transfer checksum. This recall would be detrimental to lolo archive function and will result in poor performance of transfers. The -W or --whole-file option must always be used._

_Using rsync has the benefit of ensuring integrity of transferred files. When rsync transfers a file it always calculates a checksum for the whole file and compares at the completion of the transfer. This works even in whole file (-W, --whole-file) mode._

## Questions we're not sure the answer to yet 

Does anyone who is part of our Hyak access group also have automatic access to lolo?
