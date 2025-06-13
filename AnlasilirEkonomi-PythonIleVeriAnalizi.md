# Excel Ayarları

## Excele Veri Çözücü Ekleme 

Eğer veri çözücü excelde gözükmüyorsa Dosya > Seçenekler > Eklentiler > Yönet: Excel eklentiler > Git > Çöz.. ile başlayan 3 eklentiyi aktifleştir. 

Veri çözüsü ile temel istatistik deyince kip değeri modu verir. Ek olarak yinelenenleri kaldırarak frekans sütun elde edilir ve veri çözümleme > histogram seçilerek Veri aralığı tüm veri, bin aralığı frekans sütunu seçilerek çıktı istenen yer belirlenir. Grafik istenirse grafik çıktısı seçilir.)

## Excelde normal dağılımda veri oluşturmak

Normal dağılımda veri oluşturmak için Veri çözümleme > Rastgele sayı üretimi > Değişken sayısı: istediğimiz sütun miktarı, rastgele sayı adedi: istediğimiz sütun adedi, dağılım: dağılım türü seçip histogramını alırsak çan eğrisi gibi bir grafik çıkıyor.

# Ölçek Türleri

- **Nominal:** Herhangi bir önem sıralaması yoktur. Kadın, Erkek gibi.
- **Ordinal:** Önem derecesi olan ölçek. Çok Soğuk, Soğuk, Serin, Sıcak, Çok Sıcak gibi.
- **Metric:** Sayısal işlem yapabildiğimiz türler. Termometre sonucu gibi. Pozitif veya negatif olur.
- **Oran:** Metric ölçeğinin başlangıç oranı sıfır olan pozitif sayılardan oluşan versiyonu. Uzunluk, Hız

# 1 Betimsel istatistik

## 1.1 Merkezi eğilim ölçüleri

Verinin hangi değerler etrafında toplandığını ifade eden yapıdır. 

### Aritmetik ortalama

Bütün değerleri toplayıp değer sayısına böler. Uç değerlere hassastır. Excelde ortalama fonksiyonu ile kullanılır.

### Medyan

Uç değer varsa medyan kullanılır. n tekse n+1/2 inci değer, n çiftse n/2. değer ve n/2+1.inci değerin ortalaması ile bulunur. Excelde Ortanca fonksiyonu ile kullanılır.

### Mod

Bir değişken içindeki verilerin en çok tekrar edenidir, yani en büyük frekansa sahip olanıdır. 

### Frekans

Bir gözlemden kaç tane olduğunu gösterir.

### Kartiller

Veriyi 4 eşit parçaya bölen değerlerdir. Dağılımdaki bir değerin bu 4 eşit parçanın herhangi birinde olma olasılığı %25'tir. 

Excelde kartilleri dörttebirlik fonksiyonu ile çağırıyoruz. Eğer min maks noktalarını istiyorsak DörttebirlikDHL, direk hesaplanması için DörttebirlikHRC fonksiyonunu seçiyoruz.

## 1.2 Merkezi dağılım ölçüleri

Veri kümesinin nasıl bir alanda oluştuğunu gösterir.

### Değişim aralığı (Ranj)

Maksimum değer ile minimum değer arasındaki fark. Ranjın yüksek olması bize o verinin daha ayırt edici olduğunu gösterir.

### Standart sapma

Veri setindeki her gözlemin ortalamayla olan farkının ortalamasıdır. Hem riskin ölçüsüdür, hem de veri setindeki değerlerin ortalamadan ne kadar uzaklıkta dağıldığını gösterir. Standart sapma çok yüksekse veriler ortalamadan aşırı sapmıştır. Standart sapmadaki yükseklik verideki oynaklığın da fazla olduğunu gösterir.  

Excelde standart sapma S ve standart sapma P seçenekleri var. Tüm popülasyonun standart sapmasıysa standart sapma P, örneklem standart sapmasıysa standart sapma S kullanılır.

2 x Standart sapma'dan daha fazla olan veriler uç değer olarak görülür. Bazı kaynaklara göre 3 standart sapma. Mesela, standart sapma 10 ise 20 (veya 30) dan büyük değerler aykırı değerlerdir.

### Varyans

Yaklaşık olarak standart sapmanın karesidir. Eğer popülasyon varyansıysa bütün değişkenlerin ortalamadan farkının karesinin adetine bölünmesiyle hesaplanır. Eğer örneklem için kullanılıyorsa adet - 1 e bölünür. 

Excelde tüm popülasyonun varyansıysa var P, örneklem ise var S kullanılır.

### Çarpıklık (Skewness)

![image](https://github.com/user-attachments/assets/b7926963-b61a-4320-b0de-6b3e4f57b962)

Ortadaki veri simetrik dağılım. Simetrik dağılımda uç değer olmaz. Ortalama, Medyan ve Mode çok yakın veya eşit.
Negatif yönde çarpık olursa ortalama < medyan < mode şeklinde bir dağılım olur.
Pozitif yönde çarpık olursa mode < medyan < ortalama şeklinde bir dağılım olur.

![image](https://github.com/user-attachments/assets/4cb0c5cc-5fe5-4025-af0b-35f3572890e0)

Pearson çarpıklık ölçüsü, Xort ile mod değerinin farkının standart sapmaya bölümü veya Xort ile medyan değerinin farkının 3 katının standart sapmaya bölümü ile bulunur.
Pearson çarpıklık ölçüsü pozitif ise sağa çarpık negatif ise sola çarpıktır.
Excelde çarpıklık fonksiyonuyla bulunabilir.





### 
## 
# 
