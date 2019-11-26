
Use Braker2 predicted amino acid fasta file to submit to Interproscan5
----------------------------------------------------------------------


Take the braker2 output amino acid fasta file (remove the stop codons * as Interproscan5 does not recognise)

Run the (18875) primary and (15,684) secondary braker2 peptides through interproscan

```
module load interproscan/5.34-73.0
module load java/jdk1.8.0_111

interproscan.sh -i APSI_primary.aa.fasta -f tsv,gff3
```

Run the (18875) primary and (15,684) secondary braker2 predicted amino acid fasta through SignalP 
------------------------------------------------------------------

```
module load signalp/4.1f
module load python/2.7.9

signalp -m APSI_primary.mature -t euk APSI_primary.aa.fasta
```
1157 and 925 predicted secreted proteins with  signal peptide and no TM. 

Run the MR_P.mature.aa.fasta (1157) and MR_H.mature.aa.fasta (925) SignalP outputs through EffectorP and ApoplastP
-----------------------------------------------------------------------------------
```
module load effectorp/2.0
module load python/2.7.9

EffectorP.py -i APSI_primary.aa.fasta -o APSI_primary_effectorp.txt -E effectors.fasta -N noneffectors.fasta
```
Number of proteins that were tested: 1157 (925)

Number of predicted effectors: 367 (304)

31.7 (32.9) percent are predicted to be effectors.
```
module load apoplastp/1.0.1
module load python/2.7.9

ApoplastP.py -i APSI_primary.aa.fasta -A apoplastP.fasta -N nonApoplastP.fasta -o APSI_primary_apoplastP.txt
```
Number of proteins that were tested: 1157 (925)

Number of predicted apoplastic proteins: 347 (254)

32.3 (27.5) percent are predicted to be apoplastic

Total effectors that are not apoplastic based on apoplastp and effectorp cross-check = 194 (183)
