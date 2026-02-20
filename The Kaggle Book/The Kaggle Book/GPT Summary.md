# 📘 BÖLÜM 1 – Kaggle ve Veri Bilimi Yarışmalarına Giriş

Bu bölüm teknik modelleme öğretmez.
Ekosistemi, tarihi, mantığı ve yarışma dinamiklerini öğretir.

Ana soru:

> Veri bilimi yarışmaları neden ortaya çıktı ve neden işe yarıyor?

---

## 1️⃣ Veri Bilimi Yarışmalarının Tarihçesi

### 1.1 Competitive Programming Kökeni

Modern veri yarışmalarının atası:

* International Collegiate Programming Contest

Bu yarışmalar:

* Algoritma temelli
* Hız odaklı
* Matematiksel problem çözmeye dayalı

Ancak:
Veri yoktu, sadece algoritma vardı.

---

### 1.2 Veri Madenciliği Yarışmaları

Dönüm noktası:

* KDD Cup

Bu yarışmalar:

* Gerçek veri kullandı
* Akademik benchmark oluşturdu
* Performans metriği kavramını getirdi

Burada önemli olan:

Model değil, ölçüm sistemiydi.

---

## 2️⃣ Crowdsourcing ve No Free Lunch Teoremi

### 2.1 No Free Lunch

* David Wolpert
* William Macready

Teorem:

Hiçbir algoritma tüm problemler için en iyi değildir.

Sonuç:

* Çok sayıda kişi aynı problemi çözmeli
* Farklı yaklaşımlar denenmeli
* Ensemble modeller oluşmalı

---

### 2.2 Basit Simülasyon (Telifsiz Kod)

Aynı veri üzerinde iki farklı model:

```python
import numpy as np
from sklearn.datasets import make_classification
from sklearn.ensemble import RandomForestClassifier
from sklearn.linear_model import LogisticRegression
from sklearn.metrics import roc_auc_score
from sklearn.model_selection import train_test_split

X, y = make_classification(
    n_samples=5000,
    n_features=20,
    random_state=42
)

X_train, X_val, y_train, y_val = train_test_split(
    X, y, test_size=0.3, random_state=42
)

rf = RandomForestClassifier()
lr = LogisticRegression(max_iter=1000)

rf.fit(X_train, y_train)
lr.fit(X_train, y_train)

rf_score = roc_auc_score(y_val, rf.predict_proba(X_val)[:,1])
lr_score = roc_auc_score(y_val, lr.predict_proba(X_val)[:,1])

print("RandomForest AUC:", rf_score)
print("LogisticRegression AUC:", lr_score)
```

Farklı problem → farklı kazanan model.

---

## 3️⃣ Netflix Prize – Büyük Kırılma

2006’da:

* Netflix

1 milyon dolar ödül koydu.

Amaç:
Cinematch algoritmasını %10 geliştirmek.

Sonuç:

* Matrix factorization yükseldi
* Takımlar birleşti
* Dev ensemble çözümler çıktı

Önemli ders:

Kazanan çözüm değil,
üretilen metodoloji değerliydi.

---

## 4️⃣ Kaggle’ın Doğuşu

Kurucu:

* Anthony Goldbloom

Amaç:

Netflix modelini sürekli hale getirmek.

2010 → Kuruluş
2017 → Google satın aldı

Sonrası:

* Ücretsiz GPU
* Notebooks
* Dataset paylaşımı
* Küresel topluluk

---

## 5️⃣ Kaggle Yarışma Yapısı

Bir yarışma şu bileşenlerden oluşur:

1. Problem tanımı
2. Train dataset
3. Test dataset
4. Evaluation metric
5. Leaderboard
6. Submission sistemi

---

## 6️⃣ Evaluation Metric (Değerlendirme Metriği)

Her yarışmada bir metrik vardır:

* AUC
* LogLoss
* RMSE
* MAE
* F1

### 6.1 Basit AUC Hesaplama

```python
from sklearn.metrics import roc_auc_score

preds = rf.predict_proba(X_val)[:,1]
auc = roc_auc_score(y_val, preds)
print("AUC:", auc)
```

Metriği bilmeden model kurmak hatadır.

---

## 7️⃣ Leaderboard Mekanizması

Test verisi ikiye bölünür:

* Public Leaderboard
* Private Leaderboard

Risk:

Public’e optimize etmek → private çöküş

Buna denir:

**Leaderboard Overfitting**

---

### 7.1 Simülasyon

```python
import numpy as np

public_scores = np.random.normal(0.80, 0.01, 50)
private_scores = public_scores - np.random.normal(0.02, 0.01, 50)

print("Public mean:", public_scores.mean())
print("Private mean:", private_scores.mean())
```

Public yüksek görünebilir ama private düşebilir.

---

## 8️⃣ Submission Mekanizması

Yarışmalarda prediction dosyası şu formatta yüklenir:

```python
import pandas as pd

submission = pd.DataFrame({
    "id": range(len(preds)),
    "target": preds
})

submission.to_csv("submission.csv", index=False)
```

Bu dosya sisteme yüklenir → skor hesaplanır.

---

## 9️⃣ Kaggle Yarışma Türleri

* Featured
* Research
* Getting Started
* Playground
* Masters
* Recruitment

Ama hepsinin temel mantığı aynıdır:

Metric + Leaderboard.

---

## 🔟 Kaggle’ın Kariyer Etkisi

Kaggle:

* Portföy oluşturur
* Networking sağlar
* İş fırsatı üretir
* Pratik hız kazandırır

Kaggle kökenli girişimler:

* DataRobot
* fast.ai

---

## 1️⃣1️⃣ Kaggle Ekosistem Bileşenleri

Kaggle sadece yarışma değildir:

* Datasets
* Notebooks
* Discussion
* Kaggle Learn
* Ranking sistemi

Bu yapı sosyal öğrenme ortamıdır.

---

## 1️⃣2️⃣ Stratejik Dersler

Bölüm 1’in verdiği stratejik mesajlar:

✔ Model tek başına yetmez
✔ Validation kritiktir
✔ Deney kültürü gereklidir
✔ Topluluk öğrenmesi avantaj sağlar
✔ Leaderboard’a körü körüne güvenilmez

# 📘 BÖLÜM 2 – Kaggle Datasets ile Veriyi Organize Etmek

Bu bölüm model kurmaktan önce gelen en kritik aşamayı öğretir:

> Veri organizasyonu, versiyonlama ve paylaşım disiplini.

Ana mesaj:

Model geçici avantaj sağlar.
İyi organize edilmiş veri kalıcı avantaj sağlar.

---

## 1️⃣ Kaggle Datasets Nedir?

Kaggle Datasets:

* Veri yükleme sistemi
* Versiyon kontrolü
* Public / private paylaşım
* Notebook entegrasyonu
* API erişimi

Kaggle yalnızca yarışma platformu değildir — aynı zamanda veri barındırma ve paylaşma altyapısıdır.

---

## 2️⃣ Dataset Türleri

Kaggle’da temel veri formatları:

### 2.1 Tabular Veri

* CSV
* Parquet
* Feather

### 2.2 Dosya Tabanlı Veri

* Görseller (image klasörleri)
* Ses dosyaları
* Video
* JSON
* ZIP arşivleri

---

## 3️⃣ Dataset Hazırlama Süreci

Bir dataset yüklemeden önce yapılması gerekenler:

1. Temizleme
2. Formatlama
3. Dokümantasyon
4. Lisans belirleme
5. Versiyonlama

---

## 4️⃣ Ham Veriyi Temizleme (Telifsiz Kod)

### 4.1 Veri Yükleme

```python
import pandas as pd

raw = pd.read_csv("raw_data.csv")
print(raw.shape)
raw.head()
```

---

### 4.2 Eksik Değer Analizi

```python
raw.isnull().sum().sort_values(ascending=False)
```

Eksik doldurma:

```python
cleaned = raw.copy()

cleaned["age"] = cleaned["age"].fillna(cleaned["age"].median())
cleaned["income"] = cleaned["income"].fillna(0)
```

---

### 4.3 Duplicate Temizleme

```python
cleaned = cleaned.drop_duplicates()
```

---

### 4.4 Veri Tipi Düzeltme

```python
cleaned["date"] = pd.to_datetime(cleaned["date"])
cleaned["category"] = cleaned["category"].astype("category")
```

---

### 4.5 Outlier İncelemesi

```python
import seaborn as sns
import matplotlib.pyplot as plt

sns.boxplot(x=cleaned["income"])
plt.show()
```

---

## 5️⃣ Feature Engineering ve Standardizasyon

```python
from sklearn.preprocessing import StandardScaler
import numpy as np

scaler = StandardScaler()

cleaned["income_scaled"] = scaler.fit_transform(
    cleaned[["income"]]
)

cleaned["income_log"] = np.log1p(cleaned["income"])
```

---

## 6️⃣ Dataset Kaydetme

```python
cleaned.to_csv("processed_dataset_v1.csv", index=False)
```

Bu dosya artık Kaggle’a yüklenmeye hazır.

---

## 7️⃣ Metadata ve Dokümantasyon

İyi bir dataset şunları içerir:

* Veri kaynağı
* Toplanma yöntemi
* Tarih aralığı
* Feature açıklamaları
* Ön işleme adımları
* Olası bias
* Lisans bilgisi

Bu bölüm şunu vurgular:

> Dokümantasyon, dataset’in teknik kalitesi kadar önemlidir.

---

## 8️⃣ Versiyonlama Mantığı

Her güncelleme yeni bir versiyon oluşturur.

### 8.1 Versiyon Simülasyonu

```python
v1 = cleaned.copy()

v2 = v1.copy()
v2["income_squared"] = v2["income"] ** 2

v2.to_csv("processed_dataset_v2.csv", index=False)
```

Avantaj:

* Eski sonuçlar kaybolmaz
* Deneyler karşılaştırılabilir
* Reproducibility sağlanır

