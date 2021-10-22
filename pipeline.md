## create document
cd /s3_d4/caorui/mouse_RNAseq/lncRNA

mkdir genome
## Download gtf and mouse reference genome file 
cd m39genome

wget http://ftp.ensembl.org/pub/release-104/gtf/mus_musculus/Mus_musculus.GRCm39.104.gtf.gz

wget http://ftp.ensembl.org/pub/release-104/fasta/mus_musculus/dna/Mus_musculus.GRCm39.dna.toplevel.fa.gz

ln -s ../genome/Mus_musculus.GRCm39.dna.toplevel.fa .

## use bowtie2 build index 
bowtie2-build Mus_musculus.GRCm39.dna.toplevel.fa Mus_musculus.GRCm39.dna.toplevel
