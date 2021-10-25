## create document
```
cd /s3_d4/caorui/mouse_RNAseq/lncRNA
mkdir genome
```
## Download gtf and mouse reference genome file 
```
cd m39genome
wget http://ftp.ensembl.org/pub/release-104/gtf/mus_musculus/Mus_musculus.GRCm39.104.gtf.gz
wget http://ftp.ensembl.org/pub/release-104/fasta/mus_musculus/dna/Mus_musculus.GRCm39.dna.toplevel.fa.gz
ln -s ../genome/Mus_musculus.GRCm39.dna.toplevel.fa .
```

## use bowtie2 build index 
```bowtie2-build Mus_musculus.GRCm39.dna.toplevel.fa Mus_musculus.GRCm39.dna.toplevel```
## Align the reads using tophat2
note: tophat2 can only run in python2.7, please prepare the py2.7 environment by conda
### Align the first sample E10_5A
```~/tophat-2.1.0.Linux_x86_64/tophat2 -p 40 -I 5000 -G ../genome/Mus_musculus.GRCm39.104.gtf -o B6E10_5A Mus_musculus.GRCm39.dna.toplevel ../X201SC20123423-Z01-F001/raw_data/B6E10_5A_1.fastq ../X201SC20123423-Z01-F001/raw_data/B6E10_5A_2.fastq```
### finish the rest samples
``` 
cp ../quantify/RSEM_result/sampleList.txt .
cat sampleList.txt
```
The sampleList.txt look like below
```
B6E10_5C
B6E12_5A
B6E12_5B
B6E12_5C
B6E14_5B
B6E14_5C
B6E14_5D
B6E16_5A
B6E16_5B
B6E16_5C
B6E18_5A
B6E18_5E
B6E18_5F
```

#### loop script 
```
cat runtophat2.sh
```
```
#!/usr/bin/bash
for sample in `cat sampleList.txt`
do
echo $sample
~/tophat-2.1.0.Linux_x86_64/tophat2 -p 40 -I 5000 -G ../genome/Mus_musculus.GRCm39.104.gtf -o $sample Mus_musculus.GRCm39.dna.toplevel ../X201SC20123423-Z01-F001/raw_data/"$sample"_1.fastq ../X201SC20123423-Z01-F001/raw_data/"sample"_2.fastq
done
```
#### run script
```
nohup ./runtophat2.sh 
```