---

## 9️⃣ Kaggle Notebook’ta Dataset Kullanımı

Kaggle ortamında klasör yapısı:

```
/kaggle/input/
/kaggle/working/
/kaggle/temp/
```

Dataset listeleme:

```python
import os

for dirname, _, filenames in os.walk("/kaggle/input"):
    for filename in filenames:
        print(os.path.join(dirname, filename))
```

Dataset okuma:

```python
data = pd.read_csv("/kaggle/input/my-dataset/processed_dataset_v2.csv")
```

---

## 🔟 Kaggle API Kullanımı

API ile dataset indirme:

```python
!kaggle datasets download username/dataset-name
```

Zip açma:

```python
import zipfile

with zipfile.ZipFile("dataset-name.zip", "r") as zip_ref:
    zip_ref.extractall("data_folder")
```

Bu yöntem Kaggle dışı çalışma ortamları için gereklidir.

---

## 1️⃣1️⃣ Veri Kalitesi ve Riskler

Bölüm şu riskleri vurgular:

* Missing values
* Duplicate kayıtlar
* Data leakage
* Distribution shift
* Yanlış tip dönüşümü
* Hatalı encoding

---

## 1️⃣2️⃣ Data Leakage Örneği

Yanlış yaklaşım:

```python
# Tüm veri üzerinden ortalama hesaplamak (yanlış)
mean_value = cleaned["income"].mean()
```

Doğru yaklaşım:

Sadece train üzerinde hesaplanmalı.

---

## 1️⃣3️⃣ Bias ve Etik

Veri:

* Toplumsal bias içerebilir
* Cinsiyet, ırk, gelir dengesizliği barındırabilir

Anonimleştirme örneği:

```python
cleaned["user_id"] = cleaned["user_id"].astype(str).apply(lambda x: hash(x))
```

GDPR gibi regülasyonlara dikkat edilmelidir.

---

## 1️⃣4️⃣ Lisans Seçimi

Dataset yüklerken lisans seçilir:

* Açık kullanım
* Attribution gerekli
* Ticari kullanım sınırlı

Yanlış lisans:

→ Hukuki risk
→ Dataset kaldırılması

---

## 1️⃣5️⃣ Büyük Dataset Optimizasyonu

Büyük veri için:

* CSV yerine Parquet
* Sıkıştırma
* Gereksiz kolon silme

Parquet kaydetme:

```python
cleaned.to_parquet("processed_dataset_v2.parquet")
```

Parquet avantajı:

* Daha hızlı okuma
* Daha az disk kullanımı

---

## 1️⃣6️⃣ Dataset’i Portföy Aracı Yapmak

Stratejik avantaj:

İyi dataset:

* Notebook’larda referans olur
* Arama motorlarında çıkar
* CV’ye eklenir
* Alan uzmanlığını gösterir

Bu bölüm, dataset yayınlamayı kariyer yatırımı olarak konumlandırır.

---

# 🎯 Bölüm 2’nin Ana Mesajı

Veri bilimi:

Model yazmak değildir.

Veri bilimi:

✔ Veri toplamak
✔ Temizlemek
✔ Versiyonlamak
✔ Dokümante etmek
✔ Hukuki sorumluluk almak

üzerine kuruludur.

# 📘 BÖLÜM 3 – Kaggle Notebooks ile Çalışmak ve Öğrenmek

Bu bölümün ana mesajı:

> Kaggle’da öğrenme pasif değildir. Notebook yazarak öğrenilir.

Notebook sadece kod yazma alanı değildir.
Deney ortamı + paylaşım platformu + portföy aracıdır.

---

## 1️⃣ Kaggle Notebook Nedir?

Kaggle Notebook:

* Cloud tabanlı Jupyter ortamıdır
* Python ve R destekler
* GPU / TPU erişimi sunar
* Dataset’lere doğrudan bağlıdır
* Ücretsizdir

Google satın alımı sonrası:

* Daha güçlü runtime
* Daha fazla RAM
* GPU erişimi

gibi imkanlar sunulmuştur.

---

## 2️⃣ Notebook Ortam Yapısı

Kaggle klasör yapısı:

```
/kaggle/input/      # Dataset (read-only)
/kaggle/working/    # Çıktılar
/kaggle/temp/       # Geçici dosyalar
```

---

### 2.1 Dataset Listeleme

```python
import os

for dirname, _, filenames in os.walk("/kaggle/input"):
    for filename in filenames:
        print(os.path.join(dirname, filename))
```

Bu, hangi dataset’in bağlı olduğunu gösterir.

---

## 3️⃣ Notebook Bileşenleri

Notebook üç temel bloktan oluşur:

1. Code cells
2. Markdown cells
3. Output cells

Bölümün önemli vurgusu:

> Notebook anlatıdır. Sadece kod değildir.

---

## 4️⃣ Reproducibility (Tekrarlanabilirlik)

En kritik alışkanlık:

Random seed sabitlemek.

```python
import random
import numpy as np
import os

def set_seed(seed=42):
    random.seed(seed)
    np.random.seed(seed)
    os.environ["PYTHONHASHSEED"] = str(seed)

set_seed(42)
```

Seed olmadan:

* CV tutarsız olur
* Sonuçlar değişir
* Leaderboard ile korelasyon bozulur

---

## 5️⃣ Runtime Ayarları

Notebook ayarlarında:

* CPU / GPU seçimi
* Internet erişimi
* RAM bilgisi

GPU gerekli mi?

* Deep Learning → Evet
* Tabular GBDT → Genelde hayır

---

## 6️⃣ Basit Yarışma Notebook Akışı (Tam Pipeline)

### 6.1 Kütüphaneler

```python
import pandas as pd
import numpy as np
from sklearn.model_selection import StratifiedKFold
from sklearn.metrics import roc_auc_score
from sklearn.ensemble import RandomForestClassifier
```

---

### 6.2 Dataset Yükleme

```python
train = pd.read_csv("/kaggle/input/example/train.csv")
test = pd.read_csv("/kaggle/input/example/test.csv")
```

---

### 6.3 Feature / Target Ayrımı

```python
TARGET = "target"
ID = "id"

features = [col for col in train.columns if col not in [TARGET, ID]]

X = train[features]
y = train[TARGET]
X_test = test[features]
```

---

### 6.4 Cross Validation

```python
skf = StratifiedKFold(n_splits=5, shuffle=True, random_state=42)

oof = np.zeros(len(train))
test_preds = np.zeros(len(test))

for fold, (train_idx, val_idx) in enumerate(skf.split(X, y)):
    
    X_tr, X_val = X.iloc[train_idx], X.iloc[val_idx]
    y_tr, y_val = y.iloc[train_idx], y.iloc[val_idx]

    model = RandomForestClassifier(
        n_estimators=300,
        random_state=42,
        n_jobs=-1
    )

    model.fit(X_tr, y_tr)

    val_preds = model.predict_proba(X_val)[:,1]
    oof[val_idx] = val_preds

    fold_score = roc_auc_score(y_val, val_preds)
    print(f"Fold {fold+1} AUC:", fold_score)

    test_preds += model.predict_proba(X_test)[:,1] / skf.n_splits

print("OOF AUC:", roc_auc_score(y, oof))
```

---

### 6.5 Submission

```python
submission = pd.DataFrame({
    ID: test[ID],
    TARGET: test_preds
})

submission.to_csv("/kaggle/working/submission.csv", index=False)
```

---

## 7️⃣ Notebook Türleri

### 7.1 Yarışma Notebook’u

* Hızlı
* Minimal açıklama
* Skor odaklı

### 7.2 Öğretici Notebook

* Bol EDA
* Grafik
* Açıklama
* Topluluk oy alır

---

## 8️⃣ Notebook’tan Öğrenme

Önerilen strateji:

* En çok upvote alan notebook’ları incele
* Kazanan çözümleri analiz et
* Feature engineering fikirlerini not al
* Validation stratejisini çöz

---

## 9️⃣ Notebook → GitHub Export

Notebook:

* Script olarak indirilebilir
* HTML export edilebilir
* GitHub’a yüklenebilir

Bu, portföy oluşturur.

---

## 🔟 Production vs Notebook

Notebook:

✔ Deney ortamı
❌ Production kodu değildir

Production’da:

* Modüler kod
* Fonksiyon yapısı
* Testler
* Logging

gereklidir.

---

## 1️⃣1️⃣ Notebook Performans Optimizasyonu

### 11.1 Bellek Azaltma

```python
def reduce_memory(df):
    for col in df.columns:
        if df[col].dtype == "float64":
            df[col] = df[col].astype("float32")
        elif df[col].dtype == "int64":
            df[col] = df[col].astype("int32")
    return df

train = reduce_memory(train)
```

---

### 11.2 Parquet Kullanımı

```python
train.to_parquet("/kaggle/working/train.parquet")
```

---

## 1️⃣2️⃣ Topluluk ve Networking

Notebook paylaşımı:

* Upvote alır
* Takipçi kazandırır
* İş fırsatı yaratır

---

# 🎯 Bölüm 3’ün Ana Mesajı

Notebook:

* Deney alanıdır
* Öğrenme aracıdır
* Portföy ürünüdür
* Networking kapısıdır

Kazananlar:

* En iyi kodu yazan değil
* En iyi deney sistemini kurandır

# 📘 BÖLÜM 4 – Kaggle Discussion Forums (Tartışma Forumları)

Bu bölümün ana mesajı:

> Kaggle’da tek başına yarışılmaz.
> En büyük avantaj bilgi paylaşımıdır.

Forumlar:

* Öğrenme alanıdır
* Strateji alanıdır
* Networking alanıdır
* Bilgi savaşı alanıdır

---

## 1️⃣ Kaggle Discussion Nedir?

Discussion bölümü:

* Her yarışmaya özel forum içerir
* Genel Kaggle forumu vardır
* Dataset ve Notebook forumları vardır

Amaç:

