These commands can be run from this directory once `bwa-parclip` is installed

1. Index The Reference.
2. Align the Reads.

```Shell

# rm ref.fa.bwaparclip.*
bwaparclip.py index ref.fa
bwaparclip.py --reference ref.fa t_R1.fastq.gz t_R2.fastq.gz -t 12

```

Then check the alignments:

```Shell
samtools flagstat bwa-parclip.bam
```
    92215 + 585 in total (QC-passed reads + QC-failed reads)
    0 + 0 duplicates
    92172 + 583 mapped (99.95%:99.66%)
    92215 + 585 paired in sequencing
    46115 + 291 read1
    46100 + 294 read2
    91783 + 0 properly paired (99.53%:0.00%)
    92134 + 579 with itself and mate mapped
    38 + 4 singletons (0.04%:0.68%)
    0 + 0 with mate mapped to a different chr
    0 + 0 with mate mapped to a different chr (mapQ>=5)
