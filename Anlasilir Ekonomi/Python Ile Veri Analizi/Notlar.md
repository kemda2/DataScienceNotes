# Excel Ayarları

## Excele veri çözücü ekleme 

Eğer veri çözücü excelde gözükmüyorsa Dosya > Seçenekler > Eklentiler > Yönet: Excel eklentiler > Git > Çöz.. ile başlayan 3 eklentiyi aktifleştir. 

Veri çözüsü ile temel istatistik deyince kip değeri modu verir. Ek olarak yinelenenleri kaldırarak frekans sütun elde edilir ve veri çözümleme > histogram seçilerek Veri aralığı tüm veri, bin aralığı frekans sütunu seçilerek çıktı istenen yer belirlenir. Grafik istenirse grafik çıktısı seçilir.)

## Excelde normal dağılımda veri oluşturmak

Normal dağılımda veri oluşturmak için Veri çözümleme > Rastgele sayı üretimi > Değişken sayısı: istediğimiz sütun miktarı, rastgele sayı adedi: istediğimiz sütun adedi, dağılım: dağılım türü seçip histogramını alırsak çan eğrisi gibi bir grafik çıkıyor.

# Ölçek türleri

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

![carpiklik](./images/carpiklik.png)

Ortadaki veri simetrik dağılım. Simetrik dağılımda uç değer olmaz. Ortalama, Medyan ve Mode çok yakın veya eşit.
Negatif yönde çarpık olursa ortalama < medyan < mode şeklinde bir dağılım olur.
Pozitif yönde çarpık olursa mode < medyan < ortalama şeklinde bir dağılım olur.

![pearsoncarpiklik](./images/pearsoncarpiklik.png)

Pearson çarpıklık ölçüsü, Xort ile mod değerinin farkının standart sapmaya bölümü veya Xort ile medyan değerinin farkının 3 katının standart sapmaya bölümü ile bulunur.
Pearson çarpıklık ölçüsü pozitif ise sağa çarpık negatif ise sola çarpıktır.
Excelde çarpıklık fonksiyonuyla bulunabilir.

### Basıklık (Kurtosis)

![kurtosis](./images/kurtosis.png)

![positivenegativekurtosis](./images/positivenegativekurtosis.png)

![leptokurticplatykurtic](./images/leptokurticplatykurtic.png)

Kurtosis değerinin sınırı 3 olarak kabul edilir.

