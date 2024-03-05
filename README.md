# useful-command-lines
A collection of commands that I find handy working in unix command lines

## General

### check space usage
```
df -h # check filesystem space
du -h # check space usage of specific folder
```

### kill all processes that match a specific command
```
pkill -f "/programs/structure_2_3_4/bin/structure"

#pkill is a command that sends a signal to processes based on their name or other attributes.
#The -f option tells pkill to match against the entire command line of the processes.
#"/programs/structure_2_3_4/bin/structure" is the pattern you want to match in the command line.
#Running this command will send a termination signal to all processes whose command lines contain "/programs/structure_2_3_4/bin/structure."
```

### kill all stopped jobs
```
kill -9 `jobs -ps`
```

### remove all broken soft links
```
find . -type l ! -exec test -e {} \; -print | tee broken_links.txt | xargs -I {} rm {}
# find /path/to/search -type l ! -exec test -e {} \; -print: This part of the command searches for broken symbolic links 
# and prints their paths.
# tee broken_links.txt: This part of the command uses tee to save the list of broken symbolic links to a file named broken_links.txt
# xargs -I {} rm {}: This part of the command takes the list of broken symbolic links (from the find command) and 
# uses xargs to execute the rm command on each broken link.
```

### SLURM header template
```
#SBATCH --job-name=jMap.sh # job name
#SBATCH --mail-user=yc2644@cornell.edu # where to send mail
#SBATCH --mail-type=END,FAIL # mail events (NONE, BEGIN, END, FAIL, ALL)
#SBATCH --ntasks=8 # number of tasks; by default, 1 task=1 slot=1 thread
#SBATCH --nodes=1 # number of nodes, i.e., machines; all non-MPI jobs *must* run on a single node, i.e., '--nodes=1' must be given here
#SBATCH --mem=10gb # job memory request; request 10 GB of memory for this job; default is 1GB per job; here: 8
#SBATCH --output=jMap.out.%A_%a # write stdout+stderr to this file; %A and %a replacement strings for the master job ID and task ID
#SBATCH --array=1-8
```


### recording run time of a script
```
start=`date +%s`

#commands to run

end=`date +%s` ## date at end
runtime=$((end-start))
hours=$((runtime / 3600))
minutes=$(( (runtime % 3600) / 60 ))
seconds=$(( (runtime % 3600) % 60 ))
echo "Runtime: $hours:$minutes:$seconds (hh:mm:ss)"
```

## Wrangling with files

### delete lines from one file which are in another file
```
grep -v -x -f file2 file1 > file3

# -v to select non-matching lines
# -x to match whole lines only
# -f file2 to get patterns from file2
```

### remove all files listed in one file
```
xargs -d '\n' rm < 2nd_batch_bamlist
# This command uses xargs to read each line from the "2nd_batch_bamlist" file and then 
# passes it as an argument to the rm command. 
# The -d '\n' option specifies that lines are separated by newline characters.
```

### get the number of columns; I used this with .ped  .dat files. 
```
awk '{print NF}' FILE_NAME | sort -nu | tail -n 1
awk '{print NF}' FILE_NAME | sort -nu | head -n 1
# Use head -n 1 for lowest column count, tail -n 1 for highest column count.
```

### remove specific lines from a file
```
sed -i '4d;8d' FILE_NAME # removes line 4 and line 8
sed -i '4,8d' FILE_NAME # with a range of 4,8 it will delete the lines 4 to 8
```

### remove the last certain columns of each line in the specified range of lines in a text file
```
sed -i '5006,5095s/\([^ ]* *\)\{4\}$//' FILE_NAME
# removes the last four columns of each line in the range of lines 5006-5095
# the regular expression \([^ ]* *\)\{4\}$ matches the last four columns and replace it with nothing, effectively removing them.
# the range 5006,5095 specifies that this command should only apply to lines 5006 to 5095.
```

### extract lines from a file between the specified range and then redirect them into a new file
```
sed -n '5006,5035p;5036q' FILE_NAME > NEW_FILE_NAME
# output 30 lines including line 5006 and line 5035
# p - Print out the pattern space (to the standard output). This command is usually only used in conjunction with the -n command-line option.
# An address range matches lines starting from where the first address matches, and continues until the second address matches (inclusively).
# q - Exit sed without processing any more commands or input. Otherwise sed will keep scanning till the end.
```
