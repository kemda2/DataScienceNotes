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

### Basıklık (Kurtosis)

![image](https://github.com/user-attachments/assets/195b8d56-421f-4e3a-8a83-27200049ab55)

![image](https://github.com/user-attachments/assets/03d808fd-7c0a-47d4-b55d-dde946db9605)

![image](https://github.com/user-attachments/assets/17e25227-7fba-4404-8b79-f907ae49a329)

Kurtosis değerinin sınırı 3 olarak kabul edilir.

[Normal dağılım hesaplamak için site](https://www.desmos.com/calculator/jxzs8fz9qr?lang=tr)

Excelde basıklık fonksiyonuyla kullanılır. 3'ten büyükse sivri bir dağılım küçükse basık bir dağılım var deriz.

## 1.3 Ek Bilgi

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

![image](https://github.com/user-attachments/assets/676c0d2d-0f8d-46d3-8067-77f3afec3887)

```Python
veri["Cinsiyet"].describe()
```

![image](https://github.com/user-attachments/assets/40cd920b-bf17-42a6-b576-a012c92bc277)

```Python
veri["Cinsiyet"].value_counts()
```

![image](https://github.com/user-attachments/assets/9b1a3b39-9dfd-42af-b696-a8cc613e2a7e)

```Python
veri["Cinsiyet"].value_counts(normalize = True) *100
```

![image](https://github.com/user-attachments/assets/881d4e1c-2fb7-4d75-8e9d-81dfe8033a11)

```Python
plt.hist(veri["Yaş"], bins = 50)
plt.show()
```

![image](https://github.com/user-attachments/assets/54c931db-65c2-46b8-923f-83e727fe2e3f)

```Python
veri["Yaş"].skew()
```

![image](https://github.com/user-attachments/assets/49ff66fc-b74c-4680-93b1-6df7a4d97b32)

```Python
veri["Yaş"].kurtosis()
```

![image](https://github.com/user-attachments/assets/214696d0-5e87-42bc-aac2-569a24be6e81)

```Python
veri.groupby("Cinsiyet").mean()
```

![image](https://github.com/user-attachments/assets/1167e8f9-9fca-42f7-9e09-7e6ad4776e02)

# 2 Çıkarımsal İstatistik

Örnekleme yapılarak popülasyon için çıkarım yapılan istatistik türüdür. 

## 2.1 Merkezi Limit Teoremi

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
```

![image](https://github.com/user-attachments/assets/f67d5298-5ef3-40b8-a391-4c8debd69049)

```Python
yas.mean()
```

![image](https://github.com/user-attachments/assets/67dc9f30-0ee4-4252-89f0-e9e79f88710c)

```Python
plt.hist(yas)
plt.show()
```

![image](https://github.com/user-attachments/assets/0008b5db-f202-4d3a-ac9a-9bc5bc8ef5ea)

```Python
import random
orneklem = random.choices(yas,k=5)
orneklem
```

![image](https://github.com/user-attachments/assets/f4a63e32-def3-4f8e-a13c-3892e6876e08)

```Python
import random
orneklem = [np.mean(random.choices(yas, k=1)) for _ in range(1000)] # 1000 kez örneklem çekiyor. 
plt.hist(orneklem)
plt.show()
```

![image](https://github.com/user-attachments/assets/b714f0ef-dc3b-4ca5-a174-3695a7a5084c)

```Python
import random
orneklem = [np.mean(random.choices(yas, k=2)) for _ in range(1000)] # 1000 kez örneklem çekiyor. 
plt.hist(orneklem)
plt.show()
```

![image](https://github.com/user-attachments/assets/363c9e5e-e7ab-4fbe-960a-bc849e2a3941)

```Python
import random
orneklem = [np.mean(random.choices(yas, k=10)) for _ in range(1000)] # 1000 kez örneklem çekiyor. 
plt.hist(orneklem)
plt.show()
```

![image](https://github.com/user-attachments/assets/116b0f09-f5d2-480b-82c2-c1cfccbb6733)

```Python
import random
orneklem = [np.mean(random.choices(yas, k=30)) for _ in range(1000)] # 1000 kez örneklem çekiyor. 
plt.hist(orneklem)
plt.show()
```

![image](https://github.com/user-attachments/assets/dbb90183-0124-417f-90c9-ca9bb5c1fc30)

## 2.2 Standart Hata Nedir?

![image](https://github.com/user-attachments/assets/314cba26-d8a9-4952-9003-6faf2a24c5b6)


Çekilen örneklemlerin sayısı arttıkça örneklem kümesinin ortalaması popülasyon ortalamasına yakınsar. Örneklem çektikçe örneklem kümesinin ortalaması ile popülasyon ortalamasına yaklaşır. Bir popülasyondan seçilebilecek olası örneklemlerin ortalamalarınının standart sapmasına standart hata denir.

Düşük standart sapma ve düşük standart hata isteniyor.

[Dağılım oluşturma sitesi](https://onlinestatbook.com/stat_sim/sampling_dist/index.html)

## 2.3 Seaborn Kütüphanesi

```Python
import seaborn as sns
import matplotlib.pyplot as plt
import numpy as np

x = np.random.normal(35,1,10000) #ortalama, standart sapma, adet

sns.histplot(x)
plt.show()
```

![image](https://github.com/user-attachments/assets/670b0509-10aa-487e-8b26-123348c6817b)



```Python

```

### 
## 
# 
