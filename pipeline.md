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
### Assemble the transcripts using cufflinks
```
cufflinks -p 40 -g ../genome/Mus_musculus.GRCm39.104.gtf -o B6E10_5A B6E10_5A/accepted_hits.bam 
```
run rest of samples

```
#!/usr/bin/bash
for sample in `cat sampleList.txt`
do
echo $sample
cufflinks -p 40 -g ../genome/Mus_musculus.GRCm39.104.gtf -o $sample $sample/accepted_hits.bam
done
#script name: runcuff.sh
nohup ./runcuff.sh 
```

### merge gtf
```
find . -name transcripts.gtf > assemblies.txt
cuffmerge -p 40 -g ../genome/Mus_musculus.GRCm39.104.gtf -s ../genome/Mus_musculus.GRCm39.dna.toplevel.fa assemblies.txt  

```
### get the different experssion level of each locus and transcripts
```
cuffdiff -o diff_out -b ../genome/Mus_musculus.GRCm39.dna.toplevel.fa -p 40 -L E11,E12,E13,E14,E15,E16,E17,E18,P0,P1,P3,P7 -u merged_asm/merged.gtf \ 
> B6E115A/accepted_hits.bam,B6E115B/accepted_hits.bam,B6E115C/accepted_hits.bam \
> B6E12_5A/accepted_hits.bam,B6E12_5B/accepted_hits.bam,B6E12_5C/accepted_hits.bam \
> B6E135A/accepted_hits.bam,B6E135B/accepted_hits.bam,B6E135D/accepted_hits.bam \
> B6E14_5B/accepted_hits.bam,B6E14_5C/accepted_hits.bam,B6E14_5D/accepted_hits.bam \
> B6E155A/accepted_hits.bam,B6E155C/accepted_hits.bam,B6E155D/accepted_hits.bam \
> B6E16_5A/accepted_hits.bam,B6E16_5B/accepted_hits.bam,B6E16_5C/accepted_hits.bam \
> B6E175A/accepted_hits.bam,B6E175B/accepted_hits.bam,B6E175C/accepted_hits.bam \
> B6E185H/accepted_hits.bam,B6E18_5A/accepted_hits.bam,B6E18_5E/accepted_hits.bam \
> B6P0A/accepted_hits.bam,B6P0B/accepted_hits.bam,B6P0C/accepted_hits.bam \
> B6P1B/accepted_hits.bam,B6P1C/accepted_hits.bam,B6P1D/accepted_hits.bam \
> B6P3A/accepted_hits.bam,B6P3B/accepted_hits.bam,B6P3C/accepted_hits.bam \
> B6P7A/accepted_hits.bam,B6P7B/accepted_hits.bam,B6P7C/accepted_hits.bam
```

### select gtf 
```
grep 'class_code "[uiox]"' merged.gtf >selected.gtf
~/gffread/gffread -w selected.fa -g ../genome/Mus_musculus.GRCm39.dna.toplevel.fa merged_asm/selected.gtf
```
### calculate coding potential

```
~/CPC2_standalone-1.0.1/bin/CPC2.py -i selected.fa -o CPC2_result.txt
less CPC2_result.txt|grep 'noncoding'|awk '{print $1}'> CPC2_id.txt
```
