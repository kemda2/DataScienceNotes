# Dinleme Sayısını Binleme

![](.\i\001.png)

```py
import pandas as pd
listen_count = pd.read_csv('millionsong/train_triplets.txt.zip', header=None, delimiter='\t')
# The table contains user-song-count triplets. Only nonzero counts are
# included. Hence, to binarize the count, we just need to set the entire
# count column to 1.
listen_count[2] = 1
```

# Yelp Veri Kümesindeki İşletme Yorum Sayılarının Görselleştirilmesi
```py
import pandas as pd
import json

# Load the data about businesses
biz_file = open('yelp_academic_dataset_business.json')
biz_df = pd.DataFrame([json.loads(x) for x in biz_file.readlines()])
biz_file.close()

import matplotlib.pyplot as plt
import seaborn as sns

# Plot the histogram of the review counts
sns.set_style('whitegrid')
fig, ax = plt.subplots()
biz_df['review_count'].hist(ax=ax, bins=100)
ax.set_yscale('log')
ax.tick_params(labelsize=14)
ax.set_xlabel('Review Count', fontsize=14)
ax.set_ylabel('Occurrence', fontsize=14)
```


![](.\i\002.png)

# Sabit Genişlikte Binleme ve Üssel Genişlikte Binleme

Sabit genişlikte binleme ile her bin, belirli bir sayısal aralığı içerir. Bu aralıklar özel olarak tasarlanabilir veya otomatik olarak segmentlere ayrılabilir ve doğrusal ya da üssel ölçekte olabilir. Örneğin, kişileri on yıllık yaş dilimlerine göre gruplandırabiliriz: 0–9 yaş bin 1'de, 10–19 yaş bin 2'de, vb. Sayımdan bine geçmek için, sadece binin genişliğine böleriz ve tam sayı kısmını alırız.

Ayrıca, hayatın evrelerine daha iyi karşılık gelen özel yaş aralıklarını görmek yaygın bir durumdur, örneğin:

* 0–12 yaş
* 12–17 yaş
* 18–24 yaş
* 25–34 yaş
* 35–44 yaş
* 45–54 yaş
* 55–64 yaş
* 65–74 yaş
* 75 yaş ve üzeri

Sayılarda birden fazla büyüklük sırası olduğunda, sayıları 10'un katları (veya herhangi bir sabit sayının katları) ile gruplamak daha iyi olabilir: 0–9, 10–99, 100–999, 1000–9999, vb. Bin genişlikleri üssel olarak büyür, O(10)'dan O(100), O(1000)'e ve daha fazlasına geçer. Sayımdan bine geçmek için, sayımın logaritmasını alırız. **Üssel genişlikte binleme**, "Logaritma Dönüşümü" başlığı altında **sayfa 15**'te tartıştığımız log dönüşümü ile çok yakından ilişkilidir. **Örnek 2-3**, bu binleme yöntemlerinden birkaçını göstermektedir.

**Sabit Genişlikte Binlerle Sayımları Kuantize Etme**

```python
import numpy as np

# 0 ile 99 arasında rastgele 20 tam sayı üret
small_counts = np.random.randint(0, 100, 20)
small_counts
# array([30, 64, 49, 26, 69, 23, 56, 7, 69, 67, 87, 14, 67, 33, 88, 77, 75, 47, 44, 93])

# 0-9 arası eşit aralıklı binlere bölme
np.floor_divide(small_counts, 10)
# array([3, 6, 4, 2, 6, 2, 5, 0, 6, 6, 8, 1, 6, 3, 8, 7, 7, 4, 4, 9], dtype=int32)

# Birkaç büyüklük sırasını kapsayan bir dizi sayım
large_counts = [296, 8286, 64011, 80, 3, 725, 867, 2215, 7689, 11495, 91897, 44, 28, 7971, 926, 122, 22222]

# Log fonksiyonu ile üssel genişlikte binlere bölme
np.floor(np.log10(large_counts))
# array([ 2., 3., 4., 1., 0., 2., 2., 3., 3., 4., 4., 1., 1., 3., 2., 2., 4.])
```

