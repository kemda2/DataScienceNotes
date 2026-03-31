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

**Özellik Ölçekleme Örneği**

```python
import pandas as pd
import sklearn.preprocessing as preproc

# Online News Popularity veri kümesini yükle
df = pd.read_csv('OnlineNewsPopularity.csv', delimiter=', ')

# Orijinal veriye bak - bir makaledeki kelime sayısı
df['n_tokens_content'].as_matrix()
# array([ 219., 255., 211., ..., 442., 682., 157.])

# Min-max ölçekleme
df['minmax'] = preproc.minmax_scale(df[['n_tokens_content']])
df['minmax'].as_matrix()
# array([ 0.02584376, 0.03009205, 0.02489969, ..., 0.05215955, 0.08048147, 0.01852726])

# Standartlaştırma - tanım gereği, bazı çıktılar negatif olacaktır
df['standardized'] = preproc.StandardScaler().fit_transform(df[['n_tokens_content']])
df['standardized'].as_matrix()
# array([-0.69521045, -0.61879381, -0.71219192, ..., -0.2218518 , 0.28759248, -0.82681689])

# L2 normalizasyonu
df['l2_normalized'] = preproc.normalize(df[['n_tokens_content']], axis=0)
df['l2_normalized'].as_matrix()
# array([ 0.00152439, 0.00177498, 0.00146871, ..., 0.00307663, 0.0047472 , 0.00109283])
```

```python
fig, (ax1, ax2, ax3, ax4) = plt.subplots(4,1)
fig.tight_layout()
df['n_tokens_content'].hist(ax=ax1, bins=100)
ax1.tick_params(labelsize=14)
ax1.set_xlabel('Makale Kelime Sayısı', fontsize=14)
ax1.set_ylabel('Makale Sayısı', fontsize=14)

df['minmax'].hist(ax=ax2, bins=100)
ax2.tick_params(labelsize=14)
ax2.set_xlabel('Min-max Ölçeklenmiş Kelime Sayısı', fontsize=14)
ax2.set_ylabel('Makale Sayısı', fontsize=14)

df['standardized'].hist(ax=ax3, bins=100)
ax3.tick_params(labelsize=14)
ax3.set_xlabel('Standartlaştırılmış Kelime Sayısı', fontsize=14)
ax3.set_ylabel('Makale Sayısı', fontsize=14)

df['l2_normalized'].hist(ax=ax4, bins=100)
ax4.tick_params(labelsize=14)
ax4.set_xlabel('L2-Normalize Edilmiş Kelime Sayısı', fontsize=14)
ax4.set_ylabel('Makale Sayısı', fontsize=14)
```

![](.\i\013.png)

# İnteraksiyon Özellikleri

Basit bir çiftli etkileşim özelliği, iki özelliğin çarpımıdır. Bu, **mantıksal AND** ile benzer bir analojidir. Çiftli etkileşim, bir dizi koşul açısından sonucu ifade eder: "satın alma 98121 posta kodundan geliyor" **VE** "kullanıcının yaşı 18 ile 35 arasında". **Karar ağacı tabanlı modeller** bunu otomatik olarak alır, ancak genelleştirilmiş doğrusal modeller genellikle etkileşim özelliklerinden çok faydalanır.

Bir basit doğrusal model, bireysel girdi özelliklerinin **x1, x2, ... xn** doğrusal birleşimini kullanarak sonucu **y** tahmin eder:

$$
[
y = w_1 x_1 + w_2 x_2 + ... + w_n x_n
]
$$

Doğrusal modeli kolayca genişletmenin bir yolu, girdi özelliklerinin çiftlerinin kombinasyonlarını dahil etmektir:

$$
[
y = w_1 x_1 + w_2 x_2 + ... + w_n x_n + w_{1,1} x_1 x_1 + w_{1,2} x_1 x_2 + w_{1,3} x_1 x_3 + ...
]
$$

Bu, özellikler arasındaki etkileşimleri yakalamamıza olanak tanır ve bu çiftler **etkileşim özellikleri** olarak adlandırılır. Eğer **x1** ve **x2** ikili (binary) ise, o zaman **x1 x2** çarpımı, **x1 VE x2** mantıksal fonksiyonunu temsil eder. Örneğin, müşteri tercihini **yaş** ve **lokasyon** bilgisi üzerinden tahmin etmeye çalışalım. Bu örnekte, yalnızca kullanıcının yaşı ya da lokasyonu üzerinden tahmin yapmak yerine, etkileşim özellikleri, modelin kullanıcının belirli bir yaşta **VE** belirli bir lokasyonda olduğunu göz önünde bulundurarak tahmin yapmasına olanak tanır.

**Örnek 2-17**, **UCI Online News Popularity** veri kümesindeki çiftli etkileşim özelliklerini kullanarak her haber makalesi için **paylaşım sayısını** tahmin eder. Sonuçlar, etkileşim özelliklerinin, yalnızca tekil özelliklere kıyasla doğruluğu biraz artırdığını göstermektedir. Her iki yöntem, **Örnek 2-9**'da kullanılan tek bir özellik olan makalenin **kelime sayısının** (log dönüşümü uygulanmış veya uygulanmamış) tahmin olarak kullanıldığı modelden daha iyi performans göstermektedir.

**Örnek 2-17. Tahminde Etkileşim Özelliklerinin Kullanımı**

