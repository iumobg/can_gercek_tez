# STAPHYLOCOCCUS SAPROPHYTICUS BAKTERISININ ISS VE DUNYADAKI GENOMLARININ KARŞILAŞTIRILIP UZAYDAKİ RADYASYONUN BAKTERININ GENOMUNA VE PATOJENİTESİNE OLAN ETKISININ ARASTIRILMASI

## TEZİN AMACI
Staphylococcus Saprophyticus patojenin uzaydaki radyasyona bağlı olarak genomunda bir değişiklik var mı bunu tespit etmek ve bu genom değişiklerininin ne gibi sonuçlara yol açabileceğinin araştırılması.

## STAPHYLOCOCCUS SAPROPHYTICUS BAKTERISININ GENEL OZELLIKLERI
Staphylococcus Saprophyticus bir fırsatçı patojendir,yani normalde zararsızdır fakat hedef konumuna ulaşınca hastalığa yol açar.Gram pozitif bir bakteridir.İdrar yolu enfeksiyonu hastalıklarına yol açar ve genellikle mesaneye yapışmasıyla bilinir.Genç kadınlarda daha sık görülür.Cinsel aktivite bu bakterinin bulaşmasında etkilidir.

## ROAD MAP
NASA GeneLab sitesinden ilk önce bakterimizi seçtik , sonra tezimiz için çalışmaya uygun bir genomu ISS çalışmaları içinden bulduk.Dünyadaki genomumuz ise bakterimizin referans genomu olacak.
Bu genomlarımızı sırasıyla :

-FASTQC ( genomlarımızın kalitesi hakkında bilgi ediniyoruz )  

-TRIMMING (hatalı veya eksik genom parçalarını çıkartıyoruz )  

-ASSEMBLY (de novo assembly , spades )  

-QUAST (assembly kalitesini kontrol,n50 L50 değerlerine bakmak)  

-ANI (average nucleotide identity)  

-SNP (single nucleotide polymorfism)  

-Whole Genome Alignment (MUMmer)  

-PANGENOME (nfcore-pangenome)  


aşamalarından geçireceğiz.
Bu aşamaları yaparken Nextflow'dan ve onun kütüphanelerinden yararlanacağız(bkz.nfcore-pangenome).
Tüm bu aşamalar gerçekleştirildikten sonra genomdaki (varsa) radyasyona bağlı değişiklikleri tablolar,grafikler ve sayısal verilerle somutlaştıracağız.
Somutlaştırdığımız verilere bağlı olarak da bu genom değişikliklerinin bakterinin patojenitesinde ne gibi değişikliklere yol açabileceğini araştıracağız.


## FASTQC



## TRIMMING
## ASSEMBLY
## QUAST
```
# Quast çıktısı
tie2-build --threads 4 ref_file ref_name
bowtie2 -x ref_name -1 fastq1 -2 fastq2 -S sam_file

samtools view -uS -o bam_file sam_file
samtools sort -@ 4 -T temp_file -o sorted_bam bam_file
samtools index sorted_bam

bcftools mpileup -f ref_file sorted_bam | bcftools call -mV -Ob -o bcf_file
bcftools convert -O v -o vcf_file bcf_file
bgzip -c vcf_file > gz_file
tabix -p vcf gz_file

bcftools consensus -f ref_file gz_file > consensus_fasta

prokka --outdir outputDir --prefix prokkaAnnotes --force consensus_fasta
```

Quast sonuçlarımız bu şekilde çıktı.
## ANI (AVERAGE NUCLEOTIDE IDENTITY)
## SNP (SINGLE NUCLEOTİDE IDENTITY)
## WHOLE GENOME ALIGNMENT
## PANGENOME (NFCORE-PANGENOME)
## VARIANT ANALYSIS




## 


##


