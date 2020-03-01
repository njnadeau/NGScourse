*APS Advanced Stats Delivery* https://github.com/visoca/AdvDataAna-introNGS
# Sequence data formats and assessing sequence quality - day 1 morning part 2
#### Nicola Nadeau, Alison Wright, Helen Hipperson

In this practical you will get to grips with handelling sequence data, assessing its quality and trimming reads.

## 1. Fastq format, viewing and searching
We will start by having a look at the sequence data files you downloaded this morning. Navigate to your fastdata directory where they are located. The files are compressed (ending in ```.gz```) so you can't view them directly with ```less```. You could extract them with ```gunzip``` but then they would take up much more space and this isn't necessary for the programs we will be using, because all of these can work on compressed data. Instead we can use ```zcat```, which can unzip files "on the fly" and writes the output to  the standard output (```stdout```). By default this is the terminal window, so if we used just ```zcat``` the whole thing gets written to the terminal window. If this happens by mistake don't panic! ```Ctrl c``` will stop it, although it might take a little time for your command to take effect, the node will already have read in a huge amount of data before your command is even registered! 

Instead we can use ```|``` (called a pipe) to send the output to another program. In our case we can send it to ```less``` so we can control how we view the output
```bash
zcat 151019_D00248_0127_AC7HJ7ANXX_7_TP-D7-006_TP-D5-008_1.sanfastq.gz | less
```
One useful thing we can do within less is search by typing ```/``` followed by the string you want to find, for example ```/5843:36399``` will jump to the read in the file with that id. You can then continue scrolling up and down if you want to see the reads that come before and after it. 

Another was to search is using ```grep```. This will find all the lines matching the string you specify and write them to ```stdout```. If you want to save these to another file you can do this with ```>```
```bash
zcat 151019_D00248_0127_AC7HJ7ANXX_7_TP-D7-006_TP-D5-008_1.sanfastq.gz | grep "@HISEQ:127:C7HJ7ANXX:7:1101:18" > subsetofreadnames.fastq
```
This writes the read names containing ```@HISEQ:127:C7HJ7ANXX:7:1101:18``` into a new file called ```subsetofreadnames.fastq```
Of course in reality we would probably never want to do this because this file is not very useful on its own. What we might want to do is know how many reads there are that start ```@HISEQ:127:C7HJ7ANXX:7:1101:18```. We can do this us ```wc -l```, which counts the number fo lines a file. We can of course now run ```wc -l subsetofreadnames.fastq```, or we could skip the file writing stage
```bash
zcat 151019_D00248_0127_AC7HJ7ANXX_7_TP-D7-006_TP-D5-008_1.sanfastq.gz | grep "@HISEQ:127:C7HJ7ANXX:7:1101:18" | wc -l
```
Do these give the same answer? 

Remove ```subsetofreadnames.fastq```

### Exercise
Every sequenced fragment has a forward read and a reverse read (this is called paired-end sequencing). These two reads are contained in different files that have the same name but end in ```_1``` and ```_2``` respectively. Have a look at the start of two of the sequence files that contain the forward and reverse reads. Can you tell which reads are from the same fragment? Pick one read from one of the files and see if you can find its partner in the other file.

There should be the same number of reads in the forward and reverse files. Is this the case?

## 2. Checking sequence quality
We are going to use the program ```fastqc``` to assess the quality of our sequence data. We will submit this as a job to the cluster because it will take some time to run. You have been given the bash script, it is called ```fastqc.sh```. Open this with nano
```
#!/bin/bash
#$ -l h_rt=1:00:00
#$ -l rmem=2G
#$ -m bea
#$ -j y
#$ -o fastqc.log
#$ -pe smp 4
#$ -M

source /usr/local/extras/Genomics/.bashrc

# the following sets up an input string containing all the files ending sanfastq.gz in raw/60A
inputString=""
space=" "
for i in raw/60A/*.sanfastq.gz
  do
  inputString=$inputString$i$space
  done

fastqc -o fastqc_output -t 4 $inputString
```
As before, enter your email address. ```#$ -pe smp 4``` tells the cluster to run this in parallel over 4 nodes. In the ```fastqc``` input the ```-t 4``` then tells the program it can split its job over 4 nodes. The 2 numbers need to be the same!
This line ```source /usr/local/extras/Genomics/.bashrc``` tells it where to find the Genomics Software Repository, which we set up access to in the morning. 
Before running this you will need to make a directory called ```fastqc_output```. 

What do you notice when this is running (```qstat```)?

Once it has run (```qstat``` to check) you can check the ```fastqc.log``` to see that everything seems to have run OK (this is long so you might want to use ```tail``` to view the end or search for lines that say ```Analysis complete```) but if there are output files for every input file then it has probably run OK. The output is graphical so you will need to download it to your computer to view it. 