* Soru sormak
* İpucu paylaşmak
* Hata tartışmak
* Fikir üretmek

---

## 2️⃣ Discussion Türleri

Yarışma forumlarında tipik başlıklar:

### 2.1 Clarification (Açıklama)

* Problem tanımı net mi?
* Veri anlamı nedir?
* Hangi external data serbest?

---

### 2.2 EDA Paylaşımları

* Veri keşfi
* İlginç pattern’ler
* Dağılım analizleri

---

### 2.3 Baseline Modeller

* Basit başlangıç modeli
* İlk submission örneği

---

### 2.4 Strategy Discussion

* CV nasıl kurulmalı?
* Leakage var mı?
* Time split gerekli mi?

---

### 2.5 Post-Competition Writeups

Yarışma bittikten sonra:

* Kazanan çözümler
* Feature engineering teknikleri
* Ensemble stratejileri

En değerli içerik buradadır.

---

## 3️⃣ Forumların Stratejik Önemi

Forumlar:

✔ Hızlı öğrenme sağlar
✔ Zaman kazandırır
✔ Hataları erken gösterir
✔ Topluluk içgörüsü üretir

Ama dikkat:

> Her ipucu doğru değildir.

---

## 4️⃣ Bilgi Asimetrisi

Yarışmalarda bilgi dağılımı eşit değildir.

Bazı yarışmacılar:

* Domain uzmanıdır
* Veri kaynağını bilir
* İleri tekniklere sahiptir

Forumlar bu asimetrinin azalmasını sağlar.

---

## 5️⃣ Forumdan Teknik Öğrenme Örneği

### Örnek: Bir kullanıcı target leakage fark etti.

Simülasyon:

Yanlış feature:

```python
train["future_value"] = train["target"].shift(-1)
```

Bu leakage üretir.

Forum uyarısı sonrası doğru yaklaşım:

```python
train["future_value"] = train.groupby("user_id")["value"].shift(1)
```

Bu geçmiş bilgi kullanır.

Forumlar leakage’i ortaya çıkarabilir.

---

## 6️⃣ Forumdan Gelen EDA İpucu Simülasyonu

Diyelim forumda biri şunu yazdı:

> Feature X ile target arasında güçlü korelasyon var.

Kontrol edelim:

```python
import pandas as pd
import seaborn as sns
import matplotlib.pyplot as plt

corr = train.corr()

sns.heatmap(corr[["target"]].sort_values(by="target", ascending=False), annot=True)
plt.show()
```

Bu tür analizler forum tartışmalarında sık görülür.

---

## 7️⃣ Baseline Paylaşmanın Önemi

Forumda baseline paylaşmak:

✔ Topluluğa katkı
✔ Upvote kazanma
✔ Tanınma
✔ İş fırsatı

Örnek minimal baseline:

```python
from sklearn.dummy import DummyClassifier

dummy = DummyClassifier(strategy="most_frequent")
dummy.fit(X, y)

preds = dummy.predict_proba(X_test)[:,1]
```

Baseline paylaşımı yarışmanın çıtasını belirler.

---

## 8️⃣ Forum Etiği

Yazılı olmayan kurallar:

* Spoiler vermemek
* Özel private data paylaşmamak
* Kuralları ihlal etmemek
* Saygılı olmak

---

## 9️⃣ Post-Competition Writeups

Yarışma bittikten sonra:

Kazananlar genelde:

* Validation stratejisini açıklar
* Feature engineering tekniklerini listeler
* Model stacking yapısını anlatır

Örnek stacking simülasyonu:

```python
from sklearn.linear_model import LogisticRegression

meta_model = LogisticRegression()

meta_model.fit(
    np.column_stack([model1_oof, model2_oof]),
    y
)

final_preds = meta_model.predict_proba(
    np.column_stack([model1_test, model2_test])
)[:,1]
```

Writeup’lar en büyük öğrenme kaynağıdır.

---

## 🔟 Forum Psikolojisi

Leaderboard sıralaması değiştikçe:

* Moral dalgalanır
* Spekülasyon artar
* “LB shakeup” beklentisi oluşur

Forumlar bu psikolojik baskıyı da yansıtır.

---

## 1️⃣1️⃣ Networking ve Görünürlük

Forumda aktif olmak:

* Takipçi kazandırır
* Takım daveti almanı sağlar
* İş teklifi getirebilir

Birçok Kaggle Grandmaster, forum katkılarıyla tanınmıştır.

---

## 1️⃣2️⃣ Forumdan Gelen Stratejik Avantaj

Forum okuma stratejisi:

1. İlk gün clarifications oku
2. İlk hafta EDA paylaşımlarını takip et
3. Orta aşamada CV tartışmalarını analiz et
4. Son hafta psikolojik manipülasyona kapılma

---

## 1️⃣3️⃣ Forum Riskleri

* Yanlış yönlendirme
* Bilinçli bilgi saklama
* Overhype edilmiş teknikler
* Public LB manipülasyonu

Forum bilgisi:

> Filtrelenerek kullanılmalıdır.

---

# 🎯 Bölüm 4’ün Ana Mesajı

Kaggle:

Bireysel zeka yarışması değildir.

Topluluk öğrenmesi yarışmasıdır.

Forumlar:

✔ Hızlı öğrenme sağlar
✔ Stratejik içgörü üretir
✔ Networking oluşturur

Ama:

Kendi validation sistemin yoksa
forum da seni kurtaramaz.

# 📘 BÖLÜM 5 – Validation (Doğrulama)

Bu bölüm kitabın en kritik teknik bölümüdür.

Ana mesaj:

> Leaderboard’a güvenme.
> Kendi validation sistemini kur.

Kaggle’da başarı:

%60 Validation kalitesi
%40 Model kalitesi

---

## 1️⃣ Validation Nedir?

Validation:

Modelin gerçek test performansını tahmin etmeye yarayan sistemdir.

Amaç:

* Overfitting’i önlemek
* Leaderboard overfitting’i engellemek
* Model seçimini doğru yapmak

---

## 2️⃣ Public vs Private Leaderboard

Kaggle’da test verisi ikiye bölünür:

* Public LB
* Private LB

Tehlike:

Public’e optimize etmek → private düşüş

Buna denir:

**Leaderboard Overfitting**

---

### 2.1 Simülasyon

```python
import numpy as np

np.random.seed(42)

public_scores = np.random.normal(0.82, 0.01, 30)
private_scores = public_scores - np.random.normal(0.02, 0.01, 30)

print("Public Ortalama:", public_scores.mean())
print("Private Ortalama:", private_scores.mean())
```

Public yüksek görünebilir ama private düşebilir.

---

## 3️⃣ Basit Hold-Out Validation

Veriyi ikiye bölmek:

```python
from sklearn.model_selection import train_test_split

X_train, X_val, y_train, y_val = train_test_split(
    X, y,
    test_size=0.2,
    random_state=42
)
```

Model:

```python
from sklearn.ensemble import RandomForestClassifier
from sklearn.metrics import roc_auc_score

model = RandomForestClassifier(random_state=42)
model.fit(X_train, y_train)

val_preds = model.predict_proba(X_val)[:,1]
print("Validation AUC:", roc_auc_score(y_val, val_preds))
```

Sorun:

* Veri küçükse güvenilmez
* Rastgele bölme leakage yaratabilir

---

## 4️⃣ K-Fold Cross Validation

En yaygın yöntem:

```python
from sklearn.model_selection import KFold
import numpy as np

kf = KFold(n_splits=5, shuffle=True, random_state=42)

oof = np.zeros(len(X))

for fold, (train_idx, val_idx) in enumerate(kf.split(X)):
    
    X_tr, X_val = X.iloc[train_idx], X.iloc[val_idx]
    y_tr, y_val = y.iloc[train_idx], y.iloc[val_idx]

    model = RandomForestClassifier(random_state=42)
    model.fit(X_tr, y_tr)

    preds = model.predict_proba(X_val)[:,1]
    oof[val_idx] = preds

    print(f"Fold {fold+1} AUC:",
          roc_auc_score(y_val, preds))

print("OOF AUC:", roc_auc_score(y, oof))
```

OOF (Out Of Fold):

Gerçek test performansına en yakın tahmindir.

---

## 5️⃣ Stratified K-Fold

Dengesiz sınıf varsa:

```python
from sklearn.model_selection import StratifiedKFold

skf = StratifiedKFold(
    n_splits=5,
    shuffle=True,
    random_state=42
)
```

Avantaj:

* Target dağılımı her fold’da korunur
* Daha stabil skor

---

## 6️⃣ Group K-Fold (Leakage Önleme)

Aynı kullanıcı birden fazla satırdaysa:

```python
from sklearn.model_selection import GroupKFold

gkf = GroupKFold(n_splits=5)

for train_idx, val_idx in gkf.split(X, y, groups=train["user_id"]):
    ...
```

Bu:

✔ Aynı kullanıcıyı farklı fold’lara dağıtmaz
✔ Leakage’i önler

---

## 7️⃣ Time Series Validation

Zamana bağlı veri varsa:

Yanlış:

Random split

Doğru:

```python
from sklearn.model_selection import TimeSeriesSplit

tscv = TimeSeriesSplit(n_splits=5)

for train_idx, val_idx in tscv.split(X):
    ...
```

Mantık:

Geçmiş → Geleceği tahmin eder.

---

## 8️⃣ Data Leakage

Validation’ı bozan en büyük düşman.

### 8.1 Leakage Örneği

Yanlış:

```python
X["target_mean"] = y.mean()
```

Bu, validation bilgisini train’e sızdırır.

Doğru:

Fold içinde hesaplanmalı.

---

## 9️⃣ Adversarial Validation

Amaç:

Train ve test dağılımları farklı mı?

```python
train["is_test"] = 0
test["is_test"] = 1

combined = pd.concat([train, test])

X_adv = combined[features]
y_adv = combined["is_test"]

model = RandomForestClassifier()
model.fit(X_adv, y_adv)

preds = model.predict_proba(X_adv)[:,1]

from sklearn.metrics import roc_auc_score
print("Adversarial AUC:",
      roc_auc_score(y_adv, preds))
```

