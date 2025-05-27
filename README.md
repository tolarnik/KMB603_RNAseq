# KMB/603 RNAseq analysis 2025
supplementary materials for KMB/603 RNAseq analysis course

## Basic linux commands
These are some basic linux commands that we will use to navigate in our Metacentrum segment.


### Basic command table
|Command                 |Description
|------------------------|-------------------------------------------------------------------------
|`#`                      |hashtag means a comment, anything after a hashtag is ignored 
|`.`                      |single dots means we are referencing the current folder
|`pwd`                    |print path to current folder
|`cd [adress]`            |change directory
|`ls`                     |list all files in the current folder
|`ls -la`                 |list even hidden files and give me more info
|`mkdir [dir_name]`       |create a new directory
|`rm [file_name]`         |remove a file
|`rm -r [dir_name]`       |remove directory and everything inside
|`cat [file_name]`        |print the insides of a file
|`head [file_name]`       |print first few lines of a file
|`tail [file_name]`       |print last few lines of a file
|`cp [file_name] *new_location]` |move a file to a new location
|`cp -r [folder_name] [new_location]` |move entire folder and everything in it to a new location
|`wget [link]`            |downloads the file from the specified link
|`tar xvzf [file.tar.gz]`    |extract contents of an archive that has .tar extension and .gz compression


Further, in our scripts, we will use some variable definitions. Variables are just containers that hold some information for further use. In the scripts, we use variables to remember a path to input and output files. When we then reference this path in a command, we can use the variable value and save ourselfs from having ugly and hard to read commands.

Example of variable definition and usage:
```
INPUTDIR=/path/to/file           #define a path to a file into a variable
echo $INPUTDIR                   #print the variable value
cp $INPUTDIR/file.txt .          #copy file.txt from variable value to current folder
```

## Metacentrum jobs and frontend
By default, anything you do when you are connected to your MC account is taking place on frontend. It is a bad practise (and also risky) to do computationaly demanding tasks at frontend, since those resources are shared with other users. It is advised to use job schedule system to reserve your own computational resources and run tasks that way.

We have two options - interactive job (behaves just like frontend) and regular job (you just submit your script and it will be executed in the background).

The current documentation for Metacentrum systems can be found at - https://docs.metacentrum.cz/en/docs/welcome
Explanation of Job system - https://docs.metacentrum.cz/en/docs/computing/run-basic-job

For now we can just try opening an interactive session and work inside of it.
```
qsub -I -l select=ncpus=4:mem=16gb -l walltime=4:00:00
```
This will `qsub` submit us into a queue in `-I` interactive mode and request `-l select=ncpus=4:mem=16gb` 4CPUs and 16GB of RAM. Further, we specify that the maximum runtime is `-l walltime=4:00:00` 4 hours. 