How many reads are in the files (do these match up with the counts you did)? Are there differences in quality between the forward and reverse reads?

## 3. Trimming reads
You will notice that the quality of the reads tends to fall off at the end. Also there seems to be some remaining adaptor, likely where the fragment length has been shorter than the read length. Both of these can be solved by trimming the reads. You have been given a bash script ```trimmomatic.sh``` that uses the program ```trimmomatic``` to trim the reads. Open this script and edit it to add your email address
```
#!/bin/bash
#$ -l h_rt=2:00:00
#$ -l rmem=2G
#$ -m bea
#$ -j y
#$ -o trimmomatic.log
#$ -t 1-3
#$ -M

source /usr/local/extras/Genomics/.bashrc

#set the path to trimmomatic
ProgramPath="/usr/local/extras/Genomics/apps/trimmomatic/current"

#get a list of all the forward reads
SAMPLE1=($(ls raw/60A/*_1.sanfastq.gz))
#get a list of all the reverse reads
SAMPLE2=($(ls raw/60A/*_2.sanfastq.gz))

#set up an index for the reads/tasks
INDEX=$((SGE_TASK_ID-1))


# run trimmomatic on the 3 sets

java -jar $ProgramPath/trimmomatic-0.38.jar PE -phred33 ${SAMPLE1[$INDEX]} ${SAMPLE2[$INDEX]} ${SAMPLE1[$INDEX]}.out_paired_50bp.fastq.gz ${SAMPLE1[$INDEX]}.out_unpaired_50bp.fastq.gz ${SAMPLE2[$INDEX]}.out_paired_50bp.fastq.gz ${SAMPLE2[$INDEX]}.out_unpaired_50bp.fastq.gz ILLUMINACLIP:$ProgramPath/adatpers/TruSeq2-PE.fa:2:30:10 LEADING:3 TRAILING:3 SLIDINGWINDOW:4:15 MINLEN:36

```
There are many different ways to trim the data with Trimmomatic. This script uses the parameters recommended by Trimmomatic (see below for explanation). You may have to tweak these parameters in reality and see which work best.
```
Remove adapters (ILLUMINACLIP:TruSeq2-PE.fa:2:30:10)
Remove leading low quality or N bases (below quality 3) (LEADING:3)
Remove trailing low quality or N bases (below quality 3) (TRAILING:3)
Scan the read with a 4-base wide sliding window, cutting when the average quality per base drops below 15 (SLIDINGWINDOW:4:15)
Drop reads below the 36 bases long (MINLEN:36)
```
This script also runs in parallel but instead of splitting one job across several nodes it runs different jobs on each node (set by```#$ -t 1-3```), in our case running each of the 3 sets of paired end data on a different node. Again you need to make sure that the number of jobs you set up (```-t 1-3```) is the same as the number of files you have (```ls raw/60A/*_1.sanfastq.gz```).

### Exercise
Once this has run you should have 2 new sets of reads for each previous read, one set that is paired and one set that has become unparied since one of the reads as removed entirely. Move these files to a new directory using ```mv``` (hint, you can use ```*``` so you don't need to move each file individually). How many reads of each type are there?

Run fastqc on these trimmed files to check that the quality has improved. 

How have the profiles changed?

If you are waiting for scripts to run and want something to do you can try the one or both of the next exercises while you wait.

## 4. Merging sequence file
Before aligning the sequence data to the reference genome we probably want to merge the multiple files from each individual (this is also a chance to give the file clearer names).
### Exercise
Use ```zcat``` to merge together all the paired trimmed forward read files and all the paired trimmed reverse read files to generate a single forward read file called ```60A_1.fastq.gz``` and a single reverse read file called ```60A_2.fastq.gz```. Can you write a script that would scale this up to merge the reads for the 4 samples in ```raw```, with each runing in parallel on a separate core? *Be very careful!* Subsequent programs will only work if the forward and reverse reads are kept in the same order in the 2 files, so make sure you merge them in the same order. Hint: Try using the ```trimmomatic.sh``` script as a starting point. Using ```*_1.sanfastq.gz``` and ```*_2.sanfastq.gz``` will make sure the read files are listed in the same order.

## 5. Downloading data from the internet and fasta format
When you get data back from a sequencing centre you will often have to download it from their servers. You can do this direct to a directory on the hpc using the command ```wget```. If you have large files then you probably want to submit this as a job to the cluster. 
### Exercise
Check the manual for ```wget``` and then create a bash script to download the Heliconius melpomene reference genome from here http://download.lepbase.org/v4/sequence/Heliconius_melpomene_melpomene_Hmel2_-_scaffolds.fa.gz 

Once it has dowloaded unzip it and view it. This is a fasta (rather than fastq) file. What do you notice about the format?

Return to main course page: https://github.com/visoca/AdvDataAna-introNGS
