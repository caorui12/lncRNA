## create document
```cd /s3_d4/caorui/mouse_RNAseq/lncRNA```

```mkdir genome```
## Download gtf and mouse reference genome file 
```cd m39genome```

```wget http://ftp.ensembl.org/pub/release-104/gtf/mus_musculus/Mus_musculus.GRCm39.104.gtf.gz```

```wget http://ftp.ensembl.org/pub/release-104/fasta/mus_musculus/dna/Mus_musculus.GRCm39.dna.toplevel.fa.gz```

```ln -s ../genome/Mus_musculus.GRCm39.dna.toplevel.fa .```

## use bowtie2 build index 
```bowtie2-build Mus_musculus.GRCm39.dna.toplevel.fa Mus_musculus.GRCm39.dna.toplevel```
## Align the reads using tophat2
note: tophat2 can only run in python2.7, please prepare the py2.7 environment by conda
### Align the first sample E10_5A
```~/tophat-2.1.0.Linux_x86_64/tophat2 -p 40 -I 5000 -G ../genome/Mus_musculus.GRCm39.104.gtf -o E10_5A Mus_musculus.GRCm39.dna.toplevel ../X201SC20123423-Z01-F001/raw_data/B6E10_5A_1.fastq ../X201SC20123423-Z01-F001/raw_data/B6E10_5A_2.fastq```