Eğer AUC yüksekse:

→ Train ve test farklı dağılımda.

---

## 🔟 Fold Variance Analizi

Fold skorları çok farklıysa:

→ Validation zayıf.

Basit ölçüm:

```python
fold_scores = [0.82, 0.79, 0.83, 0.81, 0.80]
print("Std:", np.std(fold_scores))
```

Yüksek std = risk.

---

## 1️⃣1️⃣ Seed Sensitivity

Tek seed risklidir.

```python
seeds = [42, 2023, 777]
final_preds = np.zeros(len(X_test))

for seed in seeds:
    model = RandomForestClassifier(random_state=seed)
    model.fit(X, y)
    final_preds += model.predict_proba(X_test)[:,1] / len(seeds)
```

Seed averaging:

✔ Variance azaltır
✔ Daha stabil sonuç

---

## 1️⃣2️⃣ Early Stopping (Boosting)

```python
import lightgbm as lgb

train_data = lgb.Dataset(X_tr, label=y_tr)
val_data = lgb.Dataset(X_val, label=y_val)

params = {
    "objective": "binary",
    "metric": "auc",
    "learning_rate": 0.05
}

model = lgb.train(
    params,
    train_data,
    valid_sets=[val_data],
    num_boost_round=5000,
    callbacks=[lgb.early_stopping(100)]
)
```

Early stopping:

✔ Overfitting’i azaltır
✔ En iyi iterasyonu seçer

---

## 1️⃣3️⃣ CV – LB Korelasyonu

İdeal durum:

OOF ≈ Public LB ≈ Private LB

Eğer:

OOF yüksek
Public düşük

→ Validation yanlış.

---

# 🎯 Bölüm 5’in Ana Mesajı

Kaggle’da model ikinci plandadır.

Asıl başarı:

✔ Doğru CV
✔ Leakage kontrolü
✔ Fold stabilitesi
✔ Distribution analizi

Validation zayıfsa:

En iyi model bile private’da çöker.

# 📘 BÖLÜM 6 – Feature Engineering

Bu bölümün ana mesajı:

> Kaggle’da çoğu yarışma modelle değil, feature engineering ile kazanılır.

Özellikle tabular yarışmalarda:

✔ Model seçimi önemli
✔ Ama feature engineering daha belirleyici

---

## 1️⃣ Feature Engineering Nedir?

Feature engineering:

Ham veriyi
model için daha anlamlı temsillere dönüştürme sürecidir.

Amaç:

* Sinyali güçlendirmek
* Gürültüyü azaltmak
* Modelin öğrenmesini kolaylaştırmak

---

## 2️⃣ Basit Feature Dönüşümleri

### 2.1 Log Dönüşümü

Sağa çarpık dağılımlar için:

```python
import numpy as np

train["income_log"] = np.log1p(train["income"])
test["income_log"] = np.log1p(test["income"])
```

Avantaj:

✔ Outlier etkisini azaltır
✔ Lineer modelleri iyileştirir

---

### 2.2 Polynomial Feature

```python
train["income_squared"] = train["income"] ** 2
train["income_age_interaction"] = train["income"] * train["age"]
```

Bu tür etkileşimler özellikle lineer modellerde etkilidir.

---

## 3️⃣ Kategorik Değişken Encoding

### 3.1 Label Encoding

```python
from sklearn.preprocessing import LabelEncoder

le = LabelEncoder()

train["city"] = le.fit_transform(train["city"])
test["city"] = le.transform(test["city"])
```

Risk:

Ordinal ilişki varsayar.

---

### 3.2 One-Hot Encoding

```python
train = pd.get_dummies(train, columns=["city"])
test = pd.get_dummies(test, columns=["city"])
```

Avantaj:

✔ Lineer modeller için uygun

Dezavantaj:

❌ Yüksek cardinality problem yaratır

---

## 4️⃣ Target Encoding (Kaggle’da Çok Popüler)

Ama dikkat:

Leakage riski vardır.

Yanlış:

```python
train["city_target_mean"] = train.groupby("city")["target"].transform("mean")
```

Bu leakage üretir.

---

### 4.1 Doğru Fold İçinde Target Encoding

```python
from sklearn.model_selection import StratifiedKFold
import numpy as np

skf = StratifiedKFold(n_splits=5, shuffle=True, random_state=42)

train["city_te"] = 0

for train_idx, val_idx in skf.split(train, train["target"]):
    
    fold_train = train.iloc[train_idx]
    fold_val = train.iloc[val_idx]
    
    means = fold_train.groupby("city")["target"].mean()
    
    train.loc[val_idx, "city_te"] = fold_val["city"].map(means)

train["city_te"].fillna(train["target"].mean(), inplace=True)
```

Bu yöntem leakage’i önler.

---

## 5️⃣ Aggregation Features

Özellikle transaction verilerinde:

```python
agg = train.groupby("user_id")["amount"].agg(["mean", "sum", "count"])
agg.columns = ["user_mean", "user_sum", "user_count"]

train = train.merge(agg, on="user_id", how="left")
```

Bu tür agregasyonlar yarışmalarda çok güçlüdür.

---

## 6️⃣ Time-Based Feature Engineering

Zaman serilerinde:

```python
train["date"] = pd.to_datetime(train["date"])

train["year"] = train["date"].dt.year
train["month"] = train["date"].dt.month
train["dayofweek"] = train["date"].dt.dayofweek
```

Lag feature:

```python
train["lag_1"] = train.groupby("user_id")["amount"].shift(1)
```

Rolling mean:

```python
train["rolling_mean_3"] = (
    train.groupby("user_id")["amount"]
    .rolling(3)
    .mean()
    .reset_index(0, drop=True)
)
```

---

## 7️⃣ Text Feature Engineering

Basit örnek:

```python
train["text_length"] = train["text"].apply(len)
train["word_count"] = train["text"].apply(lambda x: len(x.split()))
```

TF-IDF:

```python
from sklearn.feature_extraction.text import TfidfVectorizer

tfidf = TfidfVectorizer(max_features=1000)

X_text = tfidf.fit_transform(train["text"])
```

---

## 8️⃣ Image Feature Engineering (Temel)

```python
from PIL import Image
import numpy as np

img = Image.open("sample.jpg")
img_array = np.array(img)

mean_pixel = img_array.mean()
```

Kaggle’da genelde:

* Pretrained CNN
* Transfer learning

kullanılır.

---

## 9️⃣ Feature Selection

Çok fazla feature → overfitting riski.

### 9.1 Feature Importance

```python
model.fit(X, y)

importances = model.feature_importances_

feat_imp = pd.DataFrame({
    "feature": X.columns,
    "importance": importances
}).sort_values(by="importance", ascending=False)

feat_imp.head(10)
```

---

### 9.2 Correlation Filtering

```python
corr_matrix = train.corr().abs()

upper = corr_matrix.where(
    np.triu(np.ones(corr_matrix.shape), k=1).astype(bool)
)

to_drop = [
    column for column in upper.columns
    if any(upper[column] > 0.95)
]

train.drop(columns=to_drop, inplace=True)
```

---

## 🔟 Interaction Features

```python
train["age_income_ratio"] = train["age"] / (train["income"] + 1)
```

Bu tür domain-based oranlar güçlü olabilir.

---

## 1️⃣1️⃣ Feature Scaling

GBDT için genelde gerekmez.
Lineer / NN için gerekir.

```python
from sklearn.preprocessing import StandardScaler

scaler = StandardScaler()
X_scaled = scaler.fit_transform(X)
```

---

## 1️⃣2️⃣ Automated Feature Engineering

Featuretools mantığı:

Otomatik agregasyon üretir.

Basit örnek:

```python
# Pseudocode mantığı
# entityset oluştur
# ilişkileri tanımla
# deep feature synthesis uygula
```

Kaggle’da genelde manuel engineering daha etkilidir.

---

# 🎯 Bölüm 6’nın Ana Mesajı

Model seçimi önemlidir.

Ama:

✔ Doğru feature
✔ Leakage’siz encoding
✔ Domain bilgisi
✔ Zaman bazlı transformasyon

yarışmayı kazandırır.

# 📘 BÖLÜM 7 – Modeling

Bu bölümün ana mesajı:

> Doğru validation + güçlü feature → doğru model seçimi.

Kaggle’da en yaygın kullanılan model ailesi:

* Gradient Boosting Decision Trees (GBDT)
* Neural Networks
* Linear Models
* Ensemble yöntemleri

---

## 1️⃣ Model Seçimi Stratejisi

Model seçimi:

* Veri tipi (tabular, text, image)
* Veri boyutu
* Feature yapısı
* Hesaplama limiti

Tabular yarışmalarda:

✔ LightGBM
✔ XGBoost
✔ CatBoost

çok yaygındır.

---

## 2️⃣ Baseline Model

Her zaman basit modelle başlanır.

```python
from sklearn.linear_model import LogisticRegression
from sklearn.metrics import roc_auc_score

model = LogisticRegression(max_iter=1000)
model.fit(X_train, y_train)

val_preds = model.predict_proba(X_val)[:, 1]
print("Validation AUC:", roc_auc_score(y_val, val_preds))
```

Amaç:

* Pipeline çalışıyor mu?
* Validation mantıklı mı?

---

## 3️⃣ Random Forest

```python
from sklearn.ensemble import RandomForestClassifier

model = RandomForestClassifier(
    n_estimators=300,
    max_depth=None,
    random_state=42,
    n_jobs=-1
)

model.fit(X_train, y_train)
```

Avantaj:

✔ Robust
✔ Feature importance verir

Dezavantaj:

❌ Büyük veride yavaş

---

## 4️⃣ Gradient Boosting – LightGBM

Tabular yarışmalarda en popüler model:

```python
import lightgbm as lgb

train_data = lgb.Dataset(X_train, label=y_train)
val_data = lgb.Dataset(X_val, label=y_val)

params = {
    "objective": "binary",
    "metric": "auc",
    "learning_rate": 0.05,
    "num_leaves": 31,
    "feature_fraction": 0.8,
    "bagging_fraction": 0.8,
    "bagging_freq": 1,
    "seed": 42
}

model = lgb.train(
    params,
    train_data,
    valid_sets=[val_data],
    num_boost_round=5000,
    callbacks=[lgb.early_stopping(200)]
)
```

Önemli parametreler:

* learning_rate
* num_leaves
* feature_fraction
* bagging_fraction

---

## 5️⃣ XGBoost

```python
import xgboost as xgb

model = xgb.XGBClassifier(
    n_estimators=1000,
    learning_rate=0.05,
    max_depth=6,
    subsample=0.8,
    colsample_bytree=0.8,
    random_state=42,
    eval_metric="auc"
)

model.fit(
    X_train, y_train,
    eval_set=[(X_val, y_val)],
    verbose=False
)
```

---

## 6️⃣ CatBoost

Kategorik feature’larda güçlüdür.

```python
from catboost import CatBoostClassifier

model = CatBoostClassifier(
    iterations=2000,
    learning_rate=0.05,
    depth=6,
    eval_metric="AUC",
    random_seed=42,
    verbose=False
)

model.fit(X_train, y_train, eval_set=(X_val, y_val))
```

Avantaj:

✔ Native categorical handling
✔ Daha az preprocessing

---

## 7️⃣ Neural Networks (Tabular)

```python
import torch
import torch.nn as nn

class SimpleNN(nn.Module):
    def __init__(self, input_dim):
        super().__init__()
        self.model = nn.Sequential(
            nn.Linear(input_dim, 128),
            nn.ReLU(),
            nn.Dropout(0.3),
            nn.Linear(128, 64),
            nn.ReLU(),
            nn.Linear(64, 1),
            nn.Sigmoid()
        )

    def forward(self, x):
        return self.model(x)

model = SimpleNN(X_train.shape[1])
```

NN’ler:

✔ Büyük veri
✔ Karmaşık feature etkileşimleri

ama küçük tabular’da GBDT genelde daha iyi.

---

## 8️⃣ Hyperparameter Tuning

Grid Search:

```python
from sklearn.model_selection import GridSearchCV

param_grid = {
    "max_depth": [4, 6, 8],
    "learning_rate": [0.01, 0.05]
}

grid = GridSearchCV(
    estimator=xgb.XGBClassifier(eval_metric="auc"),
    param_grid=param_grid,
    cv=3,
    scoring="roc_auc"
)

grid.fit(X, y)
```

Ama Kaggle’da genelde:

✔ Optuna
✔ Bayesian optimization

kullanılır.

---

## 9️⃣ Overfitting Kontrolü

Belirti:

Train AUC >> Validation AUC

Çözüm:

* Regularization artır
* Learning rate düşür
* Early stopping
* Feature azalt

---

## 🔟 Ensemble (Blending)

Birden fazla modelin ortalaması:

```python
preds1 = model1.predict_proba(X_test)[:,1]
preds2 = model2.predict_proba(X_test)[:,1]

final_preds = (preds1 + preds2) / 2
```

Basit ama güçlü.

---

## 1️⃣1️⃣ Stacking

OOF tahminlerle meta model:

```python
meta_X = np.column_stack([oof_model1, oof_model2])

from sklearn.linear_model import LogisticRegression
meta_model = LogisticRegression()
meta_model.fit(meta_X, y)

meta_test = np.column_stack([test_model1, test_model2])
final_preds = meta_model.predict_proba(meta_test)[:,1]
```

Stacking:

✔ LB artışı sağlar
✔ Riskli olabilir (validation zayıfsa)

---

## 1️⃣2️⃣ Seed Averaging

```python
seeds = [42, 2023, 777]
test_preds = np.zeros(len(X_test))

for seed in seeds:
    model = xgb.XGBClassifier(random_state=seed)
    model.fit(X, y)
    test_preds += model.predict_proba(X_test)[:,1] / len(seeds)
```

Variance azaltır.

---

## 1️⃣3️⃣ Model Seçim Hiyerarşisi (Kaggle Stratejisi)

1. Baseline model kur
2. CV sağlamlaştır
3. GBDT optimize et
4. Feature engineering derinleştir
5. Ensemble yap
6. Seed averaging uygula

---

# 🎯 Bölüm 7’nin Ana Mesajı

Kaggle’da:

✔ LightGBM / XGBoost güçlü
✔ Validation her şey
✔ Ensemble kazandırır
✔ Basit modelle başla

Model tek başına yarışma kazandırmaz.
Validation + Feature + Ensemble birlikte çalışır.

# 📘 BÖLÜM 8 – Ensembling & Advanced Techniques

Bu bölümün ana mesajı:

> Tek model nadiren kazanır.
> Kazanan çözümler genelde ensemble’dır.

Ensemble:

Birden fazla modelin çıktısını
daha güçlü bir tahmine dönüştürmektir.

---

# 1️⃣ Ensembling Neden Çalışır?

Sebep:

Bias–Variance tradeoff.

Farklı modeller:

* Farklı hata yapar
* Farklı pattern öğrenir
* Farklı overfit eder

Ortalama alınca:

✔ Variance düşer
✔ Daha stabil sonuç oluşur

---

# 2️⃣ Basit Averaging

En basit ensemble türü:

```python
pred1 = model1.predict_proba(X_test)[:, 1]
pred2 = model2.predict_proba(X_test)[:, 1]

final_preds = (pred1 + pred2) / 2
```

Avantaj:

✔ Basit
✔ Genelde işe yarar

Dezavantaj:

❌ Ağırlık optimize edilmez

---

# 3️⃣ Weighted Averaging

Daha iyi CV alan modele daha fazla ağırlık verilir.

```python
w1 = 0.6
w2 = 0.4

final_preds = w1 * pred1 + w2 * pred2
```

Ağırlık seçimi:

* CV skoruna göre
* Grid search ile
* Optimizasyon ile

---

## 3.1 Basit Weight Optimization

```python
import numpy as np
from sklearn.metrics import roc_auc_score

best_score = 0
best_weight = 0

for w in np.linspace(0, 1, 101):
    blended = w * oof1 + (1 - w) * oof2
    score = roc_auc_score(y, blended)
    
    if score > best_score:
        best_score = score
        best_weight = w

print("Best weight:", best_weight)
print("Best CV:", best_score)
```

Bu yöntem Kaggle’da sık kullanılır.

---

# 4️⃣ Rank Averaging

Özellikle farklı ölçekli modellerde kullanılır.

```python
from scipy.stats import rankdata

rank1 = rankdata(pred1)
rank2 = rankdata(pred2)

final_preds = (rank1 + rank2) / 2
```

Avantaj:

✔ Scale farklarını ortadan kaldırır
✔ Daha stabil olabilir

---

# 5️⃣ Stacking

En güçlü ensemble türlerinden biri.

Mantık:

1. Base modeller OOF üretir
2. OOF tahminler yeni feature olur
3. Meta model eğitilir

---

## 5.1 OOF Üretimi

```python
from sklearn.model_selection import KFold
import numpy as np

kf = KFold(n_splits=5, shuffle=True, random_state=42)

oof = np.zeros(len(X))
test_preds = np.zeros(len(X_test))

for train_idx, val_idx in kf.split(X):
    
    X_tr, X_val = X.iloc[train_idx], X.iloc[val_idx]
    y_tr, y_val = y.iloc[train_idx], y.iloc[val_idx]

    model = RandomForestClassifier(random_state=42)
    model.fit(X_tr, y_tr)

    oof[val_idx] = model.predict_proba(X_val)[:,1]
    test_preds += model.predict_proba(X_test)[:,1] / 5
```

---

## 5.2 Meta Model

```python
from sklearn.linear_model import LogisticRegression

meta_model = LogisticRegression()
meta_model.fit(oof.reshape(-1, 1), y)

final_preds = meta_model.predict_proba(
    test_preds.reshape(-1, 1)
)[:,1]
```

Stacking:

✔ CV artışı sağlar
✔ Validation zayıfsa tehlikelidir

---

# 6️⃣ Multi-Level Stacking

Level 1 → Level 2 → Level 3

Örnek yapı:

Model A, B, C →
Meta Model 1 →
Final Blending

Bu yapı genelde yarışma kazanan çözümlerde görülür.

---

# 7️⃣ Bagging (Seed Averaging)

Aynı model, farklı seed.

```python
seeds = [42, 2023, 777, 999]

test_preds = np.zeros(len(X_test))

for seed in seeds:
    model = RandomForestClassifier(random_state=seed)
    model.fit(X, y)
    test_preds += model.predict_proba(X_test)[:,1] / len(seeds)
```

Avantaj:

✔ Variance azaltır
✔ Genelde +0.001 – +0.01 LB artışı sağlar

---

# 8️⃣ Snapshot Ensembling (NN)

NN’de farklı epoch’lardan model kaydetme.

Mantık:

* LR cyclic schedule
* Farklı minimum noktalar
* Ortalama alma

---

# 9️⃣ Pseudo Labeling

Test verisini güvenli tahminlerle eğitime eklemek.

---

## 9.1 Basit Pseudo Label

```python
pseudo_threshold = 0.99

pseudo_data = X_test[preds > pseudo_threshold]
pseudo_labels = (preds > pseudo_threshold).astype(int)

X_aug = np.vstack([X, pseudo_data])
y_aug = np.concatenate([y, pseudo_labels])
```

Risk:

❌ Yanlış pseudo label model bozabilir

---

# 🔟 Adversarial Ensemble

Train-test distribution farkı varsa:

✔ Distribution-aware model
✔ Weighted training

---

# 1️⃣1️⃣ Model Diversity

