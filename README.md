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

If we want to check our currently running jobs, we can use
```
qstat -u [your_username]
```
And when we are done, we can exit the interactive session using
```
exit
```

## Metacentrum modules
Metacentrum has a large repertoire of bioinformatic tools. We can utilize these tools by loading appropriate modules and then work with them as if they are installed.
Some modules are described in docu -  https://docs.metacentrum.cz/en/docs/software/alphabet

But for the most comprehensive list, you will need to run code.

### Metacentrum module control commands
|Command                 |Description
|------------------------|-------------------------------------------------------------------------
|`module avail *`                      |list all modules (not recomended, too much results, might crash the graphics) 
|`module avail a*`                      |list all modules that start with letter "a" (* in linux means any number of any characters)
|`module avail *fast*`                    |liast all modules that contain "fast" in their name
|`module load [module_name]`            |load the module
|`module unload [module_name]`                     |unload the module - its a good practise to unload modules after use to prevet dependency crashes

As an example, today we will need 3 programs, [fastQC](https://home.cc.umanitoba.ca/~psgendb/doc/fastqc.help) (for RNA read quality visualisation), [TrimGalore!](https://github.com/FelixKrueger/TrimGalore/blob/master/Docs/Trim_Galore_User_Guide.md) (for trimming) and [Hisat2](http://daehwankimlab.github.io/hisat2/manual/) (for RNA read mapping).

## Quality control
After we obtain our data and have them in our MC storage, we can start with QC. 

In this step, we will use an interactive job (only run if you are not inside one already):
```
qsub -I -l select=ncpus=4:mem=16gb -l walltime=4:00:00
```
After a small wait, our job should start. When we the job starts, we can start using the terminal again. We want to use the FastQC program, so we need to check if it is available on MC, we can try something like:
```
module avail fastqc
```
Careful, the module names are case-sensitive. We can see that `fastqc` is available, so we can load it.
```
module load fastqc
```
and check if it working properly by asking for documentation.
```
fastqc --help
```

Now that we can see that fastqc is running properly, we can take a look at documentation and figure out how to run the command to handle our data. In this case, we simply need to specify what files to use. To improve it, however, we can specify output folder by `-o out_folder` and we can also use more threads (current job requested 4 NCPUs, so we can use up to 4 for the task, here 2 will be enough) `-t 2`.
```
#go to the folder with your data
cd [path/to/your/data]
#run fastqc
fastqc * -o fastqc_results -t 2
```

## Trimming and Mapping
Trimming and mapping is handled by a regular job, so for reference and explanation, please refer to the example scripts.

An example of a trimming script:
```
#!/bin/sh
#PBS -N mus_trimming
#PBS -l walltime=1:00:00
#PBS -l select=1:ncpus=16:mem=10gb:scratch_local=100gb

# The 4 lines above are options for scheduling system: job will run 1 hour at maximum, 1 machine with 4 processors + 4gb RAM memory + 10gb scratch memory are requested

# define a DATADIR and a OUTPUTDIR variable: directory where the input files are taken from and where output will be copied to
DATADIR=path_to_your_RNA_data
OUTPUTDIR=path_to_where_you_want_your_output

# copy input files to scratch directory
cp $DATADIR/*.fastq.gz  $SCRATCHDIR

# move into scratch directory and prepare fast5 folder
cd $SCRATCHDIR

# load modules
module load trim_galore
module load fastqc

#run trimgalore

trim_galore --paired SRR3083900_1.fastq.gz SRR3083900_2.fastq.gz \
                     --fastqc \
                     --clip_R1 25 --clip_R2 25 \
                     --three_prime_clip_R1 10 --three_prime_clip_R2 10 \
                     -o mus_results -j 16



#export results back
cp -r mus_results $OUTPUTDIR/

# clean the SCRATCH directory
clean_scratch
```

An example of a mapping script:
```
#!/bin/sh
#PBS -N mus_mapping
#PBS -l walltime=1:00:00
#PBS -l select=1:ncpus=16:mem=50gb:scratch_local=100gb

# The 4 lines above are options for scheduling system: job will run 1 hour at maximum, 1 machine with 4 processors + 4gb RAM memory + 10gb scratch memory are requested

# define a DATADIR and a OUTPUTDIR variable: directory where the input files are taken from and where output will be copied to
DATADIR=path_to_your_RNA_data
OUTPUTDIR=path_to_where_you_want_your_output
REFDIR=path_to_reference_file

# copy input files to scratch directory
cp $DATADIR/*.fq.gz  $SCRATCHDIR
cp $REFDIR/Mus_musculus.GRCm39.dna.primary_assembly.subsample.fa $SCRATCHDIR

# move into scratch directory
cd $SCRATCHDIR

# load modules
module load hisat2
module load samtools

#prepare output folder
mkdir mus_map_results

#build index from reference
hisat2-build Mus_musculus.GRCm39.dna.primary_assembly.subsample.fa mus_index

#run hisat2
hisat2 -x mus_index -p 16 -1 SRR3083900_1_val_1.fq.gz -2 SRR3083900_2_val_2.fq.gz | samtools view -bS -F 4 -F 256 > mus_map_results/mus_900_hisat2.bam

#export results back
cp -r mus_map_results $OUTPUTDIR/

# clean the SCRATCH directory
clean_scratch
```