# Yelp İşletme Yorum Sayılarının Ondalıklarını Hesaplama ve Görselleştirme


```python
deciles = biz_df['review_count'].quantile([.1, .2, .3, .4, .5, .6, .7, .8, .9])
deciles

# 0.1 3.0
# 0.2 4.0
# 0.3 5.0
# 0.4 6.0
# 0.5 8.0
# 0.6 12.0
# 0.7 17.0
# 0.8 28.0
# 0.9 58.0
# Name: review_count, dtype: float64

sns.set_style('whitegrid')
fig, ax = plt.subplots()
biz_df['review_count'].hist(ax=ax, bins=100)
for pos in deciles:
    handle = plt.axvline(pos, color='r')
ax.legend([handle], ['deciles'], fontsize=14)
ax.set_yscale('log')
ax.set_xscale('log')
ax.tick_params(labelsize=14)
ax.set_xlabel('Yorum Sayısı', fontsize=14)
ax.set_ylabel('Frekans', fontsize=14)
```

![](.\i\003.png)


# Sayımları Kuantilere Göre Binlemek

```py
import pandas as pd

# Sayımları çeyreklere (quartiles) ayıralım
pd.qcut(large_counts, 4, labels=False)
# array([1, 2, 3, 0, 0, 1, 1, 2, 2, 3, 3, 0, 0, 2, 1, 0, 3], dtype=int64)

# Kuantilleri hesaplayalım
large_counts_series = pd.Series(large_counts)
large_counts_series.quantile([0.25, 0.5, 0.75])

# 0.25    122.0
# 0.50    926.0
# 0.75    8286.0
# dtype: float64
```

# Logaritma Dönüşümü

Önceki bölümde, veriyi üssel genişlikte binlere yerleştirmek için sayımın logaritmasını almanın kavramını kısaca tanıttık. Şimdi buna daha yakından bakalım.

Logaritma fonksiyonu, üssel fonksiyonun tersidir. Şu şekilde tanımlanır:
**loga(ax) = x**, burada **a** pozitif bir sabittir ve **x** pozitif herhangi bir sayı olabilir. Çünkü **a⁰ = 1**, bu durumda **loga(1) = 0** olur. Bu, logaritma fonksiyonunun, (0, 1) arasındaki küçük sayı aralığını tüm negatif sayılar aralığına (–∞, 0) eşlediği anlamına gelir. **log₁₀(x)** fonksiyonu, [1, 10] aralığını [0, 1] aralığına, [10, 100] aralığını [1, 2] aralığına ve benzer şekilde daha büyük sayıları sırasıyla eşler. Başka bir deyişle, logaritma fonksiyonu büyük sayıların aralığını sıkıştırır ve küçük sayıların aralığını genişletir. **x** ne kadar büyükse, **log(x)** daha yavaş artar.