Ensemble çalışması için:

Modellerin korelasyonu düşük olmalı.

Basit korelasyon kontrolü:

```python
np.corrcoef(oof1, oof2)
```

Korelasyon yüksekse:

→ Ensemble katkısı düşük.

---

# 1️⃣2️⃣ Overfitting Riskleri

Ensemble overfit edebilir:

* Çok fazla model
* Zayıf CV
* Aşırı weight tuning

Public LB artar
Private düşer.

---

# 1️⃣3️⃣ Grandmaster Stratejisi

Tipik kazanma pipeline:

1. Güçlü CV
2. Güçlü feature engineering
3. 3–6 farklı model
4. Seed averaging
5. Weighted blending
6. Final stacking

---

# 🎯 Bölüm 8’in Ana Mesajı

Kaggle’da:

✔ Ensemble = stabilite
✔ Diversity = güç
✔ CV sağlam değilse stacking risklidir
✔ Basit averaging bile kazandırabilir

En iyi çözümler:

Tek model değil
Model ekosistemidir.

# 📘 BÖLÜM 9 – Competition Strategy & Meta Game

Bu bölüm teknik değil, stratejiktir.

Ana mesaj:

> Kaggle sadece model yarışı değildir.
> Aynı zamanda bir strateji oyunudur.

---

# 1️⃣ Yarışma Türleri

Kaggle’da farklı yarışma tipleri vardır:

* Tabular
* NLP
* Computer Vision
* Time Series
* Reinforcement Learning
* Simulation

Strateji yarışma tipine göre değişir.

---

# 2️⃣ Yarışma Aşamaları

Tipik bir Kaggle yarışması 4 fazdan geçer:

1. Exploration (EDA)
2. Stabil CV kurma
3. Model geliştirme
4. Ensemble & Final push

---

## 2.1 İlk Hafta Stratejisi

Amaç:

✔ Veri anlamak
✔ Leakage var mı bakmak
✔ Baseline kurmak

Örnek hızlı baseline:

```python
from sklearn.dummy import DummyClassifier

dummy = DummyClassifier(strategy="most_frequent")
dummy.fit(X_train, y_train)
```

Burada amaç skor değil, pipeline doğrulama.

---

# 3️⃣ Leaderboard Dinamikleri

Public LB:

* Test verisinin küçük kısmı
* Yanıltıcı olabilir

Private LB:

* Gerçek sıralama

Risk:

Public optimizasyonu → Private çöküş.

---

## 3.1 LB Shake-up

Bazı yarışmalarda final sıralama dramatik değişir.

Sebep:

✔ Zayıf validation
✔ Distribution shift
✔ Overfitting

---

# 4️⃣ Submission Stratejisi

Çok submission yapmak risklidir.

Strateji:

* Güçlü CV olmadan submit etme
* Farklı fikirleri grupla
* Final gün panik submit yapma

---

# 5️⃣ Risk Yönetimi

Kaggle bir risk oyunudur.

Her model:

* Potansiyel kazanç
* Potansiyel çöküş

Denge kurmak gerekir.

---

# 6️⃣ Model Çeşitliliği Stratejisi

Tek model risklidir.

Portföy yaklaşımı:

Model A → düşük variance
Model B → yüksek risk, yüksek kazanç
Model C → farklı feature set

---

# 7️⃣ Public LB Overfitting Simülasyonu

```python
import numpy as np

np.random.seed(42)

true_private = 0.85
public_scores = true_private + np.random.normal(0, 0.02, 30)

best_public = np.max(public_scores)
print("En iyi Public skor:", best_public)
print("Gerçek skor:", true_private)
```

Public skor şans eseri yüksek olabilir.

---

# 8️⃣ Time Management

Uzun yarışmalarda:

* İlk ay: keşif
* Orta dönem: geliştirme
* Son hafta: stabilize

Sprint yarışmalarda:

* Hızlı baseline
* Hızlı tuning
* Erken ensemble

---

# 9️⃣ Team vs Solo

Solo:

✔ Tam kontrol
✔ Hızlı karar

Team:

✔ Model çeşitliliği
✔ Daha güçlü ensemble
✔ Daha hızlı ilerleme

---

# 🔟 Gold Medal Stratejisi

Tipik medal pipeline:

1. Güçlü CV
2. 3–5 farklı model
3. Farklı feature set
4. Seed averaging
5. Weighted blending
6. Final stacking

---

# 1️⃣1️⃣ Psychological Game

Leaderboard:

* Motivasyon kaynağı
* Aynı zamanda tuzak

Yüksek public → rehavet
Düşük public → panik

Duygusal kontrol önemlidir.

---

# 1️⃣2️⃣ Over-Engineering Tuzağı

Her zaman daha karmaşık model daha iyi değildir.

Bazen:

Basit + sağlam CV
karmaşık + zayıf CV’den iyidir.

---

# 1️⃣3️⃣ Competition Sonrası Analiz

Yarışma bitince:

✔ Kazanan çözümleri oku
✔ Validation farklarını analiz et
✔ Hangi strateji işe yaradı öğren

Bu öğrenme bir sonraki yarışmaya aktarılır.

---

# 1️⃣4️⃣ Meta Game: Kaggle Kariyeri

Kaggle:

* Portföy oluşturur
* Networking sağlar
* İş fırsatı yaratır

Sürekli öğrenen kazanır.

---

# 🎯 Bölüm 9’un Ana Mesajı

Kaggle:

✔ Teknik yarışma
✔ Strateji oyunu
✔ Risk yönetimi egzersizi
✔ Psikolojik dayanıklılık testi

Başarı:

Model + Validation + Ensemble + Strateji + Disiplin

# 📘 BÖLÜM 10 – From Kaggle to Real World (Kaggle’dan Gerçek Dünyaya)

Bu bölümün ana mesajı:

> Kaggle bir oyun alanıdır.
> Gerçek dünya ise kısıtlar dünyasıdır.

Kaggle’da:

✔ Temiz veri
✔ Sabit metrik
✔ Statik test set

Gerçek dünyada:

❌ Veri akar (streaming)
❌ Distribution değişir
❌ Gecikme (latency) önemlidir
❌ Maliyet vardır

---

# 1️⃣ Kaggle Modeli vs Production Modeli

Kaggle’da amaç:

→ Skor maksimize etmek

Production’da amaç:

→ Stabil, açıklanabilir, sürdürülebilir sistem kurmak

---

## 1.1 Basit Kaggle Pipeline

```python
model.fit(X_train, y_train)
preds = model.predict(X_test)
```

Yeterli olabilir.

---

## 1.2 Production Gereksinimi

Gerçek sistemde:

* Data validation
* Feature pipeline
* Monitoring
* Drift detection
* Logging
* Versioning

gerekir.

---

# 2️⃣ Feature Pipeline’ın Sabitlenmesi

Kaggle’da feature engineering notebook içinde yapılır.

Production’da ise:

Feature pipeline ayrı modül olur.

---

## 2.1 Basit Pipeline Örneği

```python
from sklearn.pipeline import Pipeline
from sklearn.preprocessing import StandardScaler
from sklearn.ensemble import RandomForestClassifier

pipeline = Pipeline([
    ("scaler", StandardScaler()),
    ("model", RandomForestClassifier(random_state=42))
])

pipeline.fit(X_train, y_train)
```

Pipeline:

✔ Tek noktadan inference
✔ Tek noktadan versioning

---

# 3️⃣ Model Versioning

Kaggle’da model dosyası indirilir.

Production’da:

* Model versiyonu tutulur
* Eğitim tarihi kaydedilir
* Feature set hash’i saklanır

---

## 3.1 Model Kaydetme

```python
import joblib

joblib.dump(pipeline, "model_v1.pkl")
```

Yükleme:

```python
model = joblib.load("model_v1.pkl")
```

---

# 4️⃣ Data Drift

Gerçek dünyada veri değişir.

Train dağılımı ≠ Production dağılımı

---

## 4.1 Drift Kontrolü (Basit)

```python
import numpy as np

train_mean = X_train["income"].mean()
prod_mean = X_prod["income"].mean()

print("Fark:", abs(train_mean - prod_mean))
```

Büyük fark varsa:

→ Model performansı düşebilir.

---

# 5️⃣ Monitoring

Production’da:

* Accuracy düşüyor mu?
* Prediction dağılımı değişti mi?
* Latency arttı mı?

Basit prediction dağılım kontrolü:

```python
import matplotlib.pyplot as plt

plt.hist(preds_train, alpha=0.5, label="train")
plt.hist(preds_prod, alpha=0.5, label="prod")
plt.legend()
plt.show()
```

---

# 6️⃣ Reproducibility

Kaggle’da:

Notebook snapshot yeterli olabilir.

Production’da:

✔ Seed sabit
✔ Library version sabit
✔ Deterministic pipeline

---

## 6.1 Seed Sabitleme

```python
import numpy as np
import random

np.random.seed(42)
random.seed(42)
```

NN için:

```python
import torch
torch.manual_seed(42)
```

---

# 7️⃣ Explainability

Kaggle’da genelde gerekli değildir.

Production’da:

✔ SHAP
✔ Feature importance
✔ Decision trace

---

## 7.1 SHAP Örneği

```python
import shap

explainer = shap.TreeExplainer(model)
shap_values = explainer.shap_values(X_sample)

shap.summary_plot(shap_values, X_sample)
```

Açıklanabilirlik:

* Regülasyon gereksinimi olabilir
* İş birimleri için önemlidir

---

# 8️⃣ Latency ve Maliyet

Kaggle’da:

GPU sınırsız gibi davranılır.

Production’da:

* Inference süresi kritik
* Cloud maliyeti önemlidir

Örnek:

LightGBM genelde NN’den daha hızlı inference yapar.

---

# 9️⃣ Online vs Offline Learning

Kaggle:

Offline learning (tek seferlik eğitim)

Gerçek dünya:

* Periyodik retraining
* Online learning
* Incremental update

---

# 🔟 Deployment Basit Simülasyon

Basit API mantığı:

```python
def predict(input_data):
    model = joblib.load("model_v1.pkl")
    return model.predict_proba(input_data)[:,1]
```

Gerçekte bu:

* REST API
* Batch job
* Streaming service

olabilir.

---

# 1️⃣1️⃣ Kaggle Mindset’in Avantajı

Kaggle’dan gelen biri:

✔ Hızlı prototipleme
✔ Güçlü validation
✔ Feature engineering bilgisi
✔ Ensemble mantığı

ama öğrenmesi gerekenler:

❌ System design
❌ MLOps
❌ Monitoring

---

# 1️⃣2️⃣ Kaggle Tuzakları

Kaggle alışkanlıkları production’da sorun yaratabilir:

* Aşırı karmaşık ensemble
* Overfitting’e tolerans
* Black-box model

Gerçek dünyada:

Basit + stabil genelde daha iyidir.

---

# 1️⃣3️⃣ End-to-End Mini Production Örneği

```python
# 1. Feature engineering
X_train["income_log"] = np.log1p(X_train["income"])

# 2. Model pipeline
pipeline = Pipeline([
    ("scaler", StandardScaler()),
    ("model", RandomForestClassifier(random_state=42))
])

# 3. Train
pipeline.fit(X_train, y_train)

# 4. Save
joblib.dump(pipeline, "production_model.pkl")
```

Bu basit yapı production mantığının çekirdeğidir.

---

# 🎯 Bölüm 10’un Ana Mesajı

Kaggle:

✔ Model optimizasyonu öğretir
✔ Validation disiplini öğretir
✔ Feature engineering öğretir

Ama gerçek dünya:

✔ Stabilite
✔ Monitoring
✔ Drift yönetimi
✔ Explainability
✔ Deployment

gerektirir.

# 📘 BÖLÜM 11 – Kaggle Mastery & Long-Term Growth

Bu bölüm teknik değil; ustalık (mastery) ve uzun vadeli gelişim üzerine.

Ana mesaj:

> Kaggle bir maratondur.
> Kazanmak bir yarışma değil, bir süreçtir.

---

# 1️⃣ Kaggle’da Seviye Sistemi

Kaggle’da resmi seviyeler vardır:

* Novice
* Contributor
* Expert
* Master
* Grandmaster

Bu seviyeler:

✔ Competition
✔ Dataset
✔ Notebook
✔ Discussion

kategorilerine göre ayrı ayrı verilir.

---

# 2️⃣ Medal Sistemi

Competition medals:

* Bronze
* Silver
* Gold

Genelde:

* Top ~10% → Bronze
* Top ~5% → Silver
* Top ~1% → Gold

(Oran yarışmaya göre değişebilir.)

---

# 3️⃣ Master Olma Stratejisi

Master olmak için:

✔ Süreklilik
✔ Güçlü validation
✔ Ensemble ustalığı
✔ Sabır

Tek yarışma kazanmak yeterli değildir.

---

# 4️⃣ Uzun Vadeli Öğrenme Stratejisi

En etkili öğrenme döngüsü:

1. Yarış
2. Hata yap
3. Writeup oku
4. Analiz et
5. Sonraki yarışta uygula

Bu döngü katlanarak gelişim sağlar.

---

# 5️⃣ Yarışma Seçimi Stratejisi

Her yarışmaya girilmez.

Seçim kriterleri:

✔ İlgi alanı
✔ Veri tipi uzmanlığı
✔ Süre uygunluğu
✔ Katılım yoğunluğu

---

# 6️⃣ Uzmanlaşma vs Genelcilik

İki yaklaşım vardır:

### 6.1 Uzmanlaşma

Örn:

* Sadece NLP
* Sadece tabular

Avantaj:

✔ Derin uzmanlık

---

### 6.2 Genelcilik

Farklı veri tiplerinde yarışmak.

Avantaj:

✔ Daha geniş teknik repertuar

Grandmaster’ların çoğu zamanla hibrit olur.

---

# 7️⃣ Kaggle’da Zaman Yönetimi

Çok önemli:

* Haftada sabit saat ayır
* Son hafta yoğunlaş
* Tükenmişlikten kaçın

Kaggle burnout gerçektir.

---

# 8️⃣ Notebook & Discussion Katkısı

Sadece yarışmak yeterli değildir.

Notebook paylaşmak:

✔ Tanınırlık sağlar
✔ Takım daveti getirir
✔ İş fırsatı doğurabilir

Discussion katkısı:

✔ Network oluşturur
✔ Saygınlık kazandırır

---

# 9️⃣ Teknik Derinleşme Planı

Önerilen teknik gelişim sırası:

1. Validation ustalığı
2. Feature engineering
3. GBDT tuning
4. Ensemble teknikleri
5. NN mimarileri
6. Advanced optimization

---

# 🔟 Performans Analizi Alışkanlığı

Her yarışmadan sonra analiz yapılmalı.

Örnek analiz tablosu:

```python
results = {
    "Competition": "ExampleComp",
    "CV Score": 0.842,
    "Public LB": 0.839,
    "Private LB": 0.834
}

print(results)
```

Sorulması gereken sorular:

* CV ne kadar güvenilir?
* Overfitting oldu mu?
* Ensemble katkısı neydi?

---

# 1️⃣1️⃣ Teknik Borç (Technical Debt)

Sık yapılan hata:

* Kod karmaşık
* Version kontrol yok
* Model dosyaları düzensiz

Çözüm:

✔ Modüler kod
✔ Seed kontrolü
✔ Feature listesi sabitleme

---

# 1️⃣2️⃣ Kaggle’dan Kariyere Geçiş

Kaggle:

✔ Portföy
✔ Problem çözme kanıtı
✔ Topluluk referansı

Ama iş görüşmelerinde:

* Sistem tasarımı
* Production deneyimi
* İş etkisi

sorulur.

---

# 1️⃣3️⃣ Grandmaster Zihniyeti

Grandmaster’ların ortak özellikleri:

✔ Sabırlı
✔ Analitik
✔ Risk yönetimi güçlü
✔ CV takıntılı
✔ Sürekli öğrenen

---

# 1️⃣4️⃣ Mental Dayanıklılık

Uzun yarışmalarda:

* Motivasyon düşer
* LB geriye gider
* Final shakeup olur

Uzun vadeli başarı için:

Duygusal stabilite gerekir.

---

# 1️⃣5️⃣ Mini Uzun Vadeli Gelişim Planı

Örnek 12 aylık plan:

Ay 1–3:

* Validation ustalaş
* 3 yarışmaya gir

Ay 4–6:

* Ensemble öğren
* İlk medal hedefle

Ay 7–9:

* Domain specialization
* Silver kovala

Ay 10–12:

* Advanced stacking
* Gold hedefle

---

# 🎯 Bölüm 11’in Ana Mesajı

Kaggle başarısı:

✔ Teknik bilgi
✔ Strateji
✔ Sabır
✔ Süreklilik

birleşimidir.

Tek yarışma kazanmak başarı değildir.
Sürdürülebilir performans başarıdır.

# 📘 BÖLÜM 12 – End-to-End Competition Blueprint

Bu bölüm kitabın pratiğe dökülmüş halidir.

Ana mesaj:

> Tüm öğrendiklerini sistematik bir yarışma planına dönüştür.

Bu bölüm bir “şablon” verir:

Başlangıçtan medal’a kadar sistematik ilerleme planı.

---

# 1️⃣ Yarışmaya Başlama Checklist

Başlamadan önce:

✔ Problem tipini anla
✔ Evaluation metric’i öğren
✔ Submission formatını kontrol et
✔ External data kurallarını oku

---

## 1.1 Metric’i Doğru Anlama

Yanlış metric optimizasyonu = çöküş.

Örnek: AUC

```python
from sklearn.metrics import roc_auc_score

score = roc_auc_score(y_true, y_pred)
```

Ama RMSE ise:

```python
from sklearn.metrics import mean_squared_error
import numpy as np

rmse = np.sqrt(mean_squared_error(y_true, y_pred))
```

Metric’i yanlış optimize etmek en sık yapılan hatadır.

---

# 2️⃣ İlk Gün Stratejisi

Amaç:

✔ Hızlı EDA
✔ Baseline model
✔ Basit CV

---

## 2.1 Basit Baseline

```python
from sklearn.model_selection import train_test_split
from sklearn.ensemble import RandomForestClassifier

X_train, X_val, y_train, y_val = train_test_split(
    X, y, test_size=0.2, random_state=42
)

model = RandomForestClassifier(random_state=42)
model.fit(X_train, y_train)
```

Bu aşamada skor önemli değildir.

Pipeline’ın çalışması önemlidir.

---

# 3️⃣ Güçlü Validation Kurulumu

Baseline sonrası:

✔ KFold
✔ StratifiedKFold
✔ GroupKFold
✔ TimeSeriesSplit

Örnek:

```python
from sklearn.model_selection import StratifiedKFold

skf = StratifiedKFold(n_splits=5, shuffle=True, random_state=42)
```

CV sağlam değilse hiçbir şey sağlam değildir.

---

# 4️⃣ Feature Engineering Fazı

Bu aşamada:

* Aggregation
* Target encoding
* Interaction feature
* Log transform

Örnek:

```python
train["income_log"] = np.log1p(train["income"])
train["age_income_ratio"] = train["age"] / (train["income"] + 1)
```

---

# 5️⃣ Model Geliştirme

Tipik sıralama:

1. Logistic Regression
2. Random Forest
3. LightGBM / XGBoost
4. CatBoost
5. Neural Network (gerekiyorsa)

---

## 5.1 LightGBM Örneği

