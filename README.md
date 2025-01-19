Download files using `curl` to a new `in/` folder:
```sh
mkdir -p in
cd in
curl -O https://hgdownload.soe.ucsc.edu/goldenPath/hg38/chromosomes/chr7.fa.gz
curl -O https://gear-genomics.embl.de/data/.slides/R1.fastq.gz
curl -O https://gear-genomics.embl.de/data/.slides/R2.fastq.gz
cd ..
```

Unzip the files (bcftools struggled with them in zipped form) to a new `data` folder:
```sh
mkdir -p data
zcat in/chr7.fa.gz > data/chr7.fa
```

Prepare index of `chr7.fa` using `bwa` and align reads R1 and R1:
```sh
bwa index data/chr7.fa
bwa mem data/chr7.fa in/R1.fastq.gz in/R2.fastq.gz > data/aln.sam
```

Sort the alignment file and make an index using `samtools`:
```sh
samtools sort -o data/aln.bam data/aln.sam
samtools index data/aln.bam
```

Call variants using `bcftools` and index it:
```sh
bcftools mpileup -Ob -f data/chr7.fa -o data/pileup.bam data/aln.bam
bcftools call -mv -Ou -o data/calls.vcf data/pileup.bam
```

Finally, put all SNPs to a new file:
```sh
bcftools view -v snps data/calls.vcf > data/snps.vcf
```

The file `data/snps.vcf` was uploaded to the [VEP tool](https://www.ensembl.org/Multi/Tools/VEP?db=core). A query was run with Ensembl/GENCODE transcripts with no additional configurations. A [link to the job results](https://www.ensembl.org/Homo_sapiens/Tools/VEP/Results?tl=wMnPqmM6wvtGxgXt-10762971).

Answer to the excercise 1:  
My opinion is the option "chr7, 2915243, G, A", because it leads into a stop codon in a gene for [caspase recruitment domain family member 11](https://www.ensembl.org/Homo_sapiens/Transcript/ProteinSummary?db=core;g=ENSG00000198286;r=7:2915193-2915293;t=ENST00000396946;tl=wMnPqmM6wvtGxgXt-10762971). That I can imagine to lead to some disease, because the modified gene produces shorthened and highly possibly malfunctional proteins.