[Normal dağılım hesaplamak için site](https://www.desmos.com/calculator/jxzs8fz9qr?lang=tr)

Excelde basıklık fonksiyonuyla kullanılır. 3'ten büyükse sivri bir dağılım küçükse basık bir dağılım var deriz.

## 1.3 Ek bilgi

Python kullanım fonksiyonları;

- ortalama: veri.mean()
- medyan: veri.median()
- %25 quantile: veri.quantile(q=0.25)
- Medyan %50 quantile: veri.quantile(q=0.50)
- %75 quantile: veri.quantile(q=0.75)
- standart sapma: veri.std()
- varyans: veri.var()
- basıklık: veri.kurtosis()
- çarpıklık: veri.skew()

Eğer dağılımını bilmediğin bir Tarih sıralı verinin histogramıyla karşılaşırsan, her satırın bir öncekiyle olan farkını bulabilirsin. Farklarını gösterince normal dağılıma benzer bir dağılıma ulaşırsın.

## 1.4 Örnekler

```Python
import pandas as pd
import matplotlib.pyplot as plt

veri = pd.read_excel("/kaggle/input/satis-deneme/Satis.xlsx")

veri.describe()
```

![describe](./images/describe.png)


```Python
veri["Cinsiyet"].describe()
```

![cinsiyetdescribe](./images/cinsiyetdescribe.png)

```Python
veri["Cinsiyet"].value_counts()
```

![cinsiyetvalues](./images/cinsiyetvalues.png)

```Python
veri["Cinsiyet"].value_counts(normalize = True) *100
```

![cinsiyetvaluesnor](./images/cinsiyetvaluesnor.png)

```Python
plt.hist(veri["Yaş"], bins = 50)
plt.show()
```

![yashist](./images/yashist.png)

```Python
veri["Yaş"].skew()

# 0.024461786786661114
```

```Python
veri["Yaş"].kurtosis()

# -0.18269821709302603
```

```Python
veri.groupby("Cinsiyet").mean()
```

![image](./images/cinsmean.png)

# 2 Çıkarımsal istatistik

Örnekleme yapılarak popülasyon için çıkarım yapılan istatistik türüdür. 

## 2.1 Merkezi limit teoremi

Uygun bir şekilde seçilen örneklem yapısı tüm popülasyona benzeyecektir.

Eğer bir popülasyonda anket almak gibi defalarca veri almaya devam edersek örneklemin ortalamasının dağılımı bizi normal dağılıma götürür.

Popülasyondan 30 adet örneklemin ortalamasını alırsak normal dağılıma ulaşırız.

### Örnekler

```Python
import numpy as np
import matplotlib.pyplot as plt

# Uniform bir dağılım elde etmek
yas = np.random.uniform(low = 18, high = 72, size = 40000)
yas 

# array([69.06428513, 35.3954505 , 21.27415043, ..., 49.2332403 ,
#        42.29571594, 20.79167345])
```

```Python
yas.mean()

# 44.96710755555197
```

```Python
plt.hist(yas)
plt.show()
```

![image](./images/yashist2.png)

```Python
import random
orneklem = random.choices(yas,k=5)
orneklem

# [34.32305248567211,
#  64.0623590234868,
#  33.06747583685961,
#  56.207328531842954,
#  59.87675708682805]
```

```Python
import random
orneklem = [np.mean(random.choices(yas, k=1)) for _ in range(1000)] # 1000 kez 1 örneklem çekiyor. 
plt.hist(orneklem)
plt.show()
```
![image](./images/hist1.png)

```Python
import random
orneklem = [np.mean(random.choices(yas, k=2)) for _ in range(1000)] # 1000 kez 2 örneklem çekiyor. 
plt.hist(orneklem)
plt.show()
```

![image](./images/hist2.png)

```Python
import random
orneklem = [np.mean(random.choices(yas, k=10)) for _ in range(1000)] # 1000 kez 10 örneklem çekiyor. 
plt.hist(orneklem)
plt.show()
```

![image](./images/hist10.png)

```Python
import random
orneklem = [np.mean(random.choices(yas, k=30)) for _ in range(1000)] # 1000 kez 30 örneklem çekiyor. 
plt.hist(orneklem)
plt.show()
```

![image](./images/hist30.png)

## 2.2 Standart hata nedir?

![image](./images/shata.png)

Çekilen örneklemlerin sayısı arttıkça örneklem kümesinin ortalaması popülasyon ortalamasına yakınsar. Örneklem çektikçe örneklem kümesinin ortalaması ile popülasyon ortalamasına yaklaşır. Bir popülasyondan seçilebilecek olası örneklemlerin ortalamalarınının standart sapmasına standart hata denir.

Düşük standart sapma ve düşük standart hata isteniyor.

[Dağılım oluşturma sitesi](https://onlinestatbook.com/stat_sim/sampling_dist/index.html)

## 2.3 Seaborn kütüphanesi

```Python
import seaborn as sns
import matplotlib.pyplot as plt
import numpy as np

x = np.random.normal(35,1,10000) #ortalama, standart sapma, adet

sns.histplot(x)
plt.show()
```
![image](./images/seahist.png)

```Python
sns.displot(x, kde = True, color = "r")
plt.show()
```

![image](./images/seahist2.png)

```Python
sns.kdeplot(x, color = "g")
plt.show()
```

![image](./images/seahist3.png)

```Python
sns.boxplot(x=x, color="g")
plt.show()
```

![image](./images/seahist4.png)

```Python
sns.lineplot(x=np.arange(len(x)), y=x)
plt.show()
```

![image](./images/seahist5.png)

## 2.4 z-score

![image](./images/z-score.png)

![image](./images/z-scoreformula.png)

Excelde z-score çevirimini standartlaştırma formülü ile yapıyoruz. **Standartlastırma exceli var.**

### Örnekler

```Python
import numpy as np
import matplotlib.pyplot as plt
import seaborn as sns

np.random.seed(35)

veri = np.random.normal(35,2,40000)
veri

# array([31.22052658, 34.17281564, 33.46794798, ..., 30.91770669,
#        36.07625002, 31.63870381])

```

```Python
sns.displot(veri, kde = True, height=8, aspect=1.5)
plt.title("Veri Dağılım Grafiği", fontsize=15, loc="right", c="red")
plt.xlabel("Veriler", fontsize=15, c="red")
plt.ylabel("Frekanslar", fontsize=15, c="red")
plt.axvline(x=np.mean(veri), linestyle = "--", linewidth = 2.5, label = "Ortalama", c="red")
plt.axvline(x=np.mean(veri) - np.std(veri), linestyle = "--", linewidth = 2.5, label = "Ortalama - 1 Standart Sapma", c="orange")
plt.axvline(x=np.mean(veri) + np.std(veri), linestyle = "--", linewidth = 2.5, label = "Ortalama + 1 Standart Sapma", c="orange")
plt.legend() # axvline çizgi için gerekli
plt.show()
```

![image](./images/veridagilim.png)

```Python
veriz = (veri - np.mean(veri)) / np.std(veri)

sns.displot(veriz, kde = True, height=8, aspect=1.5)
plt.title("Veri Dağılım Grafiği", fontsize=15, loc="right", c="red")
plt.xlabel("Veriler", fontsize=15, c="red")
plt.ylabel("Frekanslar", fontsize=15, c="red")
plt.xlim(-3,3)
plt.axvline(x=np.mean(veriz), linestyle = "--", linewidth = 2.5, label = "Ortalama", c="red")
plt.axvline(x=np.mean(veriz) - np.std(veriz), linestyle = "--", linewidth = 2.5, label = "1 Standart Sapma", c="black")
plt.axvline(x=np.mean(veriz) + np.std(veriz), linestyle = "--", linewidth = 2.5, c="black")
plt.axvline(x=np.mean(veriz) - 2*np.std(veriz), linestyle = "--", linewidth = 2.5, label = "2 Standart Sapma", c="blue")
plt.axvline(x=np.mean(veriz) + 2*np.std(veriz), linestyle = "--", linewidth = 2.5, c="blue")
plt.legend() # axvline çizgi için gerekli
plt.tight_layout()
plt.show()
```

![image](./images/veridagilim2.png)

## 2.5 z-table

z-score tablosu;

![image](./images/z-table.png)

[Z skor online hesaplayıcı](https://homepage.divms.uiowa.edu/~mbognar/applets/normal.html)

### Örnekler

Ortalaması 5,3 ve sapması 1 olan normal dağılımda P(x<4.5) olasılğını arıyoruz.

z-score = (4,5-5,3)/1 = -0,8

4,5 değerinin ortalama değeri olan 5,3 ile arasındaki yüzde %28,81 olarak bulunur. %50 den bu değer çıkarılırsa %21,19 olarak olasılığı bulunur.

![image](./images/cozum.png)

```Python
from scipy.stats import norm

olasılık = norm(loc=5.3, scale=1).cdf(4.5)
olasılık

# 0.21185539858339675
```

4.5 ile 6.5 arasında olma olasılığı;

```Python
from scipy.stats import norm

olasılık = norm(loc=5.3, scale=1).cdf(6.5) - norm(loc=5.3, scale=1).cdf(4.5)
olasılık

# 0.673074931194895
```

⚠⚠⚠ cdf hep sol tarafta kalan alanın olasılığını verir.

-----

Bir kamu bankası personel alımı için ilana çıkmış ve iş için başvuran çok sayıda adaya, ekonomi, maliye, finans, işletme, hukuk alanlarında hazırlanan bir test uygulamıştır. Adayların uygulanan testte aldıkları puanların ortalaması 60 ve standart sapması 12 hesaplanmıştır. Adayların aldıkları puanların normal dağıldığı bilindiğine göre;

1. Tesadüfen seçilecek bir adayın 60-70 arasında puan almış olma olasılığı nedir?

Ortalama 60 olduğuna göre;
* 60'ın z-score değeri (60-60) / 12 ile 0 bulunur.
* 70'ın z-score değeri (70-60) / 12 ile 0,83 bulunur.

0,83 z-score için tablodaki değer % 29,67 bulunmuştur.

![image](./images/kamu1.png)

```Python
from scipy.stats import norm

olasılık = norm(loc=60, scale=12).cdf(70) - 0.5
olasılık

# 0.29767161903635686
```

2. Tesadüfen seçilecek bir adayın 45-60 arasında puan almış olması olasılığı nedir?

* 60'ın z-score değeri (60-60) / 12 ile 0 bulunur.
* 70'ın z-score değeri (45-60) / 12 ile -1,25 bulunur.

1,25 z-score  için tablodan % 39,44 olarak bulunmuştur.

![image](./images/kamu2.png)

```Python
from scipy.stats import norm

olasılık = 0.5 - norm(loc=60, scale=12).cdf(45)
olasılık

# 0.39435022633314465
```

3. Tesadüfen seçilecek bir adayın 45'den az puan alma olasılığı nedir?

![image](./images/kamu3.png)

```Python
from scipy.stats import norm

olasılık = norm(loc=60, scale=12).cdf(45)
olasılık

# 0.10564977366685535
```

-----

İmal edilen ampullerin ortalama ömrü 800 saat, standart sapması 40 saattir. Ampulün ömrünün normal dağılım gösterdiği bilindiğine göre bir ampulün;

⚠⚠⚠ DİKKAT bu tarz sorularda birimler aynı olmalı.

1. 778 saatten daha fazla bir ömre sahip olması olasılığını bulunuz?

778'in z-score değeri (778-800) / 40 ile -0,55 bulunur. Tablodan 0,55 z-score değerinin yüzdesi %21 olarak bulunur. %21 + %50 = %71 bulunur.

![image](./images/ampul1.png)

```Python
from scipy.stats import norm

ust = norm(loc=800, scale=40).cdf(1600) # 1600 1, 800 0
alt = norm(loc=800, scale=40).cdf(778)
olasılık = ust - alt
olasılık

# 0.7088403132116536
```

2. 834 saatten daha az bir ömre sahip olması olasılığını bulunuz?

834'ün z-score değeri (834-800) / 40 ile 0,85 bulunur. Tablodan 0,85 z-score değerinin yüzdesi %30 olarak bulunur. %30 + %50 = %80 bulunur.

![image](./images/ampul2.png)

```Python
from scipy.stats import norm

olasılık = norm(loc=800, scale=40).cdf(834)
olasılık

# 0.8023374568773076
```

3. 778 saat ile 834 saat arasında bir ömre sahip olması olasılığını bulunuz?

778'in z-score değeri (778-800) / 40 ile -0,55 bulunur.
834'ın z-score değeri (45-60) / 12 ile 0,85 bulunur.

Tabloda karşılıkları toplanarak %21 + %30 = %51 bulunur.

![image](./images/ampul3.png)

```Python
from scipy.stats import norm

ust = norm(loc=800, scale=40).cdf(834) # 1600 1, 800 0
alt = norm(loc=800, scale=40).cdf(778)
olasılık = ust - alt
olasılık

# 0.5111777700889613
```

-----

Bir imalathanede üretilen millerin çaplarının ortalaması 3.0005 inç ve standart sapmalarının ise 0.001 inç olan normal dağılıma uyduğu tespit edilmiştir. Üretilen miller eğer 3.000 +- 0,002 inç aralığının dışında iseler bu miller hatalı Üretim kabul edilmektedir. Buna göre toplam Üretimdeki hatalı ürün miktarım bulunuz?

Ortalama = 3.0005

Sapma = 0.001

z-score1 = (3,002 - 3,0005) / 0.001 = 0.0015 / 0.001 = 1,5

z-score2 = (2,998 - 3,0005) / 0.001 = -0.0025 / 0.001 = -2,5

Doğru ürün oranı %49,38 + %43,32 = % 92,7

Hatalı ürün oranı 100 - 92,7 = %7,3

![image](./images/uretim.png)

```Python
from scipy.stats import norm

ust = norm(loc=3.0005, scale=0.001).cdf(3.002)
alt = norm(loc=3.0005, scale=0.001).cdf(2.998)

olasılık = ust - alt
olasılık

# 0.9269831334053148
```

%92,69 ürünler doğru olduğuna göre hatalı ürün yüzdesi %7,31'dir.

## 2.6 Serbestlik derecesi

toplamları 15 olan 3 sayı seçin denildiğinde 2 sayı seçebiliriz ve 3. sayı otamatik olarak belli olur. Benzer şekilde ortalama alırken popülasyonu da örneklemi de n'e bölerken, standart sapma alırken popülasyonu n'e bölerken örneklemi n-1'e bölmemizin sebebi bütün değerlerden ortalamayı çıkarıptoplarsak sonuç hep sıfırdır. (Bu aynı zamanda neden karelerini alıp karekökünü aldığımızın da cevabıdır.) Bu yüzden son rakamı hep tahmin edebiliriz. Bunun için örneklem standart sapmasında n-1 ile formulü oluştururuz.

## 2.7 Student'in t dağılımı

Örneklem sayısı 30'un altında kaldığında t dağılım yapısı kullanılır. T dağılımında serbestlik derecesi yükseldikçe normal dağılıma yakınsar.

```Python
import numpy as np
from scipy import stats
import matplotlib.pyplot as plt
import seaborn as sns

np.random.seed(0)

veri1t=stats.t.rvs(loc=0,df=1,size=15)
veri2t=stats.t.rvs(loc=0,df=2,size=15)
veri5t=stats.t.rvs(loc=0,df=5,size=15)
veri20t=stats.t.rvs(loc=0,df=20,size=15)

sns.distplot(veri1t,color="red",hist=False)
sns.distplot(veri2t,color="blue",hist=False)
sns.distplot(veri5t,color="green",hist=False)
sns.distplot(veri20t,color="black",hist=False)
plt.xlim(-5,5)
plt.show()

sns.displot(veri1t,color="green",kde=True)
plt.xlim(-5,5)
plt.show()
```

![image](./images/tdagilim.png)

## 2.8 Tahmin teorisi

- Nokta tahmin: sonucu direk bir sayıyla tahmin etmek.
- Aralık tahmini: sonucu bir aralık olarak tahmin etmek. İki türü vardır:
  - Güven aralığı
  - Bayes tipi güvenilir aralık
 
Aralık Tahmini
- Tek popülasyon
  - Ortalama tahmini için;
    - Popülasyon standart sapması biliniyorsa Z tablosu
    - Popülasyon standart sapması bilinmiyorsa ve n>30 ise Z tablosu
    - Popülasyon standart sapması bilinmiyorsa ve n<=30 ise T tablosu
  - Standart Sapma tahmini için;
    - Ki Kare Tablosu
  - Oran tahmini için;
    - Z tablo
- İki popülasyon
  - Ortalama farkı tahmini için;
    - Popülasyon standart sapması biliniyorsa Z tablosu
    - Popülasyon standart sapması bilinmiyorsa varyans testleri

## 2.9 Normal dağılım ortalama güven aralığı

$$
P\left( \bar{X} - Z_{score} \cdot \frac{\theta}{\sqrt{n}} < \mu < \bar{X} + Z_{score} \cdot \frac{\theta}{\sqrt{n}} \right) = 1 - \alpha
$$

Açıklamalar:

* $$\bar{X}$$: Örneklem ortalaması
* $$\mu$$: Popülasyon ortalaması (bilinmeyen parametre)
* $$\theta$$: Anakütle standart sapması
* $$n$$: Örneklem büyüklüğü
* $$\alpha$$: Anlamlılık düzeyi (örneğin $$\alpha = 0.05$$)
* $$Z_{score}$$: Standart normal dağılımdan yüzdelik değeri (örneğin %95 güven düzeyi için 1.96)

Genelde kullanılan 3 çeşit güven aralığı vardır;
- %99
- %95
- %90

⚠⚠⚠ En çok kullanılan %95'dir.

z-score değerleri;

- %99 için 2,58
- %95 için 1,96
- %90 için 1,65

### Örnekler 

Rassal olarak seçilen fabrikadaki 100 ürünün (n 30 dan büyükse z tablosu kullanılır) ortalama ağırlık 1040 gr, standart sapması ise 25 gr'dır. Fabrikadaki tüm ürünlerin (popülasyon) ortalama ağırlıkları %95 güven aralığında kaçtır?

$$1040 - 1.96 \cdot \frac{25}{\sqrt{100}} < \mu < 1040 + 1.96 \cdot \frac{25}{\sqrt{100}}$$

$$1040 - 1.96 \cdot 2.5 < \mu < 1040 + 1.96 \cdot 2.5$$

$$1040 - 4.9 < \mu < 1040 + 4.9$$

$$1035.1 < \mu < 1044.9$$

```Python
import numpy as np
from scipy import stats

n=100
xort=1040
xstandart=25
guven=0.95

aralık=stats.norm.interval(confidence=guven, loc=xort, scale=xstandart/np.sqrt(n))
aralık

# (1035.1000900386498, 1044.8999099613502)
```

---

85 ev sahibi ile yapılan bir ankette, ev bakımına aylık olarak ortalama 67$$ (standart sapma = 14$$) harcadıkları tespit edilmiştir. Tum ev sahiplerinin aylık ev bakım harcamaları için %95 güven aralığını oluşturunuz.

```Python
import numpy as np
from scipy import stats

n=85
xort=67
xstandart=14
guven=0.95

aralik = stats.norm.interval(confidence=guven, loc=xort, scale=xstandart/np.sqrt(n))
aralik

# (64.02376880867953, 69.97623119132047)
```

---

Piyasaya yeni sürülen bir ürünün uzunluğunun standart sapması 2cm'dir. Rastgele seçilen 16 ürünün ortalama uzunluğu 4 cm olarak hesaplanmıştır.%95 güvenle anakütle ortalamasını tahmin ediniz?

```Python
import numpy as np
from scipy import stats

n=16
xort=4
xstandart=2 
guven=0.95

# n 30'dan küçük ve normalde T tablosu kullanmak gerekir. 
# Ama popülasyonun sapması (fabrika çıkışlı bütün ürünlerin) bilindiği için Z tablosu kullanılır.

aralik = stats.norm.interval(loc=xort, confidence=guven, scale=xstandart/np.sqrt(n))
aralik

# (3.020018007729973, 4.979981992270027)
```
---

Bir fabrikada üretilen margarin paketlerinin ağırlığının varyansı 100 gr'dır. Rastgele seçilen 25 paketin ağırlığının ortalaması 120 gr'dır. Ana kütle ortalamasını %90 ve %99 guvenle tahmin ediniz.

```Python
import numpy as np
from scipy import stats

n=25
xort=120
xstandart=np.sqrt(100) # varyans standart sapmanın karesidir. 
guven1=0.90
guven2=0.99

# n 30'dan küçük ve normalde T tablosu kullanmak gerekir. 
# Ama popülasyonun varyansı (fabrika çıkışlı bütün ürünlerin) bilindiği için Z tablosu kullanılır.

aralik1 = stats.norm.interval(loc=xort, confidence=guven1, scale=xstandart/np.sqrt(n))
print(aralik1)
aralik2 = stats.norm.interval(loc=xort, confidence=guven2, scale=xstandart/np.sqrt(n))
print(aralik2)

# (116.71029274609705, 123.28970725390295)
# (114.8483413929022, 125.1516586070978)
```
---

Bir firmanın ürettiği ürünlerin 100 tanesi rassal örneklem olarak seçilmiştir. Ortalama ağırlık 385gr ve standart sapması 12gr olarak hesaplanmıştır. Üretilen ürünlerin ortalama ağırlığını %95 güven düzeyi ile belirleyiniz?

```Python
import numpy as np
from scipy import stats

n=100
xort=385
xstandart=12 
guven=0.95

aralik = stats.norm.interval(loc=xort, confidence=guven, scale=xstandart/np.sqrt(n))
aralik

# (382.64804321855195, 387.35195678144805)
```

## 2.10 t dağılım ortalama güven aralığı

![image](./images/ttablosu.png)

n = 30 

ortalama =140 

standart sapma = 25 

güven aralığı = %95 

serbestlik derecesi = 29 ve standart sapması bilinmiyor. (n 30 a eşit veya daha küçükse ve popülasyon sapması bilinmiyorsa t tablosu kullanılır.)

Öncelikle serbestlik derecesiyle güven aralığının 1 değerinden çıkarılmasıyla elde edilen 0,05 alfa değeri kullanılarak tablodan t değeri 2,045 bulunur.

Xort - t * (sapma / sqrt(n)) < M < Xort + t * (sapma / sqrt(n)) 

140 - 2,045 * (25 / sqrt(30)) < M < 140 + 2,045 * (25 / sqrt(30))

130,666 < M < 149,334

```Python
import numpy as np
from scipy import stats

n=30
xort=140
xssapma=25
guven=0.95
sderecesi = n-1

aralik = stats.t.interval(confidence=guven, loc=xort, df=sderecesi, scale=xssapma/np.sqrt(n))
aralik

# (130.6648465810475, 149.3351534189525)
```
---

Rassal olarak seçilen 20 bilgisayarın tamirat maliyetleri kaydedilmiştir. Ortalama 216,53$$ örneklemin standart sapması 15.86$$. Tüm bilgisayarların ortalama tamirat maliyetlerini %95 güven ile tahmin ediniz diyor

```Python
import numpy as np
from scipy import stats

n=20
xort=216.53
xssapma=15.86
guven=0.95
sderecesi = n-1

aralik = stats.t.interval(confidence=guven, loc=xort, df=sderecesi, scale=xssapma/np.sqrt(n))
aralik

(209.10729151418025, 223.95270848581976)
```

## 2.11 İki popülasyon ortalama farkı güven aralığı

Bir fabrikada A ve B ürünlerinin ağırlıklarının varyansları sırasıyla 164gr ve 216gr. A ürününden 28 adet, B ürününden 30 adet örneklem alındığında A ürününün ortalama ağırlığı 32gr B ürününün ortalama ağırlığı 26gr çıkmıştır. Bu verilere göre A ve B ürünlerinin ortalama ağırhklarının farkını %95 güven aralığında bulunuz?

```Python
# Ürünlerin (popülasyonun) varyansı verilmiş. Z tablosu kullanılacak.
import numpy as np
from scipy import stats

na=28
nb=30

vara=164
varb=216

orta=32
ortb=26

guven=0.95

aralik=stats.norm.interval(confidence=guven,loc=(orta-ortb),scale=np.sqrt((vara/na)+(varb/nb)))
aralik

# (-1.0822649344425628, 13.082264934442563)
```
---

İngilizce ve Fransızca eğitim alan iki öğrenci grubundan sırasıyla 30 ve 40 öğrenci seçiliyor. İngilizce grubu öğrencilerinin dil öğrenme süre ortalaması 182 gün, Fransızca grubu için 176 gün olarak hesaplanıyor. Aynı gruplar için varyanslar sırasıyla 196 gün ve 144 gün olarak hesaplanıyor. Bu iki farklı dil kursuna giden ekiplerin öğrenme süreleri arasındaki fark %95 güven ile kaç gündür?

```Python
# Öğrenci sayısı 30'a eşit veya 30'dan büyük olduğu için z tablosu kullanılacak.
import numpy as np
from scipy import stats

na=30
nb=40

vara=196
varb=144

orta=182
ortb=176

guven=0.95

aralik=stats.norm.interval(confidence=guven,loc=(orta-ortb),scale=np.sqrt((vara/na)+(varb/nb)))
aralik

# (-0.2391331702703008, 12.2391331702703)
```
---

2 farklı hasta grubu arasında 8 ve 10 bireylerden oluşan örneklemler çekilmiştir. Bu iki grubun bir virüse karşı reaksiyon verme zaman ortalamaları sırasıyla 3 ve 2.7'dir. Birleştirilmiş varyans 0.05 olarak hesaplandığına göre bu iki farklı hasta grubunun virüse karşı verdiği reaksiyon zaman farklarını %95 güven ile bulunuz?

Birleştirilmiş varyans formülü;

![image](./images/birvar.png)

```Python
import numpy as np
from scipy import stats

na=8
nb=10

birvar = 0.05

orta=3
ortb=2.7

guven=0.95

n = (1/na) + (1/nb)

aralik=stats.t.interval(confidence=guven, df=(na+nb-2), loc=(orta-ortb), scale=np.sqrt(n*birvar))
aralik

# (0.07515008811712867, 0.524849911882871)
```

## 2.12 Rassal değişken nedir?

Olasılık: bir şeyin olmasının veya olmamasının matematiksel değeridir.

Olasılık dağılımı: bütün potansiyel sonuçları x eksenine yerleştirip olasılık değerlerini y eksenine yerleştiren bir dağılımdır.

Bir zarın olasılık dağılımı (uniform);

![image](./images/zaruniform.png)

## 2.13 Beklenen değer nedir?

Kesikli veri için;

![image](./images/kesikliformul.png)

Sürekli veri için

![image](./images/surekliformul.png)

hilesiz bir zar bir defa havaya atıldığını varsayalım. Zar üzerinden gelecek değerin beklenen değeri kaçtır?

![image](./images/xcarpipx.png)

E(X) = 1/6 + 2/6 + 3/6 + 4/6 + 5/6 + 6/6 = 21/6 = 3,5

```Python
from scipy import stats

x=[1,2,3,4,5,6]

p=[1/6,1/6,1/6,1/6,1/6,1/6]

beklenendeg=stats.rv_discrete(values=([x],[p])).expect()
beklenendeg

# 3.5
```

---

Hilesiz bir madeni parayı üç kez havaya attığımızı varsayıyoruz. Bu üç atışta yazı gelme olasılığının beklenen değerini hesaplayalım.


![image](./images/yaziolasilik.png)

![image](./images/yazideger.png)

E(X) = 0 + 3/8 + 6/8 + 3/8 = 1,5

```Python
from scipy import stats

x=[0,1,2,3]

p=[1/8,3/8,3/8,1/8]

beklenendeg=stats.rv_discrete(values=([x],[p])).expect()
beklenendeg

# 1.5
```

---

Sürekli veri için;

$$F(x) = 3/7 \cdot X^2$$
$$E(x) = \int_1^2 x \cdot F(x) \,dx$$
$$E(x) = \int_1^2 x \cdot \frac37  \cdot  x^2 \,dx$$
$$E(x) = \frac37  \cdot \int_1^2 x \cdot  x^2 \,dx$$
$$E(x) = \frac37  \cdot \int_1^2 x^3 \,dx$$
$$E(x) = \frac37  \cdot \int_1^2 \frac{x^4}4$$
$$E(x) = \frac37  \cdot \int_1^2 \frac{x^4}4$$
$$E(x) = \frac37  \cdot (\frac{2^4}4 - \frac{1^4}4)$$
$$E(x) = \frac37  \cdot \frac{15}4$$
$$E(x) = \frac{45}{28}$$
$$E(x) = 1,607$$

```Python
from scipy import stats
import scipy.integrate

def f(x):
    return (3/7)*x**3 # x * 3/7 x**2

beklenendeg=scipy.integrate.quad (f,1,2)
beklenendeg[0]

# 1.6071428571428572
```
---

| İkramiye Adeti | İkramiye Miktarı (TL) |
|---------------:|----------------------:|
|            160 |               200000  |
|           1761 |                10000  |
|          17245 |                 1000  |
|          54061 |                  200  |
|         740386 |                  100  |
|        2768685 |                   50  |
|       11557684 |                   20  |
|       15933969 |                   10  |
|       19586461 |                    5  |

İkramiye Olan Bilet Sayısı: 50.660.412

Toplam Bilet Sayısı: 240.937.000

İkramiye Olmayan Bilet Sayısı: 190.276.588

Bilet Fiyatı: 5 TL

![image](./images/ikramiye.png)

Sürekli bu oyunu oynarsak kazancımız -1,76 TL'dir.

## 2.14 Büyük sayılar yasası

![image](./images/para1.png)
![image](./images/para2.png)
![image](./images/para3.png)

100 atış yazılsa beklenen değer 50 gelecektir.

$$E(x) = n \cdot P(x)$$
$$E(x) = 100 \cdot 0.5$$
$$E(x) = 50$$

Yukarıdaki gibi atış yapmaya devam edersek beklenen değerlerin ortalaması %50'ye (beklenen değere yani olasılığa) yaklaşacaktır.

```Python
import numpy as np
import matplotlib.pyplot as plt
import seaborn as sns

liste=[]

for i in np.arange(0,5):
  denemesayisi=i+1
  yt=np.random.randint(0,2,size=denemesayisi)
  yolasilik=np.mean(yt)
  liste.append(denemesayisi)
  print("Deneme Sayısı: {}--------Ortalama: {}".format(denemesayisi,yolasilik))

Deneme Sayısı: 1--------Ortalama: 0.0
Deneme Sayısı: 2--------Ortalama: 0.5
Deneme Sayısı: 3--------Ortalama: 0.6666666666666666
Deneme Sayısı: 4--------Ortalama: 0.5
Deneme Sayısı: 5--------Ortalama: 0.8
```

```Python
import numpy as np
import matplotlib.pyplot as plt
import seaborn as sns

liste=[]

for i in np.arange(0,101):
    denemesayisi=i+1
    yt=np.random.randint(0,2,size=denemesayisi)
    yolasilik=np.mean(yt)
    liste.append(yolasilik)

sns.lineplot(data=liste,linewidth=2)
plt.xlabel("Deneme Sayısı")
plt.ylabel("Ortalama")
plt.ylim(0,1)
plt.axhline(0.5,linestyle="--",linewidth=1.5,color="red")
plt.show()
```

![image](./images/deneme5.png)

```Python
import numpy as np
import matplotlib.pyplot as plt
import seaborn as sns

liste=[]

for i in np.arange(0,101):
    denemesayisi=i**3
    yt=np.random.randint(0,2,size=denemesayisi)
    yolasilik=np.mean(yt)
    liste.append(yolasilik)

sns.lineplot(data=liste,linewidth=2)
plt.xlabel("Deneme Sayısı")
plt.ylabel("Ortalama")
plt.ylim(0,1)
plt.axhline(0.5,linestyle="--",linewidth=1.5,color="red")
plt.show()
```

![image](./images/deneme100.png)

## 2.15 Olasılık dağılımı nedir?

Kesikli değişkenlerde olasılık dağılımı

![image](./images/olasdag.png)

![image](./images/dagilimhistogrami.png)

# 3 Fonksiyonlar

## 3.1 Olasılık kütle ve yoğunluk fonksiyonları

![image](./images/dagilimhistogrami.png)

Yukardaki yapıya olasılık kütle fonksiyonu **(PMF)** diyoruz. Kesikli Olasılık dağılımı gösteren bir örnektir.

![image](./images/hist30.png)

$$
E(x) = \int_{-\infty}^{\infty} x \cdot F(x) \, dx
$$

Sürekli değişkenlerde ise bu şekilde olasılık değeri yoktur. İntegral alan hesabı ile olasılık değeri bulunur. Grafikteki sol eksen asla sürekli değişkenlerde olasılık değeri vermez, frekansı verir. $-\infty$ ile $\infty$ arası olasılık değeri hesaplanırsa bütün olasılıkları kapsadığı için sonuç 1 olur.

Sürekli değişken yapısında bu yapıya Olasılık yoğunluk fonksiyonu **(PDF)** denir.

## 3.2 Birikimli dağılım fonksiyonu

Bir hastanede belirli bir hastalıkla ilgili bulunan hastaların tansiyonlarının ortalaması 15, varyansları ise 9 olduğu biliniyor ve bu yapının normal dağılıma sahip olduğu biliniyor. Bu hastalar içerisinden rastgele seçilen bir hastanın tansiyonunun 11'den küçük olma olasılığı nedir?

![image](./images/birikim.png)

$$ z = \frac{11 - 15}{\sqrt{9}} $$
$$ z = \frac{-4}{3} $$
$$ z = -1,333 $$
$$ P(x) = \\% 9.2 $$

```Python
from scipy import stats

olasılık=stats.norm.pdf(x=11, loc=15, scale=3)
olasılık

# 0.05467002489199788
```
Bu değer olasılığı vermez. Normal dağılım sürekli olduğundan 11 değerinin F(x) karşılığını verir. Bu yüzden sürekli yapılarda pdf fonksiyonunun cdf fonksiyonuyla integralini almamız gerekir.

```Python
from scipy import stats

olasılık=stats.norm.cdf(x=11, loc=15, scale=3)
olasılık

# 0.09121121972586788
```

⚠️⚠️⚠️F(x) kümülatif (birikimli) fonksiyon **(CDF)**, f(x) **(PMF)** gösteriminde kullanılır.


```Python
from scipy import stats
import matplotlib.pyplot as plt
import seaborn as sns

x=[0,1,2,3]
p=[1/8,3/8,3/8,1/8]

plt.bar(x,p)
plt.show()
```

![image](./images/dagilimhistogrami.png)

Bu dağılımı kümülatif göstermek için;

```Python
from scipy import stats
import matplotlib.pyplot as plt
import seaborn as sns

x=[0,1,2,3]
p=[1/8,3/8,3/8,1/8]

cum=[]

for i in range(0,len(p)):
    if len(cum) == 0:
        cum.append(p[0])
    else:
        cum.append(p[i]+cum[i-1])

cum

# [0.125, 0.5, 0.875, 1.0]
```

```Python
from scipy import stats
import matplotlib.pyplot as plt
import seaborn as sns

x=[0,1,2,3]
p=[1/8,3/8,3/8,1/8]

cum=[]

for i in range(0,len(p)):
    if len(cum) == 0:
        cum.append(p[0])
    else:
        cum.append(p[i]+cum[i-1])

plt.plot(x,cum,drawstyle="steps")
plt.show()
```
![image](./images/kumulatifgrafik.png)

```Python
from scipy import stats
import matplotlib.pyplot as plt
import seaborn as sns
import numpy as np

x=np.random.randn(10000)
pdf=stats.norm.pdf(x)
cdf=stats.norm.cdf(x)
sns.lineplot(x=x,y=pdf)
sns.lineplot(x=x,y=cdf)
plt.show()
```
![image](./images/cdfpdfgrafik.png)

## 3.3 Bernoulli dağılımı

![image](./images/dagilimlar.png)

Bernoulli dağılımı bir deney sonucunda başarılı ve başarısız olmak üzere bize sadece iki olası sonuç veren yapılardır.

Mesela, bir madeni parayı bir defa havaya attığımızda iki olası sonuç vardır. Ya yazı gelir yada tura gelir.

Bernoulli dağılımı kesikli bir dağılım olduğu için olasılık kütle fonksiyonudur. **(PMF)** 

$$ F(x) = P^x \cdot (1-P)^{1-x} $$
$$ P(x = 0) = 1 - P $$
$$ P(x = 1) = P $$
$$ E(x) = P $$
$$ \sigma^2_x = P \cdot (1 - P) $$

Parayı atınca yazı gelme ihtimali;
$$ Y = 1/2 \rightarrow 1/2 = P \rightarrow x = 1 $$
$$ T = 1/2 \rightarrow 1/2 = 1-P \rightarrow x = 0 $$

$$ F(x) = P^x \cdot (1-P)^{1-x} $$
$$ Y \rightarrow (1/2)^1 \cdot (1/2)^0 = 0,5 $$
$$ T \rightarrow (1/2)^0 \cdot (1/2)^1 = 0,5 $$
$$ E(X) = P = 0,5 $$
$$ \sigma^2_x = P \cdot (1 - P) = 0,5 \cdot 0,5 = 0,25 $$

---

Bir iskambil setinde papaz gelme olasılığı;

Kart sayısı: 52
Papaz sayısı: 4

P = 4 / 52

$ x = 1 $ papaz gelme ihtimalini gösterir.

$$ x = 0 \rightarrow (\frac4{52})^0 \cdot (\frac{48}{52})^1 = \frac{48}{52} $$
$$ x = 1 \rightarrow (\frac{48}{52})^0 \cdot (\frac{4}{52})^1 = \frac{4}{52} $$

![image](./images/bergrafik.png)



```Python

```

![image](./images/.png)

### 
## 3.3
# 4

https://www.youtube.com/watch?v=N9MCfKdjKik&list=PLK8LlaNiWQOvAYUMGMTFeZIOo0oKmZhdw&index=42&ab_channel=Anla%C5%9F%C4%B1l%C4%B1rEkonomi
0:00