```python
import lightgbm as lgb

train_data = lgb.Dataset(X_train, label=y_train)
val_data = lgb.Dataset(X_val, label=y_val)

params = {
    "objective": "binary",
    "metric": "auc",
    "learning_rate": 0.05,
    "num_leaves": 31,
    "seed": 42
}

model = lgb.train(
    params,
    train_data,
    valid_sets=[val_data],
    num_boost_round=5000,
    callbacks=[lgb.early_stopping(200)]
)
```

---

# 6️⃣ Hyperparameter Tuning

Grid search küçük projelerde yeterli olabilir.

Daha ileri seviye:

✔ Bayesian optimization
✔ Optuna
✔ Random search

---

# 7️⃣ Ensemble Fazı

CV stabil olduktan sonra:

✔ Seed averaging
✔ Weighted blending
✔ Rank averaging
✔ Stacking

---

## 7.1 Basit Blending

```python
final_preds = 0.6 * preds_model1 + 0.4 * preds_model2
```

---

# 8️⃣ Submission Disiplini

✔ Her submission kayıt altına alınmalı
✔ Hangi model + hangi seed bilinmeli
✔ CV vs LB karşılaştırılmalı

Örnek kayıt:

```python
submission_log = {
    "Model": "LGB_v3",
    "CV": 0.8421,
    "Public_LB": 0.8394
}

print(submission_log)
```

---

# 9️⃣ Final Week Stratejisi

Son hafta:

✔ Stabil modelleri koru
✔ Riskli modelleri ayrı test et
✔ Ensemble ağırlıklarını optimize et

Panik submit yapılmaz.

---

# 🔟 Yarışma Sonrası Analiz

Yapılması gerekenler:

* Kazanan çözümleri oku
* Validation farkını analiz et
* Hangi feature fark yarattı bak
* Ensemble yapısını incele

---

# 1️⃣1️⃣ Common Failure Patterns

En sık hatalar:

❌ Zayıf CV
❌ Leakage
❌ Public LB overfitting
❌ Aşırı karmaşık stacking
❌ Feature drift’i fark etmemek

---

# 1️⃣2️⃣ Tam Yarışma Mini Blueprint

1. Problem analizi
2. Baseline
3. Sağlam CV
4. Feature engineering
5. GBDT tuning
6. Seed averaging
7. Blending
8. Stacking
9. Final optimize
10. Post-analysis

Bu döngü defalarca tekrar edilir.

---

# 🎯 Bölüm 12’nin Ana Mesajı

Kaggle başarısı:

✔ Sistematik çalışma
✔ Güçlü validation
✔ Stratejik risk yönetimi
✔ Ensemble zekası

ile gelir.

Bu bölüm:

Tekniklerin nasıl bir araya getirileceğini öğretir.

# 🏆 ADVANCED KAGGLE MASTER TEMPLATE (MODULAR)

---

# 0️⃣ IMPORTS

```python
import os
import random
import warnings
warnings.filterwarnings("ignore")

import numpy as np
import pandas as pd

from sklearn.model_selection import StratifiedKFold, GroupKFold
from sklearn.metrics import roc_auc_score
from sklearn.linear_model import LogisticRegression

import lightgbm as lgb
import optuna
```

---

# 1️⃣ CONFIG (HER ŞEY BURADAN KONTROL EDİLİR)

```python
CONFIG = {

    # Core
    "target": "target",
    "n_splits": 5,
    "seeds": [42, 2023],
    "random_state": 42,

    # CV Type
    "use_group_kfold": False,
    "group_column": None,

    # Target Encoding
    "use_target_encoding": False,
    "te_columns": [],

    # Adversarial Validation
    "use_adversarial_validation": False,

    # Pseudo Labeling
    "use_pseudo_labeling": False,
    "pseudo_threshold": 0.99,

    # Optuna
    "use_optuna": False,
    "optuna_trials": 20,

    # Ensemble
    "use_rank_averaging": False,
    "use_stacking": False
}
```

---

# 2️⃣ DATA LOAD

```python
train = pd.read_csv("/kaggle/input/competition/train.csv")
test = pd.read_csv("/kaggle/input/competition/test.csv")

TARGET = CONFIG["target"]
```

---

# 3️⃣ ADVERSARIAL VALIDATION (Opsiyonel)

```python
if CONFIG["use_adversarial_validation"]:

    train["is_test"] = 0
    test["is_test"] = 1

    combined = pd.concat([train, test])
    features = [c for c in combined.columns if c not in ["is_test", TARGET]]

    adv_model = lgb.LGBMClassifier()
    adv_model.fit(combined[features], combined["is_test"])

    preds = adv_model.predict_proba(combined[features])[:,1]
    score = roc_auc_score(combined["is_test"], preds)

    print("Adversarial AUC:", score)
```

AUC > 0.7 → Distribution farkı var.

---

# 4️⃣ TARGET ENCODING (Fold İçinde)

```python
def target_encode(train, test, col, target, n_splits):

    skf = StratifiedKFold(n_splits=n_splits, shuffle=True, random_state=42)
    train_encoded = np.zeros(len(train))

    for tr_idx, val_idx in skf.split(train, train[target]):

        means = train.iloc[tr_idx].groupby(col)[target].mean()
        train_encoded[val_idx] = train.iloc[val_idx][col].map(means)

    global_mean = train[target].mean()
    train_encoded = np.where(np.isnan(train_encoded), global_mean, train_encoded)

    test_encoded = test[col].map(train.groupby(col)[target].mean())
    test_encoded = test_encoded.fillna(global_mean)

    return train_encoded, test_encoded
```

Aktifleştirme:

```python
if CONFIG["use_target_encoding"]:

    for col in CONFIG["te_columns"]:
        train[f"{col}_te"], test[f"{col}_te"] = target_encode(
            train, test, col, TARGET, CONFIG["n_splits"]
        )
```

---

# 5️⃣ CV SEÇİMİ

```python
if CONFIG["use_group_kfold"]:
    cv = GroupKFold(n_splits=CONFIG["n_splits"])
else:
    cv = StratifiedKFold(
        n_splits=CONFIG["n_splits"],
        shuffle=True,
        random_state=CONFIG["random_state"]
    )
```

---

# 6️⃣ OPTUNA (Opsiyonel)

```python
def objective(trial):

    params = {
        "objective": "binary",
        "metric": "auc",
        "learning_rate": trial.suggest_float("lr", 0.01, 0.1),
        "num_leaves": trial.suggest_int("num_leaves", 20, 200),
        "feature_fraction": trial.suggest_float("ff", 0.6, 1.0)
    }

    oof = np.zeros(len(X))

    for tr_idx, val_idx in cv.split(X, y):
        X_tr, X_val = X.iloc[tr_idx], X.iloc[val_idx]
        y_tr, y_val = y.iloc[tr_idx], y.iloc[val_idx]

        model = lgb.LGBMClassifier(**params)
        model.fit(X_tr, y_tr)

        oof[val_idx] = model.predict_proba(X_val)[:,1]

    return roc_auc_score(y, oof)
```

Aktifleştirme:

```python
if CONFIG["use_optuna"]:
    study = optuna.create_study(direction="maximize")
    study.optimize(objective, n_trials=CONFIG["optuna_trials"])
    best_params = study.best_params
else:
    best_params = {
        "objective": "binary",
        "metric": "auc",
        "learning_rate": 0.05,
        "num_leaves": 64
    }
```

---

# 7️⃣ MODEL TRAIN + OOF

```python
FEATURES = [c for c in train.columns if c not in [TARGET]]
X = train[FEATURES]
y = train[TARGET]
X_test = test[FEATURES]

oof = np.zeros(len(X))
test_preds = np.zeros(len(X_test))

for seed in CONFIG["seeds"]:

    fold_test_preds = np.zeros(len(X_test))

    for fold, (tr_idx, val_idx) in enumerate(cv.split(X, y)):

        X_tr, X_val = X.iloc[tr_idx], X.iloc[val_idx]
        y_tr, y_val = y.iloc[tr_idx], y.iloc[val_idx]

        model = lgb.LGBMClassifier(**best_params, random_state=seed)
        model.fit(X_tr, y_tr)

        oof[val_idx] += model.predict_proba(X_val)[:,1]
        fold_test_preds += model.predict_proba(X_test)[:,1]

    test_preds += fold_test_preds / CONFIG["n_splits"]

oof /= len(CONFIG["seeds"])
test_preds /= len(CONFIG["seeds"])
```

---

# 8️⃣ PSEUDO LABELING (Opsiyonel)

```python
if CONFIG["use_pseudo_labeling"]:

    confident = test_preds > CONFIG["pseudo_threshold"]

    X_aug = pd.concat([X, X_test[confident]])
    y_aug = np.concatenate([y, confident.astype(int)])

    model.fit(X_aug, y_aug)
    test_preds = model.predict_proba(X_test)[:,1]
```

---

# 9️⃣ RANK AVERAGING (Opsiyonel)

```python
from scipy.stats import rankdata

if CONFIG["use_rank_averaging"]:
    test_preds = rankdata(test_preds)
```

---

# 🔟 MULTI-LEVEL STACKING (Opsiyonel)

```python
if CONFIG["use_stacking"]:

    meta_X = oof.reshape(-1,1)
    meta_model = LogisticRegression()
    meta_model.fit(meta_X, y)

    test_preds = meta_model.predict_proba(
        test_preds.reshape(-1,1)
    )[:,1]
```

---

# 1️⃣1️⃣ FINAL SCORE

```python
print("OOF AUC:", roc_auc_score(y, oof))
```

---

# 1️⃣2️⃣ SUBMISSION

```python
submission = pd.read_csv("/kaggle/input/competition/sample_submission.csv")
submission[TARGET] = test_preds
submission.to_csv("submission.csv", index=False)
```

---

# 🏆 BU TEMPLATE’İN GÜCÜ

Bu yapı:

✔ Competition-independent
✔ Leakage-safe
✔ Fully modular
✔ Research-ready
✔ Grandmaster-level scalable

Her yarışmada:

* Sadece config değiştirirsin
* Kod sabit kalır
