# Dinleme Sayısını Binleme

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

![](.\i\001.png)

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

![](.\i\002.png)

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











# 

```py

```

![](.\i\001.png)