Bunu, logaritma fonksiyonunun bir grafiğine bakarak daha kolay anlayabiliriz (Şekil 2-6'ya bakınız). **100** ile **1.000** arasındaki yatay **x** değerlerinin, dikey **y** aralığında yalnızca **2.0** ile **3.0** arasına sıkıştırıldığını ve **100**'den küçük olan **x** değerlerinin yatayda çok küçük bir kısmının, dikey aralığın geri kalanına eşlendiğini gözlemleyebilirsiniz.

![](.\i\004.png)

```py
fig, (ax1, ax2) = plt.subplots(2,1)
biz_df['review_count'].hist(ax=ax1, bins=100)
ax1.tick_params(labelsize=14)
ax1.set_xlabel('review_count', fontsize=14)
ax1.set_ylabel('Occurrence', fontsize=14)
biz_df['log_review_count'].hist(ax=ax2, bins=100)
ax2.tick_params(labelsize=14)
ax2.set_xlabel('log10(review_count)', fontsize=14)
ax2.set_ylabel('Occurrence', fontsize=14)
```

![](.\i\005.png)

**Mashable Haber Makalelerindeki Kelime Sayılarının Karşılaştırılması**

```py
fig, (ax1, ax2) = plt.subplots(2,1)
df['n_tokens_content'].hist(ax=ax1, bins=100)
ax1.tick_params(labelsize=14)
ax1.set_xlabel('Makaledeki Kelime Sayısı', fontsize=14)
ax1.set_ylabel('Makale Sayısı', fontsize=14)
df['log_n_tokens_content'].hist(ax=ax2, bins=100)
ax2.tick_params(labelsize=14)
ax2.set_xlabel('Kelime Sayısının Logaritması', fontsize=14)
ax2.set_ylabel('Makale Sayısı', fontsize=14)
```

![](.\i\006.png)

**Logaritma Dönüşümü Uygulanmış Yelp Yorum Sayılarını Kullanarak İşletme Ortalama Puanını Tahmin Etme**

```py
# Yelp yorum sayıları ile işletme puanını tahmin etme örneği
from sklearn.linear_model import LinearRegression
from sklearn.model_selection import cross_val_score
import numpy as np

# Yorum sayısını ve logaritma dönüşümünü kullanarak doğrusal regresyon modeli
model = LinearRegression()
X = biz_df[['review_count']]  # Yorum sayısı
X_log = np.log1p(X)  # Logaritma dönüşümü uygulanmış yorum sayısı
y = biz_df['rating']  # İşletme puanı

# 10 katlı çapraz doğrulama ile modelin doğruluğunu değerlendirme
scores = cross_val_score(model, X, y, cv=10, scoring='r2')
scores_log = cross_val_score(model, X_log, y, cv=10, scoring='r2')

# Sonuçların yazdırılması
print("R-kare skoru (ham veriler):", scores.mean())
print("R-kare skoru (log dönüşümü):", scores_log.mean())
# R-kare skoru (ham veriler): -0.03683 (+/- 0.07280)
# R-kare skoru (log dönüşümü): -0.03694 (+/- 0.07650)
```

**Online News Popularity Veri Kümesinde Logaritma Dönüşümü Uygulanmış Kelime Sayıları ile Makale Popülerliğini Tahmin Etme**

```py
# UCI'den Online News Popularity veri kümesini indirin, ardından Pandas ile
# dosyayı bir DataFrame'e yükleyin.
df = pd.read_csv('OnlineNewsPopularity.csv', delimiter=', ')

# 'n_tokens_content' özelliğinin logaritma dönüşümünü alın, bu özellik
# bir haber makalesindeki kelime (token) sayısını temsil eder.
df['log_n_tokens_content'] = np.log10(df['n_tokens_content'] + 1)

# İki doğrusal regresyon modeli eğiterek bir makalenin paylaşımlarını tahmin edin,
# biri orijinal özellik ile, diğeri ise logaritma dönüşümü uygulanmış versiyon ile.
m_orig = linear_model.LinearRegression()
scores_orig = cross_val_score(m_orig, df[['n_tokens_content']], df['shares'], cv=10)

m_log = linear_model.LinearRegression()
scores_log = cross_val_score(m_log, df[['log_n_tokens_content']], df['shares'], cv=10)

print("Log dönüşümü uygulanmadan R-kare skoru: %0.5f (+/- %0.5f)" % (scores_orig.mean(), scores_orig.std() * 2))
# Log dönüşümü uygulanmadan R-kare skoru: -0.00242 (+/- 0.00509)

print("Log dönüşümü ile R-kare skoru: %0.5f (+/- %0.5f)" % (scores_log.mean(), scores_log.std() * 2))
# Log dönüşümü ile R-kare skoru: -0.00114 (+/- 0.00418)
```

**Açıklama:**

Orijinal özellik ile model: R-kare skoru, -0.00242 ve standart sapma 0.00509. Bu model, hedef değişkenin tahmininde oldukça düşük bir başarıya sahip.
Log dönüşümü uygulanmış özellik ile model: R-kare skoru -0.00114 ve standart sapma 0.00418. Log dönüşümü ile yapılan model, daha iyi bir performans sergiliyor.

**Log Dönüşümünün Bu Veri Kümesinde Neden Daha Başarılı Olduğu?**

Log dönüşümünün neden daha başarılı olduğunu anlamak için girdi özelliği ve hedef değerleri arasındaki ilişkiye bakmamız gerekir. Aşağıdaki grafiğin alt panelinde, log dönüşümünün x eksenini yeniden şekillendirdiği ve hedef değeri çok yüksek olan makalelerin (örneğin >200.000 paylaşım) sağa doğru kayarak daha fazla "boşluk" bıraktığı görülmektedir. Bu, doğrusal modele, girdi özelliği uzayının düşük ucunda daha fazla "nefes alma alanı" sağlıyor. Log dönüşümü uygulanmadan (üst panel) model, girişteki çok küçük değişikliklerle hedef değerler arasındaki büyük farklılıkları uyarlamakta daha fazla baskı altındadır. Bu, modelin daha zorlu bir şekilde çalışmasına neden olur.

Bu durum, log dönüşümünün doğrusal regresyon modelinin veriyi daha iyi öğrenmesini sağladığını ve büyük değerlerin etkisini sınırlayarak modelin düşük değerler üzerinde daha sağlıklı bir şekilde çalışmasına yardımcı olduğunu gösteriyor.

```py
fig2, (ax1, ax2) = plt.subplots(2,1)
ax1.scatter(df['n_tokens_content'], df['shares'])
ax1.tick_params(labelsize=14)
ax1.set_xlabel('Number of Words in Article', fontsize=14)
ax1.set_ylabel('Number of Shares', fontsize=14)
ax2.scatter(df['log_n_tokens_content'], df['shares'])
ax2.tick_params(labelsize=14)
ax2.set_xlabel('Log of the Number of Words in Article', fontsize=14)
ax2.set_ylabel('Number of Shares', fontsize=14)
```

![](.\i\007.png)

**Yelp Yorumları Veri Kümesindeki Girdi ve Hedef Arasındaki Korelasyonu Görselleştirme**

Şimdi, Yelp yorumları veri kümesindeki aynı dağılım grafiğini (scatter plot) ele alalım (Örnek 2-11). Şekil 2-10, Şekil 2-9'dan oldukça farklıdır. Ortalama yıldız puanı, 1 ile 5 arasında yarım-yıldız artışlarıyla ayrılmıştır. Yüksek yorum sayıları (yaklaşık olarak 2.500'ün üzerinde) yüksek ortalama yıldız puanlarıyla ilişkilidir, ancak bu ilişki doğrusal olmaktan uzaktır. Ortalama yıldız puanını tahmin etmek için herhangi bir girdi kullanarak net bir çizgi çizmek mümkün değildir. Temelde, grafik, yorum sayısının ve onun logaritmasının, ortalama yıldız puanını tahmin etmek için kötü doğrusal tahminciler olduğunu göstermektedir.