```python 
from sklearn import linear_model
from sklearn.model_selection import train_test_split
import sklearn.preprocessing as preproc

# Varsayalım ki df, UCI Online News Popularity veri kümesini içeren bir Pandas DataFrame'dir
df.columns
Index(['url', 'timedelta', 'n_tokens_title', 'n_tokens_content', 
'n_unique_tokens', 'n_non_stop_words', 'n_non_stop_unique_tokens', 
'num_hrefs', 'num_self_hrefs', 'num_imgs', 'num_videos', 
'average_token_length', 'num_keywords', 'data_channel_is_lifestyle', 
'data_channel_is_entertainment', 'data_channel_is_bus', 
'data_channel_is_socmed', 'data_channel_is_tech', 'data_channel_is_world', 
'kw_min_min', 'kw_max_min', 'kw_avg_min', 'kw_min_max', 'kw_max_max', 
'kw_avg_max', 'kw_min_avg', 'kw_max_avg', 'kw_avg_avg', 
'self_reference_min_shares', 'self_reference_max_shares', 
'self_reference_avg_sharess', 'weekday_is_monday', 'weekday_is_tuesday', 
'weekday_is_wednesday', 'weekday_is_thursday', 'weekday_is_friday', 
'weekday_is_saturday', 'weekday_is_sunday', 'is_weekend', 'LDA_00', 
'LDA_01', 'LDA_02', 'LDA_03', 'LDA_04', 'global_subjectivity', 
'global_sentiment_polarity', 'global_rate_positive_words', 
'global_rate_negative_words', 'rate_positive_words', 'rate_negative_words', 
'avg_positive_polarity', 'min_positive_polarity', 'max_positive_polarity', 
'avg_negative_polarity', 'min_negative_polarity', 'max_negative_polarity', 
'title_subjectivity', 'title_sentiment_polarity', 'abs_title_subjectivity', 
'abs_title_sentiment_polarity', 'shares'], dtype='object')

# Modelde özellikleri tekil özellikler olarak seçin, türetilmiş özellikleri geçin
features = ['n_tokens_title', 'n_tokens_content', 
'n_unique_tokens', 'n_non_stop_words', 'n_non_stop_unique_tokens', 
'num_hrefs', 'num_self_hrefs', 'num_imgs', 'num_videos', 
'average_token_length', 'num_keywords', 'data_channel_is_lifestyle', 
'data_channel_is_entertainment', 'data_channel_is_bus', 
'data_channel_is_socmed', 'data_channel_is_tech', 'data_channel_is_world']
X = df[features]
y = df[['shares']]

# Çiftli etkileşim özelliklerini oluşturun, sabit bias terimini atlayın
X2 = preproc.PolynomialFeatures(include_bias=False).fit_transform(X)
X2.shape
(39644, 170)

# Hem özellik setleri için eğitim/test setlerini oluşturun
X1_train, X1_test, X2_train, X2_test, y_train, y_test = \
train_test_split(X, X2, y, test_size=0.3, random_state=123)

def evaluate_feature(X_train, X_test, y_train, y_test):
    """Eğitim setinde doğrusal regresyon modelini eğitin ve 
    test setinde skor yapın"""
    model = linear_model.LinearRegression().fit(X_train, y_train)
    r_score = model.score(X_test, y_test)
    return (model, r_score)

# Modelleri eğitin ve iki özellik setiyle karşılaştırma yapın
(m1, r1) = evaluate_feature(X1_train, X1_test, y_train, y_test)
(m2, r2) = evaluate_feature(X2_train, X2_test, y_train, y_test)
print("Tekil özelliklerle R-kare skoru: %0.5f" % r1)
print("Çiftli özelliklerle R-kare skoru: %0.10f" % r2)
# Tekil özelliklerle R-kare skoru: 0.00924
# Çiftli özelliklerle R-kare skoru: 0.0113276523
```

**Etkileşim Özelliklerinin Avantajları ve Zorlukları**

Etkileşim özellikleri çok basit bir şekilde formüle edilebilir, ancak bunları kullanmak maliyetli olabilir. Çiftli etkileşim özellikleri içeren bir doğrusal modelin eğitim ve skorlama süresi, **O(n)**'den **O(n²)**'ye çıkar, burada **n** tekil özelliklerin sayısını ifade eder.

Daha yüksek dereceli etkileşim özelliklerinin **hesaplama maliyetinden kaçınmak** için birkaç yol vardır. Biri, tüm etkileşim özellikleri üzerinde **özellik seçimi** yapmaktır. Alternatif olarak, daha küçük sayıda karmaşık özellik tasarlayarak bu durumu aşabilirsiniz. Her iki stratejinin de avantajları ve dezavantajları vardır. **Özellik seçimi**, bir problemin en iyi özelliklerini seçmek için hesaplama yöntemleri kullanır (bu teknik yalnızca etkileşim özellikleriyle sınırlı değildir). Ancak, bazı özellik seçimi teknikleri hâlâ **çok sayıda özellik** ile birden fazla model eğitmeyi gerektirir.

**El yapımı karmaşık özellikler**, yalnızca küçük bir sayıda kullanıldığında yeterince ifade edici olabilir, bu da modelin eğitim süresini kısaltır, ancak özelliklerin kendisi **hesaplama maliyetini** artırabilir ve modelin skorlama aşamasını zorlaştırabilir. El yapımı (veya makineyle öğrenilen) karmaşık özelliklerin iyi örneklerine **Bölüm 8**'de göz atabilirsiniz. Şimdi, bazı **özellik seçimi tekniklerine** göz atalım.

**Özellik Seçimi**

Özellik seçimi teknikleri, son modelin karmaşıklığını azaltmak için faydasız özellikleri **keser**. Nihai hedef, tahmin doğruluğunda fazla bir kayıp olmadan daha hızlı hesaplanabilen **basit bir model** elde etmektir. Böyle bir modele ulaşmak için, bazı özellik seçimi teknikleri birden fazla aday modelin eğitilmesini gerektirir. Diğer bir deyişle, özellik seçimi **eğitim süresini azaltmak** ile ilgili değildir—bazı teknikler genel eğitim süresini artırsa da, **modelin skorlama süresini azaltmak** ile ilgilidir.

Genel olarak, özellik seçimi teknikleri üç ana sınıfa ayrılır:

**Filtreleme (Filtering)**

Filtreleme teknikleri, özellikleri önceden işleyerek model için faydalı olmayacak olanları **ayıklar**. Örneğin, her bir özellik ile hedef değişken arasındaki **korelasyonu** veya **karşılıklı bilgi**yi hesaplayabilir ve eşiğin altında kalan özellikleri filtreleyebilirsiniz. **Bölüm 3**, metin özellikleri için bu tekniklere örnekler sunmaktadır. Filtreleme teknikleri, aşağıda açıklanan **sarma (wrapper)** yöntemlerinden çok daha ucuzdur, ancak kullanılan modelin dikkate alınmaması nedeniyle **doğru özellikleri seçemeyebilirler**. Bu yüzden, özellikleri **model eğitim adımına geçmeden** önce yanlışlıkla faydalı özellikleri elememek için **filtrelemeyi dikkatli yapmak** en iyisidir.

**Sarma Yöntemleri (Wrapper Methods)**

