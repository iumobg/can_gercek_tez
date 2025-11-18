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
Genom verilerimiz ILLUMINA makinesi kullanarak elde edilmiştir.
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

BURAYA RESİMLERİ YÜKLEYECEĞİM


Bu sonuçlarda bakmamız gereken belli değerler var.

Per Base Sequence Quality : Grafiğin yeşil gölgede ilerleyip ilerlemediğine bakarız , eğer çıkan çubuklarda sonda düşüş varsa TRIMMING yapmak gerekebilir.
Per Sequence Quality Scores : Grafiğin pik yaptığı noktaya bakıyoruz , > 30 olmalı
Adapter Content : Grafiğin düz yani Adapter'in olmaması gerekiyor,eğer varsa TRIMMING gerekebilir.

Bizim elde ettiğimiz verilerde sonuçlar şu şekilde :
```
                                        ISS           WORLD
Per Base Sequence Quality              YEŞİL          YEŞİL
Per Sequence Quality Scores             36             36
Adapter Content                         -              -
```
Tüm bunlar bizim elde etmek istediğimiz sonuçlardı , sadece Per Base Sequence Quality grafiklerinde sonlara doğru düşüş yaşanıyor , bu yüzden de TRIMMING yapacağız.



## TRIMMING
Bu aşamada elde ettiğimiz değerlerde istediğimiz gibi bir sonuç alamazsak TRIMMING aşamasında istediğimiz sonucu almamızı engelleyen parçalardan kurtulacağız.
```
cd ~/staph_project
conda activate assembly

trimmomatic PE -threads 4 \
  raw_data/SRR13530715_1.fastq \
  raw_data/SRR13530715_2.fastq \
  trimmed_data/SRR13530715_1_paired.fastq \
  trimmed_data/SRR13530715_1_unpaired.fastq \
  trimmed_data/SRR13530715_2_paired.fastq \
  trimmed_data/SRR13530715_2_unpaired.fastq \
  LEADING:3 TRAILING:3 SLIDINGWINDOW:4:20 MINLEN:50

```
Burada gördüğünüz gibi paired_fastq ve unpaired_fastq dosyaları var , bunların arasında şöyle bir durum var.
Biz çalışmalarımızda paired_fastq dosyalarını kullanıyoruz , unpaired_fastq dosyalarını değil . "unpaired_fastq" dosyalarının oluşturulma sebebi aslında "trimmomatic" 'in kendi tasarımıdır aslında.Bu unpaired_fastq dosyalarını nadir de olsa single-end assembly,coverage değerini arttırmak ve meta-genom çalışmalarında kullanabiliyoruz.
- unpaired_fastq dediğimiz dosyalar TRIMMING aşamasında genomda silinen bölgeler demek değildir.Şimdi bir forward ve bir reverse primer read'leri düşünelim.Burada primerlerden biri TRIMMING aşamasından sonra içindeki okunamayan-yanlış veriler silindiğinde MINLEN:50 'yi karşılamazsa o primer silinir.Geriye kalan ve eşi MINLEN:50 ' yi gerçekleştiremediği için silinen primer'imiz bizim " unpaired " verimiz oluyor.
- paired_1 ve paired_2 dosyalarının içeriklerine bakarsanız hepsi birbirinin eşi diziler olarak gözükecektir,fakat unpaired dosyalarında eş dizileri göremeyeceksiniz çünkü MINLEN:50 ' yi sağlayamadıkları için eş dizileri silindi.
- Eğer okunamayan - yanlış bölgeler silindiğinde dahi MINLEN:50 değerini sağlıyorsa bu sefer paired_fastq dosyası olarak çıkacaklardı.SLIDINGWINDOW:4:20 demek ise 4 bazlık frame'de skor değeri 20'den düşük olanları kes demektir.

TRIMMING aşaması bittiğinde bize bir "surviving pairs" değeri verir , biz bu değerin > 95 olmasını isteriz.Bunun nedeni verimizden ne kadar az baz silinirse o kadar iyidir.Bizim sonucumuz % 96.84 çıktı , her şey yolunda.




