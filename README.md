# Rad-loci pipeline

A RADseq pipeline based on a read-cluster approach

## Installation

### Dependencies

1. VSearch (https://github.com/torognes/vsearch) [Version 1.1.3 was used when testing]
2. Parallel (http://www.gnu.org/software/parallel/) [Version 20140722 was used when testing]
3. Python (https://www.python.org/) [Version 2.7.6 was used when testing]
4. Numerous shell tools (awk, basename, bc, bzcat\*, cat, cd, grep, ln, mkdir, paste, pwd, rm
   sed, sort, tail, uniq, wc, zcat\*) \*optional commands

NOTE: there is a command in the bin directory that checks that it can find all necessory 
commands called 'check-dependencies'

### Rad-loci

1. Download the latest release
2. Extract archive
3. Add bin directory to your path

e.g.

```sh
# Download the source (and unpack)
wget https://github.com/molecularbiodiversity/rad-loci/archive/v0.4.tar.gz
tar xf v0.4.tar.gz

# Optional: you may want to move the source somewhere else.  If you do you
# will need to cd to the directory containing it too.
#mv rad-loci-0.4 /usr/local
#cd /usr/local

# Add to path (you may want to put this in your ~/.bashrc file to you don't
# need to do it each time).
# use 'pwd' command to work out your working directory (as you will need to
# hard-code it if you use the ~/.bashrc option)
export PATH=$PATH:$PWD/rad-loci-0.4/bin

# Check dependencies were ok
check-dependencies
# this should output "Success: everything found in your path" at end
```

### Quick start

1. Make a directory called 'samples' in your current directory and copy/symlink all your sample 
   fastq or fasta files into it
2. Run 'rad-loci-settings settings.conf' program to create a new settings file in your current working 
   directory.  Optionally change any of the settings as required
3. Run 'rad-loci settings.conf' command.  It will output a lot of progress information on the
   terminal.  It will take a while to process the data based on how much input data is 
   provided so it is best if you run in with nohup or as an HPC job.

e.g.
```sh
# create a directory for this experiment
mkdir experiment
cd experiment

# create a directory for the samples (input)
mkdir samples
cd samples
# copy or symlink the sample fastq/fasta files
#ln -s /some/path/to/sample/files*.fq .
#cp /some/path/to/sample/files*.fq .
cd ..

# create settings
rad-loci-settings settings.conf
# edit the settings as necessory
#nano settings.conf
#vim settings.conf

# run the pipeline (with nohup as it might take a while and
# we don't want it to terminate if our connection drops)
nohup rad-loci settings.conf &

# watch the output
tail -f nohup.out
#Ctrl+C to exit this view as it will go for ever
```

## Processing steps

1. Create the catalog of potential loci and prepare samples for analysis
2. Merge (cluster) similar sequences from catalog
3. Filter clusters to those that have 2 to 16 different members (<2 = non-informative, >16 = multi-mapping)
4. Align sequences to filtered catalog (to find the which alleles map to each)
5. Refilter catalog to those that contain 2 to 16 alleles
6. Make database of all allele sequences (FastA)
7. Map sequences from each sample (100% identity) to allele database and count copies
8. Merge counts from each sample into single TSV file
9. Filter Loci based on:
  1. Max 2 alleles per sample per loci
  2. Min 2 significant alleles and
  3. Min proportion of samples with data for loci
10. Call genotype for each sample and output as a structure (.stru) file

### optional

1. Make random selection of loci
2. Call genotype into migrate format