Bu yöntemler pahalıdır, ancak **özelliklerin alt kümelerini** denemenize olanak tanır, yani yalnızca kendileri faydalı olmayan ancak bir arada kullanıldığında faydalı olan özellikleri **yanlışlıkla elemek**ten kaçınırsınız. Sarma yöntemi, modeli **kara kutu** olarak kabul eder ve önerilen özellik alt kümesinin **kalite skorunu** sağlar. Bu süreçte, alt küme kademeli olarak iyileştirilir.

**Gömülü Yöntemler (Embedded Methods)**

Bu yöntemler, **özellik seçimini** model eğitim sürecinin bir parçası olarak gerçekleştirir. Örneğin, **karar ağaçları**, her eğitim adımında ağacı bölecek bir özellik seçtiği için doğal olarak **özellik seçimi** yapar. Bir diğer örnek, herhangi bir **doğrusal modelin eğitim hedefine** eklenebilecek **ℓ1 düzenleyicisidir**. ℓ1 düzenleyicisi, **az sayıda özellik** kullanan modelleri teşvik eder, bu nedenle **modelde seyreklik kısıtlaması** olarak da bilinir. Gömülü yöntemler, model eğitim sürecinin bir parçası olarak özellik seçimi gerçekleştirir. Sarma yöntemlerinden daha güçlü değillerdir, ancak çok da pahalı değildirler. Filtrelemeyle karşılaştırıldığında, gömülü yöntemler, modele özgü özellikleri seçer. Bu açıdan bakıldığında, gömülü yöntemler **hesaplama maliyeti** ile **sonuç kalitesi** arasında bir denge kurar.

Özellik seçimi hakkında kapsamlı bir inceleme, bu kitabın kapsamı dışındadır. İlgilenen okurlar, **Guyon ve Elisseeff (2003)** tarafından yapılan anket makalesine başvurabilirler.

# Text Data: Flattening, Filtering and Chunking (Geçtim 2 bölümü)

# One-Hot Kodlama

Daha iyi bir yöntem, bir grup **bit** kullanmaktır. Her bit, olası bir kategoriyi temsil eder. Eğer değişken aynı anda birden fazla kategoriye ait olamazsa, o zaman gruptaki yalnızca bir bit "açık" olabilir. Bu yönteme **one-hot encoding** (tek sıcak kodlama) denir ve **scikit-learn**'de **sklearn.preprocessing.OneHotEncoder** olarak uygulanmıştır. Her bir bit, bir özelliktir. Bu nedenle, **k** olası kategorisi olan bir kategorik değişken, **k uzunluğunda** bir özellik vektörü olarak kodlanır. **Tablo 5-1**'de bir örnek gösterilmektedir.

**Tablo 5-1: Üç Şehir İçin One-Hot Kodlaması**

| Şehir    | One-Hot Kodlama |
| -------- | --------------- |
| Paris    | [1, 0, 0]       |
| New York | [0, 1, 0]       |
| Londra   | [0, 0, 1]       |

Bu tabloda, her şehir için bir **bit dizisi** yer alır. Paris, **[1, 0, 0]** ile temsil edilir, New York **[0, 1, 0]** ve Londra ise **[0, 0, 1]** ile temsil edilir. Bu şekilde her kategoriye karşılık gelen bir vektör oluşturulur ve bu, metni sayısal bir formata dönüştürmek için yaygın bir yöntemdir.

# Dummy Kodlama

**One-hot encoding**'in problemi, **k** serbestlik derecesi sağlamasıdır, ancak değişkenin kendisi yalnızca **k-1** serbestlik derecesine ihtiyaç duyar. **Dummy kodlama**, temsil için yalnızca **k-1** özellik kullanarak fazla serbestlik derecesini ortadan kaldırır (bkz. Tablo 5-2). Bir özellik "atılır" ve **tüm sıfırlardan oluşan vektörle** temsil edilir. Buna **referans kategori** denir. **Dummy kodlama** ve **one-hot encoding**, Pandas'ta **pandas.get_dummies** olarak uygulanır.

**Tablo 5-2. Üç şehir için Dummy kodlama**

|               | e1 | e2 |
| ------------- | -- | -- |
| San Francisco | 1  | 0  |
| New York      | 0  | 1  |
| Seattle       | 0  | 0  |

Dummy kodlama ile yapılan modellemenin çıktısı, **one-hot encoding**'e kıyasla daha **yorumlanabilir**dir. Bu, basit bir **doğrusal regresyon problemi** ile kolayca görülebilir. Diyelim ki, üç şehirdeki daire kiralama fiyatları hakkında bazı verilerimiz var: **San Francisco**, **New York** ve **Seattle** (bkz. Tablo 5-3).

**Tablo 5-3. Üç şehirdeki daire fiyatlarına ait toy veri kümesi**

| Şehir   | Kira |
| ------- | ---- |
| SF      | 3999 |
| SF      | 4000 |
| SF      | 4001 |
| NYC     | 3499 |
| NYC     | 3500 |
| NYC     | 3501 |
| Seattle | 2499 |
| Seattle | 2500 |
| Seattle | 2501 |

**Bir Kategorik Değişken Üzerinde Doğrusal Regresyon Kullanmak**