## ASSEMBLY
Assembly kısa okuma parçalarının birleştirilip contig - scaffold'ların oluşturulduğu aşamadır.Assembly aşamasında pair-end dosyalarımızı SPADEs sayesinde birleştireceğiz.SPADEs bir genom birleştirme modülüdür.2 tür assembly türü vardır bunlar Referance-Guided Assembly ve De Novo Genome Assembly şeklindedir.Biz projemizde De Novo Genome Assembly yapıyoruz.
-Referance-Guided Assembly : Read'lerin referans içindeki en benzer bölgeye benzerliğine göre gruplandırılması ve sonrasında read'lerin bir referansa göre hizalayarak bir araya getirilmesi işlemidir.
-De Novo Genome Assembly : Referans kullanmadan tam uzunlukta sequence oluşturmak için read'lerin bir araya getirilmesidir.Aşamaları şu şekildedir :
    -Sequencing read'leri contiguous sequence'lere ekleme ( contig )
    -Contig'leri daha büyük sequence'lere bağla (scaffold)
    -Scaffold'ları birbirine bağla (kromozom)
   

  De novo genome assembly yönteminde "Tandem Repeat" problemi çıkabiliyor.Tandem Repeat bazı sequence'lerin birden fazla kez tekrarlanarak okunması demektir.Bunun çözümü olarak da "coverage" değerini arttırmaktır.Coverage değeri 10 veya üstündeyse iyi demektir.
```
                read uzunluğu  x  read sayısı(bp)
   Coverage =        ---------------------
                      genom uzunluğu (bp)
```
Normalde sırasıyla  k-mer seçimi (21,55) , de Bruijn Graph , contig assembly , scaffold oluşturma , polishing  aşamalarının yapılması gerekmektedir.Fakat SPADEs kullandığınız zaman tüm bu aşamaları tek başına yapmaktadır.
```
spades.py \
  -1 trimmed_data/SRR13530715_1_paired.fastq \
  -2 trimmed_data/SRR13530715_2_paired.fastq \
  -o assembly/ISS_assembly \
  --isolate \
  -t 8 \
  -m 24
```
Burada "--isolate" seçeneği bize optimize bir mod sağlar (--careful da kullanılabilir) , "-t" işlemciden kaç çekirdek , "-m" ise kaç GB ram kullanacağımızı belirlememizi sağlar.
## QUAST
Quast aşaması Genome Assembly kalitesini değerlendirmek için kullandığımız araçlardan biridir.Farklı Assembly'leri karşılaştırmak ve Contig'lerin nasıl bir biçimde (uzun-sıralı-bütün) olduğunu görmek için bize sayısal veriler verir ve biz de bu sayısal veriler üzerinden değerlendirmemizi yaparız.
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
```
                           ıss          world
N50                       711479       2516575
```
Sonucumuza baktığımızda 2 değerimizin de yüksek olduğunu görüyoruz, bu da bize uzun contig'li ve iyi kalitede bir veri kullandığımızı gösteriyor

L50 = Genomun toplam uzunluğunun yarısını elde etmek için kaç tane contig kullanmamız gerektiğini belirten değerdir.Düşük bir değer bizi bir sürü küçük parça var sonucuna ulaştırır ki bu pek de iyi değil.Yüksek bir değer ise bize az sayıda büyük uzunlukta contig'lere sahip olduğumuzu gösterir.
```
                            ıss         world
L50                          2            1      
```
Sonucumuza baktığımızda çok az sayıda contig olduğunu gösteriyor ki bu bizim için sevindirici haber çünkü az olması demek genomun çok fazla parçaya bölünmemiş olduğunu gösteriyor bize.



## ANI (AVERAGE NUCLEOTIDE IDENTITY)
## SNP (SINGLE NUCLEOTİDE IDENTITY)
## WHOLE GENOME ALIGNMENT
## PANGENOME (NFCORE-PANGENOME)
## VARIANT ANALYSIS




## 


##


