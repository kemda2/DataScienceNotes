**Özet:** Kitaba göre tabular veri için en etkili yol **(1) sağlam EDA ve veri hazırlığı**, **(2) doğru doğrulama (validation) stratejisi**, **(3) güçlü temel modeller + HPO**, ve **(4) dikkatli ensembling / final retrain** adımlarını sırayla uygulamaktır. Aşağıda her tekniğin *nerede, ne zaman ve nasıl* kullanılacağıyla birlikte kısa, çalışır kod örnekleri verilmiştir.

### Karar noktaları ve öncelikler
- **Veri boyutu ve zaman:** Küçük veri → basit modeller + güçlü özellik müh.; büyük veri → ağaç tabanlı veya GPU hızlandırmalı modeller.  
- **Hedef metrik:** Yarışma metriğine göre (RMSE, AUC vb.) kayıp fonksiyonu ve post-processing seçilir.  
- **Kaynak:** Kısıtlı süre → hızlı LightGBM denemeleri; bol zaman → HPO + stacking.

---

### 1. Keşif ve veri hazırlığı (nerede / ne zaman)
**Ne için:** Hataları, eksik değerleri, dağılım kaymalarını bulmak; feature fikirleri üretmek.  
**Nasıl (örnek):**
```python
import pandas as pd
df = pd.read_csv('train.csv')
print(df.describe())
print(df.isnull().sum())
# Hedef ile korelasyon
print(df.corr()['target'].sort_values(ascending=False).head(20))
```
**İpucu:** **Özellikle dağılım kaymalarını (train/test shift)** kontrol edin; gerekirse hedefe göre gruplandırılmış istatistikler çıkarın.

---

### 2. Doğrulama stratejisi (nerede / ne zaman)
**Ne için:** Gerçek test performansını tahmin etmek; leaderboard sızmasını önlemek.  
**Nasıl:** K-fold (rastgele), zaman serisi için zaman bazlı split, grup bağımlılığı varsa GroupKFold.
```python
from sklearn.model_selection import KFold
kf = KFold(n_splits=5, shuffle=True, random_state=42)
for fold, (tr_idx, val_idx) in enumerate(kf.split(X)):
    X_tr, X_val = X.iloc[tr_idx], X.iloc[val_idx]
```
**Önemli:** **Validation stratejisi yanlışsa model yüksek leaderboard ama düşük gerçek performans verir.**

---

### 3. Modeller ve Hiperparametre optimizasyonu (nerede / ne zaman)
**Ne için:** Hızlı güçlü baz modeller kurmak; HPO ile doğrulamada en iyi parametreleri bulmak.  
**Nasıl (LightGBM + Optuna örneği):**
```python
import lightgbm as lgb
dtrain = lgb.Dataset(X_tr, label=y_tr)
params = {'objective':'regression','metric':'rmse','verbosity':-1}
model = lgb.train(params, dtrain, num_boost_round=1000, valid_sets=[dval], early_stopping_rounds=50)
```
**HPO (kısa):** Optuna ile `n_trials` sınırlı tutun; CV skorunu optimize edin.

---

### 4. Ensemble, stacking ve final retrain (nerede / ne zaman)
**Ne için:** Farklı modellerin hatalarını azaltıp son puanı yükseltmek.  
**Nasıl (basit stacking):**
```python
from sklearn.linear_model import Ridge
# level0_preds: list of OOF tahminleri
meta_X = np.column_stack(level0_oof_preds)
meta_model = Ridge()
meta_model.fit(meta_X, y)
```
**Final adımı:** En iyi modelleri **tüm veriyle yeniden eğitip** ağırlıklı ortalama veya stacking ile son tahmini üretin.

---

### Ek teknikler ve dikkat
- **Pseudo-labeling:** Testten güvenli tahminleri alıp eğitim setine ekleyin; *doğrulama ile dikkatli test edin*.  
- **Feature importance ile iterasyon:** Önemli olmayan özellikleri çıkarın; model hızlanır.  
- **Seed ve reproducibility:** Aynı sonuç için seed sabitleyin.

---

**Riskler:** Yanlış validation, veri sızıntısı, aşırı karmaşık ensemble’ların üretime alınamaması.  
**Kısa yol:** **EDA → Sağlam validation → Baseline modeller → HPO → Ensemble → Final retrain.**