```py
fig, (ax1, ax2) = plt.subplots(2,1)
ax1.scatter(biz_df['review_count'], biz_df['stars'])
ax1.tick_params(labelsize=14)
ax1.set_xlabel('Yorum Sayısı', fontsize=14)
ax1.set_ylabel('Ortalama Yıldız Puanı', fontsize=14)
ax2.scatter(biz_df['log_review_count'], biz_df['stars'])
ax2.tick_params(labelsize=14)
ax2.set_xlabel('Yorum Sayısının Logaritması', fontsize=14)
ax2.set_ylabel('Ortalama Yıldız Puanı', fontsize=14)
```

![](.\i\008.png)

# Box-Cox Dönüşümü

**Box-Cox Dönüşümü yalnızca veriler pozitif olduğunda çalışır. Pozitif olmayan veriler için, sabit bir konstant ekleyerek değerler kaydırılabilir.** Box-Cox dönüşümü veya daha genel bir güç dönüşümü uygularken, **λ parametresi** için bir değer belirlememiz gerekir. Bu, **maksimum olasılık** yöntemi ile yapılabilir (sonuçlanan dönüşmüş sinyalin **Gauss olasılığını maksimize eden λ**'yi bulmak) veya **Bayes yöntemleri** ile yapılabilir. Box-Cox ve genel güç dönüşümlerinin kullanımı hakkında tam bir açıklama bu kitabın kapsamı dışındadır. İlginç okurlar, güç dönüşümleri hakkında daha fazla bilgi için **Johnston ve DiNardo'nun (1997) Econometric Methods** kitabına başvurabilirler. Neyse ki, SciPy'nin **stats** paketi, **Box-Cox dönüşümünün** uygulanması ve optimal dönüşüm parametresinin bulunmasını içeren bir implementasyon sağlar. **Örnek 2-12**, bu uygulamayı Yelp yorumları veri kümesinde göstermektedir.

Farklı λ Değerleri İçin Box-Cox Dönüşümleri

![](.\i\009.png)

```py
from scipy import stats

# Önceki örnekten devam edelim, varsayalım ki biz_df, Yelp işletme yorumları verisini içeriyor.
# Box-Cox dönüşümü, girdi verisinin pozitif olduğunu varsayar.
# En düşük değeri kontrol edelim, emin olalım.
biz_df['review_count'].min()
# 3

# Girdi parametresi lmbda'yı 0 olarak ayarlamak, log dönüşümünü (sabit offset olmadan) verir.
rc_log = stats.boxcox(biz_df['review_count'], lmbda=0)
# SciPy'nin Box-Cox dönüşümü implementasyonu, çıktıyı normal dağılıma en yakın hale getirecek λ parametresini otomatik olarak bulur.
rc_bc, bc_params = stats.boxcox(biz_df['review_count'])
bc_params
# -0.4106510862321085
```

**Orijinal, Logaritma Dönüşümü Uygulanmış ve Box-Cox Dönüşümü Uygulanmış Yorum Sayılarının Histogramlarını Görselleştirme**

```python
fig, (ax1, ax2, ax3) = plt.subplots(3,1)
# Orijinal yorum sayısı histogramı
biz_df['review_count'].hist(ax=ax1, bins=100)
ax1.set_yscale('log')
ax1.tick_params(labelsize=14)
ax1.set_title('Yorum Sayıları Histogramı', fontsize=14)
ax1.set_xlabel('')
ax1.set_ylabel('Frekans', fontsize=14)

# Log dönüşümü uygulanmış yorum sayısı histogramı
biz_df['rc_log'].hist(ax=ax2, bins=100)
ax2.set_yscale('log')
ax2.tick_params(labelsize=14)
ax2.set_title('Log Dönüşümü Uygulanmış Yorum Sayıları Histogramı', fontsize=14)
ax2.set_xlabel('')
ax2.set_ylabel('Frekans', fontsize=14)

# Box-Cox dönüşümü uygulanmış yorum sayısı histogramı
biz_df['rc_bc'].hist(ax=ax3, bins=100)
ax3.set_yscale('log')
ax3.tick_params(labelsize=14)
ax3.set_title('Box-Cox Dönüşümü Uygulanmış Yorum Sayıları Histogramı', fontsize=14)
ax3.set_xlabel('')
ax3.set_ylabel('Frekans', fontsize=14)
```

![](.\i\010.png)

**Bir Olasılık Grafiği (Probability Plot) veya Probplot, bir veri kümesinin ampirik dağılımını teorik bir dağılımla görsel olarak karşılaştırmanın kolay bir yoludur.** Bu, gözlemlenen ve teorik kuantillerin bir dağılım grafiğiyle gösterilmesidir. **Şekil 2-14**, orijinal ve dönüşüm uygulanmış **Yelp yorum sayıları** verilerinin **normal dağılım** ile karşılaştırıldığı probplotları göstermektedir (**Örnek 2-14**'e bakınız). Gözlemlenen veriler kesinlikle pozitif olduğu için, Gauss dağılımı negatif olabileceğinden, kuantillerin negatif uçta asla örtüşmemesi beklenir. Bu nedenle, odak noktamız pozitif taraftadır. Bu noktada, orijinal sayımların, normal dağılımdan çok daha ağır kuyruklu olduğu açıktır. (Sıralı değerler 4.000'e kadar giderken, teorik kuantiller yalnızca 4'e kadar uzanır.) Hem düz log dönüşümü hem de optimal Box-Cox dönüşümü, pozitif kuyrukları normal dağılıma daha yakınlaştırır. Optimal Box-Cox dönüşümü, log dönüşümüne kıyasla kuyrukları daha fazla düzleştirir, çünkü kuyruk, kırmızı diyagonal eşdeğerlik çizgisi altında düzleştiği için bu durum belirgindir.

```python
fig2, (ax1, ax2, ax3) = plt.subplots(3,1)
prob1 = stats.probplot(biz_df['review_count'], dist=stats.norm, plot=ax1)
ax1.set_xlabel('')
ax1.set_title('Probplot against normal distribution')
prob2 = stats.probplot(biz_df['rc_log'], dist=stats.norm, plot=ax2)
ax2.set_xlabel('')
ax2.set_title('Probplot after log transform')
prob3 = stats.probplot(biz_df['rc_bc'], dist=stats.norm, plot=ax3)
ax3.set_xlabel('Theoretical quantiles')
ax3.set_title('Probplot after Box-Cox transform')
```

![](.\i\011.png)

# Özellik Ölçekleme veya Normalizasyonu

Bazı özellikler, örneğin enlem veya boylam, belirli bir değeri aşmaz. Diğer sayısal özellikler ise, örneğin sayım verileri, sınırsız şekilde artabilir. Girdi verilerinin düzgün fonksiyonlarıyla çalışan modeller, örneğin doğrusal regresyon, lojistik regresyon veya matris kullanan diğer modeller, girdi verisinin ölçeğinden etkilenir. Ancak, ağaç tabanlı modeller bu ölçekten etkilenmezler. Eğer modeliniz, girdi özelliklerinin ölçeğine duyarlıysa, özellik ölçekleme yardımcı olabilir. Adından da anlaşılacağı gibi, özellik ölçekleme, özelliklerin ölçeğini değiştirir. Bazen buna özellik normalizasyonu da denir. Özellik ölçekleme genellikle her bir özellik için ayrı ayrı yapılır. Şimdi, her biri farklı bir özellik değeri dağılımı ile sonuçlanan birkaç yaygın ölçekleme işlemi türünü tartışacağız.

**Bol sıfırlı matris gibi verilerde ölçekleme yapılmamalı ya da dikkatli yapılmalı.**

# ℓ2 Normalizasyonu

Bu teknik, orijinal özellik değerini **ℓ2 normu** (veya **Öklid normu**) ile böler. ℓ2 normu şu şekilde tanımlanır:

$$[
\tilde{x} = \frac{x}{| x |_2}
]$$

ℓ2 normu, **koordinat uzayındaki vektörün uzunluğunu** ölçer. Bu tanım, **Pisagor Teoremi**'nden türetilebilir ve dik üçgenin kenar uzunlukları verildiğinde hipotenüsün uzunluğunu verir:

$$[
| x |_2 = \sqrt{x_1^2 + x_2^2 + \dots + x_m^2}
]$$

ℓ2 normu, verisetindeki her bir özellik için değerlerin karelerini toplar ve ardından karekökünü alır. ℓ2 normalizasyonundan sonra, **özellik sütununda norm 1** olur. Bu işlem bazen **ℓ2 ölçekleme** olarak da adlandırılır. (Genel olarak, **ölçekleme** sabit bir sayı ile çarpmayı ifade ederken, **normalizasyon** birden fazla işlem içerebilir.)

ℓ2 normalizasyonunu görselleştiren bir örnek;

![](.\i\012.png)




---
---
---
---
---
---

# 

```py

```

![](.\i\01.png)