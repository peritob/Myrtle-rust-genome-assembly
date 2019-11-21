Long read sequence data from PacBio SMRT cells (18 X RSII plus 11 X Sequel)
---------------------------------------------------------------------------

### Preparing the data before assembly

Convert RSII bax.h5 files to subreads.bam 
```
module load smrt/5.0.1
bax2bam data_one.bax.h5
bax2bam data_two.bax.h5 
etc.
```
Total of 162 GB of subreads.bam files for later use.

Extract RSII bax.h5 to fasta and arrow files
```
module load zlib/1.2.8
module load hdf5/1.8.16
module load DEXTRACTOR
dextract -f -a *bax.h5
```
Extract Sequel subreads.bam to fasta and arrow files
```
module load zlib/1.2.8
module load hdf5/1.8.16
module load DEXTRACTOR
dextract -f -a *subreads.bam
```
Output was 70 GB of arrow files and 74.4 GB of fasta files.

Convert all 162 GB subreads.bam to fastq for later use.
```
module load bamtools/2.4.0
bamtools convert -format fastq -in data_one.subreads.bam -out data_one.subreads.fastq
bamtools convert -format fastq -in data_two.subreads.bam -out data_two.subreads.fastq
etc.
```
Make .xml data file from all subreads.bam
```
module load smrttools/5.1.0/
dataset create MR_data.xml *subreads.bam
```
Map all fasta to myrtle rust mitochondrial genome to remove before assembly. Just want genomic DNA sequences.
```
module load bbmap/37.98
module load java/jdk1.8.0_111
module load samtools/1.6
mapPacBio.sh in=read_one.fasta outu=unmappedread.fasta outm=mappedread.fasta maxlen=6000
mapPacBio.sh in=read_two.fasta outu=unmappedread.fasta outm=mappedread.fasta maxlen=6000
etc.
```
If any part of the read maps to the mitochondrial genome – the read should be derived from the mitochondrial genome. Therefore extract headers from the mapped output. Use header list to extract reads from original read files. Use the reads (minus extracted mapped reads) to assemble the genome. Reduced fasta output to 74.2 GB of data.
