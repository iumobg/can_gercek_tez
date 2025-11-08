# STAPHYLOCOCCUS SAPROPHYTICUS BAKTERISININ ISS VE DUNYADAKI GENOMLARININ KARŞILAŞTIRILIP UZAYDAKİ RADYASYONUN BAKTERININ GENOMUNA VE PATOJENİTESİNE OLAN ETKISININ ARASTIRILMASI

## TEZİN AMACI
Staphylococcus Saprophyticus patojenin uzaydaki radyasyona bağlı olarak genomunda bir değişiklik var mı bunu tespit etmek ve bu genom değişiklerininin ne gibi sonuçlara yol açabileceğinin araştırılması.

## STAPHYLOCOCCUS SAPROPHYTICUS BAKTERISININ GENEL OZELLIKLERI
Staphylococcus Saprophyticus bir fırsatçı patojendir,yani normalde zararsızdır fakat hedef konumuna ulaşınca hastalığa yol açar.Gram pozitif bir bakteridir.İdrar yolu enfeksiyonu hastalıklarına yol açar ve genellikle mesaneye yapışmasıyla bilinir.Genç kadınlarda daha sık görülür.Cinsel aktivite bu bakterinin bulaşmasında etkilidir.

## ROAD MAP
NASA GeneLab sitesinden ilk önce bakterimizi seçtik , sonra tezimiz için çalışmaya uygun bir genomu ISS çalışmaları içinden bulduk.Dünyadaki genomumuz ise bakterimizin referans genomu olacak.
Bu genomlarımızı sırasıyla :

FASTQC ( genomlarımızın kalitesi hakkında bilgi ediniyoruz )
TRIMMING (hatalı veya eksik genom parçalarını çıkartıyoruz )
ASSEMBLY (de novo assembly , spades )
QUAST (
ANI (average nucleotide identity)
SNP (single nucleotide polymorfism)
PANGENOME

aşamalarından geçireceğiz.
Bu aşamaları yaparken Nextflow'dan ve onun kütüphanelerinden yararlanacağız(bkz.nf-pangenome).
Tüm bu aşamalar gerçekleştirildikten sonra genomdaki (varsa) radyasyona bağlı değişiklikleri tablolar ve sayısal verilerle somutlaştıracağız.
Somutlaştırdığımız verilere bağlı olarak da bu değişimlerin bakterinin patojenitesinde ne gibi değişikliklere yol açabileceğini araştıracağız.






## 


##


