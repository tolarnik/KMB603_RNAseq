# KMB/603 RNAseq analysis 2025
supplementary materials for KMB/603 RNAseq analysis course

## Basic linux commands
These are some basic linux commands that we will use to navigate in our Metacentrum segment
```
.  #single dots means we are referencing the current folder
pwd #print path to current folder
cd *adress* #change directory
ls #list all files in the current folder
ls -la #list even hidden files and give me more info
mkdir *dir_name* #create a new directory
rm *file_name* #remove a file
rm -r *dir_name* #remove directory and everything inside
cat *file_name* #print the insides of a file
head *file_name* #print first few lines of a file
tail *file_name* #print last few lines of a file
cp *file_name* *new_location* #move a file to a new location
cp -r *folder_name* *new_location*
```

Further, in our scripts, we will use some variable definitions. Variables are just containers that hold some information for further use. In the scripts, we use variables to remember a path to input and output files. When we then reference this path in a command, we can use the variable value and save ourselfs from having ugly and hard to read commands.

Example of variable definition and usage:
```
INPUTDIR=/path/to/file #define a path to a file into a variable
echo $INPUTDIR #print the variable value
cp $INPUTDIR/file.txt . #copy file.txt from variable value to current folder
```
