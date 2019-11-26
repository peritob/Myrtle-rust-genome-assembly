Use RNAseq with Braker2 for annotation 
--------------------------------------
First map Illumina RNAseq data to the genome with splice aware software Hisat2 and get .bam outputs.

```
module load hisat2/2.1.0
module load samtools/1.9

#hisat2 script (map RNAseq to the primary and secondary genomes of myrtlerust and get the mapped output only)
hisat2-build -p 16 -f APSI_primary.fa APSI_primary
hisat2 -p 16 --summary-file APSI_primary -x APSI_primary --no-unal -1 *R1*.fastq.gz -2 *R2*fastq.gz -S APSI_primary.sam
samtools view -S -@ 4 -b APSI_primary.sam > APSI_primary.bam
samtools sort -m 7G -o APSI_primary_sorted.bam -@ 4 APSI_primary.bam

```
Then softmask the genome with RepeatModeler and RepeatMasker.
```
module load repeatmasker/4.0.6/
module load repeatmodeler/1.0.8
BuildDatabase -name APSI_primary.DB  -engine ncbi APSI_primary.fa
RepeatModeler -database APSI_primary.DB -engine ncbi -pa 24
RepeatMasker -pa 24 -gff -xsmall -lib consensi.fa.classified APSI_primary.fa
```
Found ~85.5% repetitive regions. Run braker2 with bam and softmasked genome.

```
# Load modules
module load braker2/2.1.3
module load genemark-es/4.33
module load augustus/3.3.2
module load bamtools/2.5.1
module load python/3.6.5
module load samtools/1.9
module load genomethreader/1.7.1
module load exonerate/2.2.0
module load eval/2.2.8
module load makehub/i
module load blast+/2.7.1

cd $PBS_O_WORKDIR
export AUGUSTUS_CONFIG_PATH="/home/augustus/config/"
export AUGUSTUS_BIN_PATH="/usr/local/augustus/3.3.2/bin"
export AUGUSTUS_SCRIPTS_PATH="/usr/local/augustus/3.3.2/scripts"

#run braker script to annotate the APSI_primary.fasta with RNAseq
braker.pl --species=myrtlerust --workingdir="/scratch/myrtlerust/braker" --cores=8 --softmasking on --genome=APSI_primary.fa.masked --bam=MR_P_sorted.bam --gff3 --fungus

```
Braker2 needs 16G memory and 20 hours walltime.
