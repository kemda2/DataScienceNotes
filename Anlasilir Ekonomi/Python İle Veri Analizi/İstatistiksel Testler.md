# Özet Tablosu

|Test Adı|Kullanım Alanı|Varsayımlar|Python Fonksiyonu|
|---|---|---|---|
|Pearson Korelasyon|Sürekli değişkenler arası doğrusal ilişki|Normal dağılım, doğrusal|`pearsonr`|
|Spearman Korelasyon|Monoton ilişki (sıralı veya sürekli)|Yok (non-parametrik)|`spearmanr`|
|Kruskal-Wallis|3+ bağımsız grubun medyan karşılaştırması|Bağımsız, sıralanabilir|`kruskal`|
|Fisher Exact|Küçük örnekli kategorik bağımsızlık testi|Kategorik, küçük örnek|`fisher_exact`|
|Kolmogorov-Smirnov|Dağılım uygunluk testi|Sürekli veri|`kstest`|
|Bartlett|Varyans homojenliği testi|Normal dağılım|`bartlett`|
|Durbin-Watson|Otokorelasyon testi (regresyon)|Regresyon model rezidüleri|`durbin_watson`|
|Z-Testi|Büyük örneklemde ortalama karşılaştırma|Varyans biliniyor|`ztest` (statsmodels)|
|Mantel-Haenszel|Katmanlı 2x2 tablo ilişki testi|Katmanlı yapı|(Özel kütüphane)|

## 1. **t-Testi (Student's t-test)**

### Kullanımı:

İki grup ortalamasının birbirinden anlamlı farklı olup olmadığını test eder.

### Varsayımlar:

- Veriler normal dağılıma uygun olmalı.
    
- Varyanslar homojen (eşit) olmalı.
    
- Örnekler bağımsız olmalı.
    

### Türleri:

- **Bağımsız iki örnek t-testi:** İki bağımsız grubun ortalaması karşılaştırılır.
    
- **Eşleştirilmiş t-testi:** Aynı bireylerin iki durumu karşılaştırılır (öncesi-sonrası gibi).
    

### Python Örneği:

```Python
from scipy import stats
import numpy as np

# Bağımsız iki örnek t-testi
group1 = np.random.normal(10, 2, 30)
group2 = np.random.normal(12, 2, 30)

t_stat, p_value = stats.ttest_ind(group1, group2)
print(f"t-statistic: {t_stat}, p-value: {p_value}")

# Eşleştirilmiş t-testi
before = np.random.normal(10, 2, 30)
after = before + np.random.normal(1, 1, 30)

t_stat_paired, p_value_paired = stats.ttest_rel(before, after)
print(f"Paired t-test t-statistic: {t_stat_paired}, p-value: {p_value_paired}")

```

---

## 2. **ANOVA (Analysis of Variance)**

### Kullanımı:

Üç veya daha fazla grubun ortalamalarının birbirinden farklı olup olmadığını test eder.

### Varsayımlar:

- Her grubun verisi normal dağılıma uygun.
    
- Varyanslar homojen.
    
- Gözlemler bağımsız.
    

### Python Örneği:

```Python
group1 = np.random.normal(10, 2, 30)
group2 = np.random.normal(12, 2, 30)
group3 = np.random.normal(11, 2, 30)

f_stat, p_value = stats.f_oneway(group1, group2, group3)
print(f"F-statistic: {f_stat}, p-value: {p_value}")
```

---

## 3. **Ki-Kare Testi (Chi-Square Test)**

### Kullanımı:

- Kategorik değişkenlerin bağımsızlığını test etmek için (Ki-Kare Bağımsızlık Testi).
    
- Gözlenen ve beklenen frekanslar arasındaki uyumu test etmek için (Ki-Kare Uyum Testi).
    

### Varsayımlar:

- Gözlem sayısı yeterince büyük olmalı (beklenen frekanslar genellikle 5'in üzerinde).
    
- Veriler kategorik olmalı.
    

### Python Örneği:

```Python
# Bağımsızlık testi için çapraz tablo
observed = np.array([[20, 15], [30, 35]])
chi2_stat, p_value, dof, expected = stats.chi2_contingency(observed)

print(f"Chi2 Statistic: {chi2_stat}, p-value: {p_value}")
print(f"Expected frequencies:\n{expected}")
```

---

## 4. **Mann-Whitney U Testi (Non-parametrik t-Testi)**

### Kullanımı:

İki bağımsız grubun medyanları arasındaki farkı test eder, ancak normal dağılım varsayımı yoktur.

### Varsayımlar:

- Bağımsız örnekler.
    
- Sıralanabilir veriler.
    

### Python Örneği:

```Python
group1 = np.random.randint(1, 100, 30)
group2 = np.random.randint(50, 150, 30)

u_stat, p_value = stats.mannwhitneyu(group1, group2)
print(f"U Statistic: {u_stat}, p-value: {p_value}")
```
---

## 5. **Wilcoxon İşaretli Sıralar Testi**

### Kullanımı:

Eşleştirilmiş iki grup için, non-parametrik ortalama fark testi.

### Varsayımlar:

- Eşleştirilmiş (bağımlı) veriler.
    
- Veriler sıralanabilir.
    

### Python Örneği:

```Python
before = np.random.randint(1, 100, 30)
after = before + np.random.randint(-10, 10, 30)

w_stat, p_value = stats.wilcoxon(before, after)
print(f"Wilcoxon Statistic: {w_stat}, p-value: {p_value}")
```

---

## 6. **Shapiro-Wilk Testi**

### Kullanımı:

Bir örneklemin normal dağılıp dağılmadığını test etmek için.

### Varsayımlar:

- Tek değişken.
    

### Python Örneği:

```Python
data = np.random.normal(0, 1, 100)
stat, p_value = stats.shapiro(data)
print(f"Shapiro-Wilk Statistic: {stat}, p-value: {p_value}")
```

---

## 7. **Levene Testi**

### Kullanımı:

Gruplar arası varyansların eşit olup olmadığını test etmek için (varyans homojenliği testi).

### Varsayımlar:

- Bağımsız gruplar.
    

### Python Örneği:

```Python
group1 = np.random.normal(10, 2, 30)
group2 = np.random.normal(12, 5, 30)

stat, p_value = stats.levene(group1, group2)
print(f"Levene Statistic: {stat}, p-value: {p_value}")
```

## 8. **Korelasyon Testleri**

### a) **Pearson Korelasyon Testi**

- **Kullanımı:** İki sürekli değişken arasındaki doğrusal ilişkinin gücünü ve anlamlılığını test eder.
    
- **Varsayımlar:** Normal dağılım, doğrusal ilişki.
    
```Python
x = np.random.normal(0, 1, 100)
y = 2 * x + np.random.normal(0, 1, 100)

r, p_value = stats.pearsonr(x, y)
print(f"Pearson r: {r}, p-value: {p_value}")
```

### b) **Spearman Korelasyon Testi**

- **Kullanımı:** İki sıralı (ordinal) veya sürekli değişken arasındaki monoton ilişkiyi test eder, normal dağılım gerektirmez.
    
```Python
r_s, p_value = stats.spearmanr(x, y)
print(f"Spearman rho: {r_s}, p-value: {p_value}")

```

---

## 9. **Kruskal-Wallis Testi**

- **Kullanımı:** Üç veya daha fazla bağımsız grubun medyanlarının birbirinden farklı olup olmadığını test eder (non-parametrik ANOVA).
    
- **Varsayımlar:** Bağımsız gruplar, sıralanabilir veri.
    
```Python
group1 = np.random.randint(1, 100, 30)
group2 = np.random.randint(50, 150, 30)
group3 = np.random.randint(30, 120, 30)

h_stat, p_value = stats.kruskal(group1, group2, group3)
print(f"Kruskal-Wallis H: {h_stat}, p-value: {p_value}")
```

---

## 10. **Fisher’in Exact Testi**

- **Kullanımı:** Küçük örneklerde (özellikle 2x2 kontenjans tabloları) Ki-Kare testine alternatif olarak kullanılır.
    
- **Varsayımlar:** Kategorik veri, küçük örneklem.
    

```Python
from scipy.stats import fisher_exact

table = np.array([[8, 2], [1, 5]])
oddsratio, p_value = fisher_exact(table)
print(f"Odds Ratio: {oddsratio}, p-value: {p_value}")
```

---

## 11. **Kolmogorov-Smirnov Testi**

- **Kullanımı:** Bir örneklemin belirli bir dağılıma uygunluğunu test etmek için (örneğin normal dağılım).
    
- **Varsayımlar:** Sürekli veri.
    
```Python
stat, p_value = stats.kstest(np.random.normal(0, 1, 100), 'norm')
print(f"KS Statistic: {stat}, p-value: {p_value}")
```

---

## 12. **Bartlett Testi**

- **Kullanımı:** Grupların varyanslarının eşit olup olmadığını test etmek için (daha güçlü ama normal dağılım varsayımı gerekiyor).
    

```Python
stat, p_value = stats.bartlett(group1, group2, group3)
print(f"Bartlett Statistic: {stat}, p-value: {p_value}")
```

---

## 13. **Durbin-Watson Testi**

- **Kullanımı:** Regresyon analizinde, hata terimlerinin otokorelasyonunu (özellikle ardışık) test eder.
    

Python’da `statsmodels` kullanılır:

```Python
import statsmodels.api as sm
from statsmodels.stats.stattools import durbin_watson

X = sm.add_constant(x)
model = sm.OLS(y, X).fit()
dw_stat = durbin_watson(model.resid)
print(f"Durbin-Watson statistic: {dw_stat}")
```

---

## 14. **Z-Testi**

- **Kullanımı:** Büyük örneklem büyüklüğünde iki ortalamayı karşılaştırmak için (varyanslar biliniyorsa).
    

Python’da genellikle `statsmodels` kullanılır:

```Python
from statsmodels.stats.weightstats import ztest

data1 = np.random.normal(10, 2, 100)
data2 = np.random.normal(12, 2, 100)

z_stat, p_value = ztest(data1, data2)
print(f"Z-statistic: {z_stat}, p-value: {p_value}")
```