Sadece şehrin kimliğine dayalı olarak kira fiyatını tahmin etmek için bir **doğrusal regresyon** modeli eğitebiliriz (Örnek 5-1'e bakınız).
Doğrusal regresyon modeli şu şekilde yazılabilir:
$$
( y = w_1x_1 + ... + w_nx_n )
$$
Bir ekstra sabit terim olan **intercept** (kesme) teriminin eklenmesi yaygındır, böylece ( y ) sıfır olmayan bir değeri alabilir, özellikle de ( x )'lar sıfır olduğunda:
$$
( y = w_1x_1 + ... + w_nx_n + b )
$$

**Örnek 5-1. Kategorik Değişken Üzerinde One-Hot ve Dummy Kodları Kullanarak Doğrusal Regresyon**

```python
import pandas
from sklearn import linear_model

# New York, San Francisco ve Seattle'daki daire kira fiyatlarına ait toy veri kümesi tanımlıyoruz
df = pd.DataFrame({
    'City': ['SF', 'SF', 'SF', 'NYC', 'NYC', 'NYC',
    'Seattle', 'Seattle', 'Seattle'],
    'Rent': [3999, 4000, 4001, 3499, 3500, 3501, 2499, 2500, 2501]
    })
df['Rent'].mean()
# 3333.3333333333335

# DataFrame'deki kategorik değişkenleri one-hot encoding kullanarak dönüştürün ve bir doğrusal regresyon modeli eğitin
one_hot_df = pd.get_dummies(df, prefix=['city'])
one_hot_df
   Rent  city_NYC  city_SF  city_Seattle
0  3999       0.0      1.0           0.0
1  4000       0.0      1.0           0.0
2  4001       0.0      1.0           0.0
3  3499       1.0      0.0           0.0
4  3500       1.0      0.0           0.0
5  3501       1.0      0.0           0.0
6  2499       0.0      0.0           1.0
7  2500       0.0      0.0           1.0
8  2501       0.0      0.0           1.0

model = linear_model.LinearRegression()
model.fit(one_hot_df[['city_NYC', 'city_SF', 'city_Seattle']],
    one_hot_df['Rent'])

model.coef_
# array([ 166.66666667,  666.66666667, -833.33333333])

model.intercept_
# 3333.3333333333335

# Dummy kodlamada bir doğrusal regresyon modeli eğitin
# 'drop_first' bayrağını belirleyin, böylece dummy kodlama elde edelim
dummy_df = pd.get_dummies(df, prefix=['city'], drop_first=True)
dummy_df
#    Rent  city_SF  city_Seattle
# 0  3999      1.0           0.0
# 1  4000      1.0           0.0
# 2  4001      1.0           0.0
# 3  3499      0.0           0.0
# 4  3500      0.0           0.0
# 5  3501      0.0           0.0
# 6  2499      0.0           1.0
# 7  2500      0.0           1.0
# 8  2501      0.0           1.0

model.fit(dummy_df[['city_SF', 'city_Seattle']], dummy_df['Rent'])
model.coef_
# array([ 500., -1000.])

model.intercept_
# 3500.0
```

**One-hot encoding** ile, intercept terimi hedef değişken **Rent**'in global ortalamasını temsil eder ve her bir doğrusal katsayı, o şehrin ortalama kirasının global ortalamadan ne kadar farklı olduğunu gösterir.

**Dummy kodlama** ile ise, bias (sapma) katsayısı, referans kategori için yanıt değişkeni ( y )'nin ortalama değerini temsil eder. Bu örnekte referans kategori **NYC**'dir. ( i )'inci özellik için katsayı, o şehirdeki yanıt değeri ile referans kategorinin yanıt değeri arasındaki farktır.

Tablo 5-4'te, bu yöntemlerin doğrusal modeller için nasıl çok farklı katsayılar ürettiğini net bir şekilde görebilirsiniz.

|                  | x1     | x2     | x3      | b       |
| ---------------- | ------ | ------ | ------- | ------- |
| One-hot encoding | 166.67 | 666.67 | –833.33 | 3333.33 |
| Dummy coding     | 0      | 500    | –1000   | 3500    |

# Etkileşim Kodlama (Effect Coding)

Kategorik değişken kodlamasının bir başka varyantı **etkileşim kodlama**dır. Etkileşim kodlama, **dummy kodlama**ya çok benzer, ancak farkı, referans kategorisinin artık **tüm –1’lerden oluşan vektörle** temsil edilmesidir.

**Tablo 5-5. Üç şehri temsil eden kategorik değişkenin etkileşim kodlaması**

| e1            | e2 |    |
| ------------- | -- | -- |
| San Francisco | 1  | 0  |
| New York      | 0  | 1  |
| Seattle       | -1 | -1 |

Etkileşim kodlama, dummy kodlamaya çok benzer, ancak doğrusal regresyon modelleri **çok daha basit** bir şekilde yorumlanabilir. **Örnek 5-2**, etkileşim kodlama ile verilen girdilerle ne olduğunu gösterir. **Intercept** terimi hedef değişkenin global ortalamasını temsil eder ve bireysel katsayılar, her bir kategorinin ortalamasının global ortalamadan ne kadar farklı olduğunu gösterir. (Bu, kategorinin **ana etkisi** olarak adlandırılır, bu yüzden **"etkileşim kodlama"** adı verilmiştir.) **One-hot encoding** aslında aynı intercept ve katsayıları elde eder, ancak bu durumda her şehir için doğrusal katsayılar vardır. Etkileşim kodlamada ise, referans kategoriyi temsil eden tek bir özellik yoktur, bu nedenle referans kategorisinin etkisi, diğer tüm kategorilerin katsayılarının negatif toplamı olarak ayrı ayrı hesaplanmalıdır. (Daha fazla bilgi için UCLA IDRE web sitesindeki **“FAQ: What is effect coding?”** başlığına bakınız.)

**Örnek 5-2. Etkileşim Kodlama ile Doğrusal Regresyon**

```python
>>> effect_df = dummy_df.copy()
>>> effect_df.ix[3:5, ['city_SF', 'city_Seattle']] = -1.0
>>> effect_df
   Rent  city_SF  city_Seattle
0  3999      1.0           0.0
1  4000      1.0           0.0
2  4001      1.0           0.0
3  3499     -1.0          -1.0
4  3500     -1.0          -1.0
5  3501     -1.0          -1.0
6  2499      0.0           1.0
7  2500      0.0           1.0
8  2501      0.0           1.0
>>> model.fit(effect_df[['city_SF', 'city_Seattle']], effect_df['Rent'])

>>> model.coef_
array([ 666.66666667, -833.33333333])

>>> model.intercept_
3333.3333333333335
```

# Kategorik Değişken Kodlamalarının Avantajları ve Dezavantajları

**One-hot**, **dummy** ve **effect kodlama** birbirine çok benzer. Her birinin avantajları ve dezavantajları vardır. **One-hot encoding** tekrarcıdır, bu da aynı problem için birden fazla geçerli modelin olmasını sağlar. Bu çokluk bazen yorumlama için problem yaratabilir, ancak avantajı, her bir özelliğin açıkça bir kategoriye karşılık gelmesidir. Ayrıca, eksik veriler **tüm sıfırlar vektörü** olarak kodlanabilir ve çıktı, hedef değişkenin genel ortalaması olmalıdır.

**Dummy coding** ve **effect coding** tekrarcı değildir. Bunlar benzersiz ve yorumlanabilir modeller oluşturur. **Dummy coding**'in dezavantajı, eksik verilerle kolayca başa çıkamamasıdır, çünkü tüm sıfırlar vektörü zaten referans kategoriye eşlenmiştir. Ayrıca, her kategorinin etkisini referans kategoriye göre kodlar, bu da garip görünebilir.

**Effect coding**, referans kategorisi için farklı bir kod kullanarak bu sorunu çözer, ancak **tüm -1'ler vektörü** yoğun bir vektördür, bu da hem depolama hem de hesaplama açısından pahalıdır. Bu nedenle, popüler makine öğrenimi yazılım paketleri (örneğin Pandas ve scikit-learn), **effect coding** yerine **dummy coding** veya **one-hot encoding** kullanmayı tercih etmektedir.

Üç kodlama tekniği de kategorilerin sayısı çok büyük olduğunda verimsiz hale gelir. **Aşırı büyük kategorik değişkenleri** ele almak için farklı stratejiler gereklidir.

---

**Büyük Kategorik Değişkenlerle Baş Etme**

İnternette otomatik veri toplama, büyük kategorik değişkenler üretebilir. Bu, hedefli reklamcılık ve dolandırıcılık tespiti gibi uygulamalarda yaygındır.

* **Hedefli reklamcılık** uygulamasında, bir kullanıcıya reklamları eşleştirmek amaçlanır. Özellikler arasında kullanıcı kimliği, reklamın web sitesi alan adı, arama sorgusu, mevcut sayfa ve bu özelliklerin tüm çiftli birleşimleri bulunur. Bu her biri büyük kategorik bir değişkendir.

Bu büyük kategorik değişkenlerle başa çıkmanın iki çözümü vardır:

1. **Kodlama ile uğraşmamak**: Eğitim için ucuz olan basit bir model kullanmak. One-hot encoding’i çok sayıda makinede bir doğrusal modele (lojistik regresyon veya doğrusal destek vektör makineleri) beslemek.

2. **Özellikleri sıkıştırmak**: İki seçenek vardır:

   * Özellik hash'leme, doğrusal modellerde yaygındır.
   * Bin sayma, doğrusal modeller ve ağaçlar için de popülerdir.

**Feature Hashing (Özellik Hash'leme)**, potansiyel olarak sınırsız bir tam sayıyı, [1, m] aralığında sonlu bir tam sayıya eşleyen deterministik bir fonksiyondur. Bu, verilerin sayısal olarak temsil edilebilen her türü için oluşturulabilir (örneğin, sayılar, dizeler, karmaşık yapılar vb.). Hash fonksiyonu ile aynı numaraya sahip olan toplar (anahtarlar) her zaman aynı kutuya yönlendirilir. Bu, özellik alanını korurken makine öğrenimi eğitim ve değerlendirme döngülerinde depolama ve işleme zamanını azaltır.

![](.\i\014.png)

Çok sayıda özellik olduğunda, özellik vektörünü depolamak çok fazla yer kaplayabilir. Özellik hash’leme, orijinal özellik vektörünü, özellik ID’sine bir hash fonksiyonu uygulayarak m boyutlu bir vektöre sıkıştırır, bu durum Örnek 5-3'te gösterilmiştir. Örneğin, eğer orijinal özellikler bir belgedeki kelimelerse, hash’lenmiş versiyon, girişteki benzersiz kelime sayısına bakılmaksızın sabit bir kelime dağarcığı boyutuna sahip olacaktır.

```python
def hash_features(word_list, m):
    output = [0] * m
    for word in word_list:
    index = hash_fcn(word) % m
    output[index] += 1
    return output
```

Özellik hash’lemenin bir başka çeşidi, bir işaret bileşeni ekler, böylece sayılar ya hashed kutuya eklenir ya da çıkarılır. İstatistiksel açıdan bakıldığında, bu, hash’lenmiş özellikler arasındaki iç çarpanların, orijinal özelliklerinkiyle beklenti açısından eşit olmasını sağlar.

```python
def hash_features(word_list, m):
    output = [0] * m  # Boyutu m olan bir vektör oluşturulur ve tüm değerleri 0 ile başlatılır.
    
    for word in word_list:  # Verilen kelime listesinde her bir kelime için döngü başlatılır.
        index = hash_fcn(word) % m  # Her kelime için bir hash fonksiyonu uygulanır, ardından m ile modül alınır.
        sign_bit = sign_hash(word) % 2  # Her kelime için bir işaret fonksiyonu (0 veya 1) elde edilir.
        
        if sign_bit == 0:  # Eğer işaret 0 ise, vektörde ilgili indeksteki değer bir azaltılır.
            output[index] -= 1
        else:  # Eğer işaret 1 ise, vektördeki değeri bir arttırılır.
            output[index] += 1
    
    return output  # Sonuç olarak, güncellenmiş özellik vektörü döndürülür.
```

Hash'leme işleminden sonra iç çarpanın değeri, orijinal iç çarpanın $O\left(\frac{1}{\sqrt{m}}\right)$ kadar yakınında olacaktır, bu nedenle hash tablosunun boyutu (m), kabul edilebilir hata miktarına göre seçilebilir. Pratikte, doğru (m)’yi seçmek bazı denemeler ve hatalar gerektirebilir.

Özellik hash'leme, özellik vektörlerinin ve katsayılarının iç çarpanını içeren modellerde, örneğin doğrusal modeller ve çekirdek yöntemlerinde kullanılabilir. Spam filtreleme görevinde başarılı olduğu gösterilmiştir (Weinberger ve diğerleri, 2009). Hedefli reklamcılık durumunda ise McMahan ve diğerleri (2013), (m)’nin milyarlar mertebesinde olması gerektiğini, aksi takdirde tahmin hatalarını kabul edilebilir bir seviyeye indiremediklerini raporlamaktadır; bu da depolama alanında yeterli bir tasarruf sağlamaz.
Özellik hash'lemenin bir dezavantajı, hash’lenmiş özelliklerin, orijinal özelliklerin birleştirilmiş hâli olmaları nedeniyle artık yorumlanabilir olmamalarıdır.
Örnek 5-5’te, scikit-learn’ün FeatureHasher’ını kullanarak, Yelp yorumları veri seti ile depolama ve yorumlanabilirlik arasında nasıl bir denge kurulduğunu gösteriyoruz.
Örnek 5-5. Özellik hash'leme (diğer adıyla “hashing hilesi”)

```python
import pandas as pd
import json
from sklearn.feature_extraction import FeatureHasher
from sys import getsizeof

# Load the first 10,000 reviews
f = open('yelp_academic_dataset_review.json')
js = []
for i in range(10000):
    js.append(json.loads(f.readline()))
f.close()

review_df = pd.DataFrame(js)

# Define m as equal to the unique number of business_ids
m = len(review_df.business_id.unique())
print(f"Number of unique business_ids (m): {m}")

# Apply FeatureHasher
h = FeatureHasher(n_features=m, input_type='string')
f = h.transform(review_df['business_id'])

# Check how it affects feature interpretability
print("First 5 unique business_ids:", review_df['business_id'].unique().tolist()[0:5])
# ['vcNAWiLM4dR7D2nwwJ7nCA',
# 'UsFtqoBl7naz8AVUBZMjQQ',
# 'cE27W9VPgO88Qxe4ol6y_g',
# 'HZdLhv6COCleJMo7nPl-RA',
# 'mVHrayjG3uZ_RLHkLj-AMg']

# Print the hashed feature array (as a dense array for readability)
print(f.toarray())
# array([[ 0., 0., 0., ..., 0., 0., 0.],
#        [ 0., 0., 0., ..., 0., 0., 0.],
#        [ 0., 0., 0., ..., 0., 0., 0.],
#        ...,
#        [ 0., 0., 0., ..., 0., 0., 0.],
#        [ 0., 0., 0., ..., 0., 0., 0.],
#        [ 0., 0., 0., ..., 0., 0., 0.]])

# Compare storage size
print('Our pandas Series, in bytes: ', getsizeof(review_df['business_id']))
print('Our hashed numpy array, in bytes: ', getsizeof(f))
# Our pandas Series, in bytes: 790104
# Our hashed numpy array, in bytes: 56
```

Özellik hash'lemenin bize nasıl hesaplama açısından fayda sağladığını açıkça görebiliyoruz, ancak bu faydayı elde ederken hemen kullanıcı yorumlanabilirliğinden feragat ediyoruz. Bu, veri keşfi ve görselleştirmeden büyük veri setleri için bir makine öğrenimi işlem hattına geçerken kabul edilmesi kolay bir takas (trade-off) durumudur.

# Bin Sayma

Bin sayma, makine öğrenimindeki uzun süreli yeniden keşiflerden biridir. Reklam tıklama oranı tahmininden donanım dalga tahminine kadar (Yeh ve Patt, 1991; Lee ve diğerleri, 1998; Chen ve diğerleri, 2009; Li ve diğerleri, 2010) çeşitli uygulamalarda yeniden keşfedilmiş ve kullanılmıştır. Ancak, bu bir özellik mühendisliği tekniği olduğu için, modelleme veya optimizasyon yöntemi olmadığından bu konuda bir araştırma makalesi yoktur. Bu tekniğin en ayrıntılı açıklamasına, Misha Bilenko'nun (2015) "Big Learning Made Easy—with Counts!" başlıklı blog yazısında ve ilgili slaytlarda rastlanabilir.

Bin sayma fikri son derece basittir: kategorik değişkenin değerini özellik olarak kullanmak yerine, bu değerin altında hedefin koşullu olasılığını kullanırız. Diğer bir deyişle, kategorik değerin kimliğini kodlamak yerine, o değerin ve tahmin etmek istediğimiz hedef arasındaki ilişki istatistiklerini hesaplarız. Naive Bayes sınıflandırıcılarına aşina olanlar için bu istatistik tanıdık gelecektir, çünkü bu, tüm özelliklerin bağımsız olduğu varsayımı altında sınıfın koşullu olasılığıdır. En iyi şekilde bir örnekle açıklanabilir (bkz. Tablo 5-6)."

| User  | Number of clicks | Number of nonclicks | Probability of click | QueryHash, AdDomain            | Number of clicks | Number of nonclicks | Probability of click |
|-------|------------------|---------------------|----------------------|--------------------------------|------------------|---------------------|----------------------|
| Alice | 5                | 120                 | 0.0400               | 0x598fd4fe, foo.com            | 5,000            | 30,000              | 0.167                |
| Bob   | 20               | 230                 | 0.0800               | 0x50fa3cc0, bar.org            | 100              | 900                 | 0.100                |
| ...   | ...              | ...                 | ...                  | ...                            | ...              | ...                 | ...                  |
| Joe   | 2                | 3                   | 0.4000               | 0x437a45e1, qux.net            | 6                | 18                  | 0.250                |

**Bin Sayma**, istatistikleri hesaplamak için tarihsel verilerin mevcut olduğunu varsayar. **Tablo 5-6**, her olası kategorik değişken değeri için toplanmış tarihsel sayıları içerir. Kullanıcı "Alice"in herhangi bir reklamı tıklama sayısı ve tıklamama sayısına dayalı olarak, herhangi bir reklama tıklama olasılığını hesaplayabiliriz. Benzer şekilde, her hangi bir **query-ad domain** kombinasyonu için tıklama olasılığını hesaplayabiliriz. Eğitim aşamasında, her "Alice" gördüğümüzde, onun tıklama olasılığını model için giriş özelliği olarak kullanabiliriz. Aynı şey "0x437a45e1, qux.net" gibi QueryHash-AdDomain çiftleri için de geçerlidir.

Diyelim ki 10.000 kullanıcı var. **One-hot encoding** yöntemi, uzunluğu 10.000 olan seyrek bir vektör oluşturur ve mevcut veri noktasına karşılık gelen sütunda bir tane 1 bulunur. **Bin sayma** ise, 10.000 ikili sütunu tek bir özellik olarak 0 ile 1 arasında bir gerçek değerle kodlar.

Tarihsel tıklama oranına ek olarak başka özellikler de dahil edebiliriz: ham sayılar (tıklamalar ve tıklamamalar), log-odds oranı veya olasılığın herhangi bir türevi. Buradaki örneğimiz reklam tıklama oranlarını tahmin etmek için olsa da, bu teknik genel ikili sınıflandırma için de rahatlıkla uygulanabilir. Ayrıca, ikili sınıflandırıcıları çoklu sınıf sınıflandırmasına genişletmek için yaygın tekniklerle kolayca genişletilebilir; yani, bir karşısına birçok odds oranı veya diğer çoklu sınıf etiketleme yöntemleri ile.

**Bin Sayma için Odds Oranı ve Log Odds Oranı**

**Odds oranı**, genellikle iki ikili değişken arasındaki ilişkiyi tanımlar. İki değişkenin ilişkisini, "X doğru olduğunda Y'nin doğru olma olasılığı ne kadar daha yüksek?" sorusunu sorarak değerlendiririz. Örneğin, şu soruyu sorabiliriz: "Alice'in reklamı tıklama olasılığı, genel nüfusa göre ne kadar daha yüksek?" Burada, X ikili değişkeni "Alice şu anda kullanıcı" ve Y değişkeni "reklama tıklama ya da tıklamama"dır. Hesaplama, **iki yönlü kontenjan tablosu** olarak adlandırılan (esas olarak, X ve Y'nin dört olası kombinasyonuna karşılık gelen dört sayıyı içeren) tabloyu kullanır, bu tabloyu **Tablo 5-7**'de görebiliriz.

**Tablo 5-7**. Reklam tıklama ve kullanıcı için kontenjan tablosu:

|           | Click | Nonclick | Total  |
| --------- | ----- | -------- | ------ |
| Alice     | 5     | 120      | 125    |
| Not Alice | 995   | 18,880   | 19,875 |
| **Total** | 1,000 | 19,000   | 20,000 |

Verilen bir giriş değişkeni X ve bir hedef değişkeni Y için **odds oranı** şu şekilde tanımlanır:

$$
[
\text{odds oranı} = \frac{P(Y = 1 | X = 1)}{P(Y = 0 | X = 1)} \div \frac{P(Y = 1 | X = 0)}{P(Y = 0 | X = 0)}
]
$$

Örneğimizde, bu "Alice'in reklama tıklama olasılığı ile tıklamama olasılığı arasındaki fark" ile "diğer insanların reklama tıklama olasılığı ile tıklamama olasılığı arasındaki fark" oranı olarak çevrilebilir. Bu sayıyı, şu şekilde hesaplarız:

$$
[
\text{odds oranı (user, ad click)} = \frac{5 / 125}{120 / 125} \div \frac{995 / 19,875}{18,880 / 19,875} = 0.7906
]
$$

Daha basitçe, sadece paydada yer alan sayıya bakabiliriz, bu da tek bir kullanıcının (Alice) reklama tıklama olasılığını inceleyen bir oranı verir:

$$
[
\text{odds oranı (Alice, ad click)} = \frac{5}{125} \div \frac{120}{125} = 0.04166
]
$$

Olasılık oranları çok küçük veya çok büyük olabilir. (Örneğin, reklama neredeyse hiç tıklamayan kullanıcılar veya reklama çok daha sık tıklayan kullanıcılar olabilir.) Logaritma dönüşümü burada bize yardımcı olur. Logaritmanın bir diğer yararlı özelliği, bölmeyi çıkarmaya dönüştürmesidir:

$$
[
\text{log-odds oranı (Alice, ad click)} = \log \left( \frac{5}{125} \right) - \log \left( \frac{120}{125} \right) = - 3.178
]
$$

Kısacası, **bin sayma**, bir kategorik değişkeni, değeriyle ilgili istatistiklere dönüştürür. One-hot encoding gibi büyük ve seyrek bir kategorik değişken temsili, çok küçük, yoğun ve gerçek değerli bir sayısal temsile dönüştürülür (Bkz. Şekil 5-2).

![](.\i\015.png)

Uygulama açısından, bin sayma, her kategoriyi ve buna bağlı sayıları arasında bir harita depolamayı gerektirir. (Diğer istatistikler, ham sayılardan anlık olarak türetilebilir.) Bu nedenle, kategorik değişkenin benzersiz değerlerinin sayısı olan k'ya bağlı olarak O(k) bellek alanı gerektirir.

Bin saymayı pratikte göstermek için, Avazu tarafından düzenlenen bir Kaggle yarışmasından verileri kullanacağız. İşte veri seti hakkında bazı önemli istatistikler:

24 değişken vardır, bunlar arasında click (tıklama/tıklamama sayacı) ve device_id (bir reklamın hangi cihazda gösterildiğini takip eden bir değişken) bulunmaktadır.
Tam veri seti 40.428.967 gözlem içerir ve 2.686.408 benzersiz cihaz bulunmaktadır.

Avazu yarışmasının amacı, reklam verilerini kullanarak tıklama oranını tahmin etmekti, ancak biz bu veri setini, bin sayma tekniğinin, büyük miktarda akış verisi için özellik alanını nasıl büyük ölçüde azaltabileceğini göstermek için kullanacağız (Bkz. Örnek 5-6).

```python
import pandas as pd

# train_subset verisi, 6+GB veri setinin ilk 10K satırıdır
df = pd.read_csv('data/train_subset.csv')

# Her kategoride kaç benzersiz özellik olmalı?
print(f"Benzersiz cihaz sayısı: {len(df['device_id'].unique())}")

# Her kategori için:
# Theta = [counts, p(click), p(no click), p(click)/p(no click)]

def click_counting(x, bin_column):
    """
    Tıklama sayıları ve tıklamama sayıları hesaplanır.
    """
    # Tıklanan veriler
    clicks = pd.Series(x[x['click'] > 0][bin_column].value_counts(), name='clicks')
    
    # Tıklanmayan veriler
    no_clicks = pd.Series(x[x['click'] < 1][bin_column].value_counts(), name='no_clicks')
    
    # Hem tıklamalar hem de tıklanmamalar ile toplam veriler
    counts = pd.DataFrame([clicks, no_clicks]).T.fillna(0)
    counts['total_clicks'] = counts['clicks'].astype('int64') + counts['no_clicks'].astype('int64')
    return counts

def bin_counting(counts):
    """
    Bin sayma özelliklerini hesaplar.
    """
    counts['N+'] = counts['clicks'].astype('int64').divide(counts['total_clicks'].astype('int64'))
    counts['N-'] = counts['no_clicks'].astype('int64').divide(counts['total_clicks'].astype('int64'))
    counts['log_N+'] = counts['N+'].divide(counts['N-'])
    
    # Sadece bin-counting özelliklerini döndürmek için filtreleme yapılabilir
    bin_counts = counts.filter(items=['N+', 'N-', 'log_N+'])
    return counts, bin_counts

# Bin sayma örneği: device_id
bin_column = 'device_id'

# device_id için tıklama sayıları hesaplanıyor
device_clicks = click_counting(df.filter(items=[bin_column, 'click']), bin_column)

# Bin sayma hesaplaması yapılıyor
device_all, device_bin_counts = bin_counting(device_clicks)

# Tüm cihazların sayısını kontrol edelim
print(f"Tüm cihaz sayısı: {len(device_bin_counts)}")

# Tıklama sayısına göre sıralama yapalım
print(device_all.sort_values(by='total_clicks', ascending=False).head(4))
```

| device_id | clicks | no_clicks | total | N+      | N-      | log_N+  |
|-----------|--------|-----------|-------|---------|---------|---------|
| a99f214a  | 15729  | 71206     | 86935 | 0.180928| 0.819072| 0.220894|
| c357dbff  | 33     | 134       | 167   | 0.197605| 0.802395| 0.246269|
| 31da1bd0  | 0      | 62        | 62    | 0.000000| 1.000000| 0.000000|
| 936e92fb  | 5      | 54        | 59    | 0.084746| 0.915254| 0.092593|

# Nadir kategoriler ne olacak?

Tıpkı nadir kelimeler gibi, nadir kategoriler de özel bir tedavi gerektirir. Bir kullanıcıyı düşünün, yılda bir kez giriş yapan bir kullanıcı: Bu kullanıcının reklam tıklama oranını güvenilir bir şekilde tahmin etmek için çok az veri olacaktır. Dahası, nadir kategoriler sayılar tablosunda yer kaplar.

Bununla başa çıkmanın bir yolu, back-off (geri çekilme) tekniğini kullanmaktır; bu teknik, tüm nadir kategorilerin sayılarının özel bir kutuda toplanmasını sağlar (Bkz. Şekil 5-3). Eğer sayılar belirli bir eşiği aşarsa, kategori kendi sayılama istatistiklerine sahip olur. Aksi takdirde, back-off kutusundaki istatistikler kullanılır. Bu, esasen tek bir nadir kategorinin istatistiklerini, tüm nadir kategoriler üzerinde hesaplanmış istatistiklere geri döndürür. Back-off yöntemini kullanırken, istatistiklerin back-off kutusundan gelip gelmediğini belirten bir ikili gösterge eklemek faydalı olabilir.

![](.\i\016.png)

Bu problemi çözmenin bir diğer yolu ise count-min sketch (Cor‐mode ve Muthukrishnan, 2005) adlı tekniktir. Bu yöntemde, tüm kategoriler, nadir ya da sık görülen fark etmeksizin, birden fazla hash fonksiyonu ile, kategorilerin sayısından (k) çok daha küçük bir çıktı aralığı olan m'ye haritalanır. Bir istatistik alındığında, kategorinin tüm hash'leri yeniden hesaplanır ve en küçük istatistik döndürülür. Birden fazla hash fonksiyonu kullanmak, tek bir hash fonksiyonu içindeki çakışma olasılığını azaltır. Bu şema, hash fonksiyonlarının sayısı ile hash tablosunun boyutunun çarpımının, kategorilerin sayısından (k) küçük tutulabileceğini ve yine de düşük çakışma olasılığı sağlanabileceğini gösterir.

Şekil 5-4 bunu gösterir. Her bir öğe i, sayılar dizisinin her bir satırındaki bir hücreye haritalanır. ct öğesine bir güncelleme geldiğinde, ct her bir hücreye eklenir ve bu hücreler, h1...hd fonksiyonları kullanılarak hash'lenir.

![](.\i\017.png)

# Veri sızıntısına karşı korunma

Bin sayma yöntemi, gerekli istatistikleri oluşturmak için tarihsel verilere dayanır, bu da veri toplama süresi boyunca beklemeyi gerektirir ve öğrenme işlem hattında hafif bir gecikmeye yol açar. Ayrıca, veri dağılımı değiştiğinde, sayılar güncellenmelidir. Veri ne kadar hızlı değişirse, sayıları yeniden hesaplama sıklığı o kadar artar. Bu durum, kullanıcı tercihleri ve popüler sorguların çok hızlı değiştiği hedeflenmiş reklamcılık gibi uygulamalar için özellikle önemlidir; çünkü mevcut dağılıma adapte olmamak, reklam platformu için büyük kayıplara yol açabilir.

Birisi şu soruyu sorabilir: Neden aynı veri setini, ilgili istatistikleri hesaplamak ve modeli eğitmek için kullanmıyoruz? Bu fikir yeterince masum görünüyor. Ancak burada büyük bir sorun var: İstatistikler, modelin tahmin etmeye çalıştığı hedef değişkeni içeriyor. Çıktıyı kullanarak giriş özelliklerini hesaplamak, sızıntı olarak bilinen kötü bir probleme yol açar. Kısacası, sızıntı, modele daha iyi tahminler yapması için ona gerçekçi olmayan bir avantaj veren bilgilerin ifşa edilmesidir. Bu durum, test verilerinin eğitim setine sızması veya gelecekteki verilerin geçmişe sızması gibi durumlarda gerçekleşebilir. Model, gerçek zamanlı tahminler yaparken erişmemesi gereken bilgilere her eriştiğinde sızıntı oluşur. Kaggle’ın wiki sayfası, sızıntının daha fazla örneğini ve neden makine öğrenimi uygulamaları için kötü olduğunu açıklar.

Eğer bin sayma prosedürü, giriş istatistiğinin bir kısmını hesaplamak için mevcut veri noktasının etiketini kullanıyorsa, bu doğrudan sızıntı oluşturur. Bunu önlemenin bir yolu, sayım toplama (bin-count istatistiklerini hesaplamak için) ile eğitim arasında katı bir ayrım yapmaktır, bu şekilde Şekil 5-5'te gösterildiği gibi: Yani, sayım için daha önceki veri noktalarını kullanın, eğitim için mevcut veri noktalarını kullanın (kategorik değişkenleri az önce topladığımız tarihsel istatistiklere eşleyin) ve test için gelecekteki veri noktalarını kullanın. Bu, sızıntı sorununu çözer, ancak daha önce bahsedilen gecikmeyi (giriş istatistiklerinin ve dolayısıyla modelin mevcut veriye göre geride kalmasını) getirir.

![](.\i\018.png)

Başka bir çözümün, diferansiyel gizlilik temelli bir çözüm olduğunu görüyoruz. Bir istatistik, dağılımı bir veri noktası olup olmadığına bakılmaksızın yaklaşık olarak aynı kaldığı sürece, sızıntıya karşı dayanıklıdır. Pratikte, Laplace(0,1) dağılımına sahip küçük bir rastgele gürültü eklemek, tek bir veri noktasından kaynaklanabilecek potansiyel sızıntıyı gizlemek için yeterlidir. Bu fikir, leave-one-out counting yöntemiyle birleştirilerek, mevcut veriler üzerine istatistikler oluşturulabilir (Zhang, 2015).

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