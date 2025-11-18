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
```
cd ~/staph_project
conda activate assembly

fastqc raw_data/SRR13530715_1.fastq raw_data/SRR13530715_2.fastq -o qc_results/ -t 2

```
Bu kodları çalıştırarak 2 tane HTML dosyası elde ediyoruz.Bu HTML dosyalarından aldığımız sonuçlar ise şu şekilde :

file:///C:/TEZ/FASTQC_SONUCLARI/SRR13530715_1_fastqc.html
file:///C:/TEZ/FASTQC_SONUCLARI/SRR13530715_2_fastqc.html


## TRIMMING
## ASSEMBLY
## QUAST
```
# Quast çıktısı
Assembly                     ISS_Lab3     Earth_Reference
# contigs (>= 0 bp)          350          3
# contigs (>= 1000 bp)       24           3
# contigs (>= 5000 bp)       16           3
# contigs (>= 10000 bp)      12           3
# contigs (>= 25000 bp)      8            2
# contigs (>= 50000 bp)      6            1
Total length (>= 0 bp)       2729473      2577899
Total length (>= 1000 bp)    2658935      2577899
Total length (>= 5000 bp)    2641089      2577899
Total length (>= 10000 bp)   2609326      2577899
Total length (>= 25000 bp)   2550402      2555029
Total length (>= 50000 bp)   2484292      2516575
# contigs                    30           3
Largest contig               915756       2516575
Total length                 2663205      2577899
Reference length             2577899      2577899
GC (%)                       32.99        33.19
Reference GC (%)             33.19        33.19
N50                          711479       2516575
NG50                         711479       2516575
N90                          92699        2516575
NG90                         128875       2516575
auN                          591639.7     2457486.3
auNG                         611217.8     2457486.3
L50                          2            1
LG50                         2            1
L90                          6            1
LG90                         5            1
# misassemblies              14           0
# misassembled contigs       6            0
Misassembled contigs length  2430257      0
# local misassemblies        5            0
# scaffold gap ext. mis.     0            0
# scaffold gap loc. mis.     0            0
# unaligned mis. contigs     3            0
# unaligned contigs          5 + 12 part  0 + 0 part
Unaligned length             230153       0
Genome fraction (%)          94.289       100.000
Duplication ratio            1.001        1.000
# N's per 100 kbp            11.26        0.00
# mismatches per 100 kbp     129.26       0.00
# indels per 100 kbp         9.83         0.00
Largest alignment            469623       2516575
Total aligned length         2432323      2577899
NA50                         212816       2516575
NGA50                        264587       2516575
NA90                         6442         2516575
NGA90                        35266        2516575
auNA                         226974.7     2457486.3
auNGA                        234485.6     2457486.3
LA50                         5            1
LGA50                        4            1
LA90                         19           1
LGA90                        14           1


```

Quast sonuçlarımız bu şekilde çıktı.Burdaki elde ettiğimiz verilere göre birkaç yorumda bulunmak mümkün.İlk olarak bakmak istediğimiz şey N50 ve L50 değerleri.
N50 = Genomun toplam uzunluğunun yarısına ulaşıldığında kullanılan son contig'in uzunluk değeridir.Düşük bir değer bize bir sürü küçük parçaya ayrıldığını gösterir ki bu iyi değildir.Yüksek bir değer ise bize iyi kaliteli , uzun contigli bir veri kullandığımızı gösterir.

L50 = Genomun toplam uzunluğunun yarısını elde etmek için kaç tane contig kullanmamız gerektiğini belirten değerdir.Düşük bir değer bizi bir sürü küçük parça var sonucuna ulaştırır ki bu pek de iyi değil.Yüksek bir değer ise bize az sayıda büyük uzunlukta contig'lere sahip olduğumuzu gösterir.

                            ıss         world
L50                          2            1      

Bu sonuç bize assembly'mizin iyi bir 

## ANI (AVERAGE NUCLEOTIDE IDENTITY)
## SNP (SINGLE NUCLEOTİDE IDENTITY)
## WHOLE GENOME ALIGNMENT
## PANGENOME (NFCORE-PANGENOME)
## VARIANT ANALYSIS




## 


##


