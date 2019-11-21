Polish the MR_P.fasta
---------------------

Map all the fastq raw reads to the primary contig assembly and get bam file (took 4cpu, 23 hours and 82 GB RAM - next time use fasta raw reads rather than fastq - minimap2 prefers)


```
module load minimap2/2.3
module load samtools/1.9

minimap2 -ax map-pb MR_P.fasta *.fastq \
| samtools view -hF 256 - \
| samtools sort -@ 8 -m 1G -o MR_P_aligned.bam -T tmp.ali
| samtools index -b -@ 4 MR_P_aligned.bam
```

Convert the MR_P_aligned.bam to PacBio formatted bam. First make dataset from all subreads.bam.

```
module load smrttools-release_5.0.1.9578
dataset create ./MR_dataset.xml ./*subreads.bam
```
```
module load pbbam/0.13.2
pbbamify --input=MR_P_aligned.bam --output=MR_P_aligned.pb.bam --verbose-level=1000000 MR_P.fasta MR_dataset.xml
```
(took 103 hours walltime, 60GB RAM - could not find way to thread therefore used less than 1 cpu - *strangely the original aligned.bam file was 35G while the aligned.pb.bam was 351G)*

Need the aligned.pb.bam to be sorted and indexed.

```
module load samtools/1.9 
samtools sort -@4 MR_P_aligned.pb.bam -o MR_P_alignedSort.pb.bam
```
(took 11 hours walltime, 60 GB RAM and 1 cpu)

Polish with arrow algorithm. Then repeat previous mapping step with polished fasta and arrow x 2

```
module load anaconda2/5.0.1
arrow MR_P_alignedSort.pb.bam -r MR_P.fasta -j 24 -o MR_P1.gff -o MR_P1.fasta -o MR_P1.fastq
```
(took 96 hours walltime, 100 GB RAM and 24 cpu)
(second polish took 97 hours walltime, 100 GB RAM and 24 cpu -o MR_P2.fasta)
We ran arrow twice on the primary contigs and did not remove bacterial contigs identified from blast as they were embedded. We found that the pilon correction deleted half the contigs and reduced BUSCO completeness so therefore did not use this output.

An additional polish step was unsuccessfully run using pilon/1.22 software after mapping Illumina reads to assembled primary assembly. The output assembly was reduced in size and highly incomplete on BUSCO results. We therefore proceeded with the arrow polished assembly. Illumina data was derived from a range of isolates, previously presumed to be the same (see variant analysis in this manuscript). It is possible that this caused errors due SNPS.
