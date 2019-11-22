Assembly using Canu version 1.6
-------------------------------

The genome was assembled with Canu long read assembly software (v.1.6) on the University of Sydney High Performance Compute cluster using PBS-Pro scheduler for job submission. We used diploid parameters to avoid collapsing heterozygous alleles and the default corOutCoverage (tested corOutCoverage=200, this output very large trimmedRead.fasta and very compute intensive subsequent steps - unfinished). Assembly took four and a half months with default coverage. Approximately 359,878 CPU hours were used for the assembly stage. Grid options were specified, and job scheduling was managed with PBS-Pro v13.1.0.160576, thereby limiting the number of jobs running at any one time to around 400. Each job required 4 CPUs, around 16 GB memory allocation and progressive allocations of walltime to a maximum of 70 hours. 
We submitted 74.2 Gigabytes of data = 72.4 gigabase of sequences pre-trimming and correction by canu. Correction stage took 3 weeks and output 36.9 gigabases. Trimming stage took 6 weeks and output 35.0 gigabases. The unitigging and consensus stage took 7 weeks and output the assembled genome at 1.98 Gb with 13,361 contigs contigs.


```
module load canu/1.6

canu -p MR \
  -d /scratch/run-MR \
  gnuplot="/usr/local/gnuplot/5.0.0/bin/gnuplot" \
  genomeSize=1g \
  correctedErrorRate=0.040 \
  batOptions="-dg 3 -db 3 -dr 1 -ca 500 -cp 50" \
  gridOptions="-P MRG" \
  gridOptionsCORMHAP="-l walltime=60:00:00" \
  gridOptionsCOROVL="-l walltime=60:00:00" \
  gridOptionsCOR="-l walltime=60:00:00" \
  gridOptionsOBTOVL="-l walltime=60:00:00 -l mem=16g -l nodes=1:ppn=8" \
  gridOptionsUTGOVL="-l walltime=60:00:00 -l mem=16g -l nodes=1:ppn=8" \
  gridOptionsMERYL="-l walltime=4:00:00" \
  saveOverlaps=true \
 -pacbio-raw ./*.fasta
 ```

