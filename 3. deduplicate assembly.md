Deduplicate the diploid assembly with Purge Haplotigs
-----------------------------------------------

Map all the fastq raw pacbio reads to canu assembled genome and get alignment bam file. Minimap2 only takes fasta/fastq but is much faster and less compute intensive than blasr and bwa-mem. (Next time use fasta raw reads rather than fastq - minimap2 prefers) 
```
module load minimap2/2.3
module load samtools/1.6

minimap2 -ax map-pb APSI_diploid.fasta *.fastq \
| samtools view -hF 256 - \
| samtools sort -@ 8 -m 1G -o APSI_diploid_aligned.bam -T tmp.ali
```
(took walltime 29 hours, 100GB RAM and 4 cpu)

Process diploid assembly to separate allelic haplotigs using Purge Haplotigs
```
module load samtools/1.6
module load purge_haplotigs/1.0
module load bedtools/2.25.0
module load mummer4/4.0.0beta2
module load minimap2/2.3

purge_haplotigs  readhist  -b APSI_diploid_aligned.bam  -g APSI_diploid.fasta -t 8
```
Make histogram with data output and manually check haploid/diploid coverage for next steps (low, medium, high flags)

#step one

```
purge_haplotigs contigcov -i APSI_diploid_aligned.bam.gencov -l 10 -m 53 -h 100
```
#step two

```
minimap2 -t 4 -p 1e-5 -f 0.001 -N 1000 tmp_purge_haplotigs/assembly.4G.mmi tmp_purge_haplotigs/suspects.fasta > tmp_purge_haplotigs/minimap2.4G.paf
```
#step three

```
purge_haplotigs purge -g APSI_diploid.fasta  -c coverage_stats.csv -t 8 -I 4G -a 65 #Note that default -a 70
```

The purged assembly produced a primary contig fasta file (APSI_primary.fasta), secondary contig fasta (APSI_secondary.fasta) and artefacts.fasta. A BUSCO analysis identified conserved genes on contigs that were absent in the APSI_primary.fasta and present in the APSI_secondary.fasta. Custom python scripts were used to extract these contigs from the APSI_secondary.fasta and include in the APSI_primary.fasta.
 
